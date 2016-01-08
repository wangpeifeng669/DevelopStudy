AsyncTask和Volley对比
===
网络连接这块，google 推行使用 Volley 框架，不过在看别人的代码时，依然看到有人用AsyncTask，不得不疑惑，使用是否该使用 Volley 框架、使用 Volley 框架是否能加快开发提升性能。下面对比下二者的区别。

###AsyncTask

AsyncTask在网络访问数据时，能提供异地加载作用，防止页面卡住，简单使用如下

    class GetDataTask extends AsyncTask<String, Void, String> {
            @Override
            protected void onPreExecute() {
                super.onPreExecute();
            }

            @Override
            protected void onPostExecute(String s) {
                super.onPostExecute(s);
            }

            @Override
            protected String doInBackground(String... params) {
                return null;
            }
        }
在doInBackground中自己实现网络请求及传递获取到的数据。  
阅读AsyncTask源码可知，其中使用了线程池统一管理所有的网络请求。  

