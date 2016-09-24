Android HttpURLConnection源码分析
---
网络请求代码

	try {
            URL url = new URL("http://www.baidu.com/");
            HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
            InputStream in = new BufferedInputStream(urlConnection.getInputStream());
        } catch (Exception e) {
            e.printStackTrace();
        }
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/AndroidPro/pic/Android%20HttpURLConnection%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90.png?raw=true)
####（一）URL部分
以下分析全部基于 HTTP 请求。
#####1、构造方法
	public URL(URL context, String spec, URLStreamHandler handler){
	...
	setupStreamHandler();
	...
	streamHandler.parseURL(this, spec, schemeSpecificPartStart, spec.length());
	}
从 url 中读取请求scheme等信息，调用setupStreamHandler生成StreamHandler用来创建网络连接。
#####2、setupStreamHandler并缓存
	void setupStreamHandler() {
	...
	if (protocol.equals("file")) {
            streamHandler = new FileHandler();
        } else if (protocol.equals("ftp")) {
            streamHandler = new FtpHandler();
        } else if (protocol.equals("http")) {
            try {
                String name = "com.android.okhttp.HttpHandler";
                streamHandler = (URLStreamHandler) Class.forName(name).newInstance();
            } catch (Exception e) {
                throw new AssertionError(e);
            }
        } else if (protocol.equals("https")) {
            try {
                String name = "com.android.okhttp.HttpsHandler";
                streamHandler = (URLStreamHandler) Class.forName(name).newInstance();
            } catch (Exception e) {
                throw new AssertionError(e);
            }
        } else if (protocol.equals("jar")) {
            streamHandler = new JarHandler();
        }
        if (streamHandler != null) {
            streamHandlers.put(protocol, streamHandler);
        }
	}
根据协议生成不同的streamHandler处理对象并缓存到streamHandlers.put，因为是http所以使用com.android.okhttp.HttpHandler对象。
#####3、com.android.okhttp.HttpHandler
对 HTTP 请求的streamHandler对象就是com.android.okhttp.HttpHandler。
#####4、openConnection
创建连接代码(HttpURLConnection)url.openConnection()中

	public URLConnection openConnection() throws IOException {
        return streamHandler.openConnection(this);
    }
调用HttpHandler.openConnection。
#####5、HttpHandler.openConnection
	protected URLConnection openConnection(URL url) throws IOException {
        return newOkHttpClient(null /* proxy */).open(url);
    }
    protected OkHttpClient newOkHttpClient(Proxy proxy) {
        OkHttpClient client = new OkHttpClient();
        ...
        return client;
    }
HttpHandler[源码](http://androidxref.com/4.4_r1/xref/external/okhttp/android/main/java/com/squareup/okhttp/HttpHandler.java)中使用OkHttpClient对象的 open 方法创建URLConnection。
#####6、OkHttpClient.open(url)
	public HttpURLConnection open(URL url) {
    return open(url, proxy);
	}

    HttpURLConnection open(URL url, Proxy proxy) {
	...
    if (protocol.equals("http")) return new HttpURLConnectionImpl(url, copy);
    }
OkHttpClient[源码](http://androidxref.com/4.4_r1/xref/external/okhttp/src/main/java/com/squareup/okhttp/OkHttpClient.java)中OkHttpClient对象的 open 方法创建HttpURLConnectionImpl。
#####7、HttpURLConnectionImpl(url,  OkHttpClient)
最终(HttpURLConnection)url.openConnection()获取到的HttpURLConnection对象就是HttpURLConnectionImpl。
#####8、urlConnection.getInputStream()
	public final InputStream getInputStream() throws IOException {
	...
    HttpEngine response = getResponse();
	...
    InputStream result = response.getResponseBody();
	...
    return result;
   	}
HttpURLConnectionImpl[源码](http://androidxref.com/4.4_r1/xref/external/okhttp/src/main/java/com/squareup/okhttp/internal/http/HttpURLConnectionImpl.java)中的 getInputStream 方法调用了HttpURLConnectionImpl.getResponse请求连接并获取到数据。
#####9、HttpURLConnectionImpl.getResponse
	private HttpEngine getResponse() throws IOException {
    initHttpEngine();
    ...
    while (true) {
      if (!execute(true)) {
        continue;
      }
      ...
    }
    
    private void initHttpEngine() throws IOException {
    ...
    httpEngine = newHttpEngine(method, rawRequestHeaders, null, null);
    ...
    }
    
    private boolean execute(boolean readResponse) throws IOException {
    try {
      httpEngine.sendRequest();
      if (readResponse) {
        httpEngine.readResponse();
      }
      ...
      }
    }
initHttpEngine创建了HttpEngine对象，HttpEngine对象负责发起和获取请求，execute发起请求httpEngine.sendRequest和读取数据httpEngine.readResponse()。
#####10、HttpEngine
	public final void sendRequest() throws IOException {
	if (responseSource.requiresConnection()) {
      sendSocketRequest();
    }
	}
	
	private void sendSocketRequest() throws IOException {
	connect();
	...
	}
HttpEngine[源码](http://androidxref.com/4.4_r1/xref/external/okhttp/src/main/java/com/squareup/okhttp/internal/http/HttpEngine.java)中的 sendRequest 方法先根据 HTTP 协议的缓存机制，查看是否缓存可用，无缓存或新鲜度已过期则放起请求sendSocketRequest()，调用connect()实现 Scoket 请求，Address对象负责地址部分功能，包括 url、port、授权、ssl 连接、代理设置等。RouteSelector对象负责 dns 解析、路由寻址等。  
HttpEngine中readResponse方法读取头和主体内容，根据头的缓存配置缓存信息，根据是否设置压缩按争取方式获取主体。
