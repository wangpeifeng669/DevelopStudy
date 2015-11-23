HTTP基础理解
===

###（一）http相关定义
####（1）http 协议
HTTP超文本传输协议是一个属于应用层的面向对象的协议，同时是无状态的，所以出现了[cookie 和 session](http://blog.csdn.net/shuaishenkkk/article/details/8634917)用来保存状态数据。当使用 http 连接时，http1.0默认设置Connection: keep-alive可以不断开重复使用连接。
####（2）http 请求过程
web 前端：浏览器输入url回车，发送 request 获取到该 url 对应response 中的 html 数据，分析数据，对其中的图片、js、css 再次发送 request 请求获取，所有数据获取完毕显示出网页。
![Alt text](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Basic/pic/http%E8%AF%B7%E6%B1%82%E8%BF%87%E7%A8%8B.png)    
图中使用[charles 抓包工具](http://www.infoq.com/cn/articles/network-packet-analysis-tool-charles)。
android、ios 等app前端：通常用 get 或 post 发送 request 请求，server 处理后返回response 带 xml 或 json 格式数据，app 前端进行解析。
####（3）代理服务器
数据请求有可能经过代理服务器，才进入server。代理服务器相当于中介，既是 client 又是 server。 
代理服务器的作用：  
1. 翻墙，越过国内防火墙，访问一些被屏蔽的网站，主要原因是国内防火墙没有屏蔽这个代理服务器，而这个代理服务器又可以访问被屏蔽的网站。
2. 缓存，对部分数据可以进行缓存，减少 server 负担，提升访问速度。
3. 匿名访问，代理服务器可以代发请求而不保存原始发送点数据，较难查到请求方。
4. 进行数据分析，比如使用 Charles 进行请求数据抓取，所以请求通过 charles 后可以进行请求数据分析抓取。
5. 权限控制，通过代理服务器，可以设置部分网站无法访问，比如部分公司禁止访问淘宝等网站。

###（二）http 消息结构
####（1）request 消息
浏览器访问http://blog.csdn.net/wangpeifeng669/article/details/38957761
![Alt text](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Basic/pic/request%E6%B6%88%E6%81%AF%E7%BB%93%E6%9E%84.png) 
![Alt text](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Basic/pic/request%E6%B6%88%E6%81%AF%E5%B1%95%E7%A4%BA.png)  
1. 请求行  
Method：请求方法，如Get、Post 等  
path-to-resource：请求资源位置，如/wangpeifeng669/article/details/38957761  
version-number：http 协议版本号，通常是1.1  
2. 消息报头  
**cache 部分**  
If-Modified-Since：上次数据修改时间，和response 中的Last-Modified一起工作，在HTTP Response中获取到Last-Modified信息缓存下来， 当用户再次请求该资源时，将在HTTP Request中加入If-Modified-Since信息(Last-Modified的值)，如果服务器验证资源的Last-Modified没有改变（该资源没有更新），将返回一个304状态告诉客户端使用本地缓存文件。否则将返回200状态和新的资源和Last-Modified  
If-None-Match：一串字符串验证令牌标识数据是否修改，server在数据修改后重新生成，和response 中的ETag一起工作， 工作原理同If-Modified-Since  
If-Modified-Since和If-None-Match都是用来判断是否缓存过期，具体对比请看这篇文章  
Cache-Control：HTTP1.1中实现的缓存控制参数，控制浏览器或者其他中继缓存如何缓存某个响应以及缓存多长时间。各种取值参加这里   
Cache-Control和前两种缓存机制的区别：Cache-Control是本地判断是否过期无需请求服务器，前两种缓存过期机制是服务器判断，无过期则返回304状态，过期则返回新数据  
**client 部分**  
Accept：前端支持的数据返回格式，如Accept: text/html   代表前端支持html文档,如果server无法返回text/html类型的数据,服务器应该返回一个406错误(non acceptable)，Accept: / 代表前端可以处理所有类型  
Accept-Encoding：前端支持的编码及压缩方式（gzip，deflate），gzip 是无损压缩，deflate 不压缩，默认是压缩方式  
User-Agent：前端的信息，包括版本系统等，如User-Agent: Dalvik/1.6.0 (Linux; U; Android 4.1.1; MI 2SC MIUI/4.12.5)  
**Transport部分**  
Connection：是否保存连接，Connection: keep-alive请求完成后保持 tcp 连接，下次继续使用，Connection: close请求完成后关闭 tcp 连接，默认是keep-alive打开状态  
Host：指定被请求资源的Internet主机和端口号，必须传输的参数  
####2、response 消息
![Alt text](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Basic/pic/response%E6%B6%88%E6%81%AF%E7%BB%93%E6%9E%84.png)  
![Alt text](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Basic/pic/response%E6%B6%88%E6%81%AF%E5%B1%95%E7%A4%BA.png) 
**cache 部分**  
Date：生成消息的时间  
Expires：前端缓存失效时间，可以用Cache-Control代替，Cache-Control优先级大于Expires  
**Entity 部分**  
ETag：参考 request 中的If-None-Match  
Last-Modified：参考 request 中的If-Modified-Since  
Content-Type：返回对象和对应的字符集，如Content-Type: text/html; charset=utf-8  
Content-Length：传输数据的长度，压缩数据后的长度，在读取时需自己计算最终数据长度  
Content-Encoding：前端支持的编码及压缩方式（gzip，deflate），gzip 是无损压缩，deflate 不压缩，默认是压缩方式  
###（三）http 状态分类
| 简单四层结构    | OSI七层模型    | OSI七层模型    |
| :----:|:----:|:----:|
| 1XX    | 100-101    |信息提示    |
| 2XX    | 200-206    |请求成功    |
| 3XX    | 300-305    |请求重定向    |
| 4XX	    | 400-415    |客户端错误    |
| 5XX	    | 500-505    |服务端错误    |

常用状态：  
200 OK ：客户端请求成功  
304 Not Modified：客户端缓存资源是最新的，可以使用缓存  
400 Bad Request ：客户端请求有语法错误，不能被服务器所理解  
401 Unauthorized ：请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用  
403 Forbidden：服务器收到请求，但是拒绝提供服务  
404 Not Found：请求资源不存在，eg：输入了错误的URL  
500 Internal Server Error：服务器发生不可预期的错误  
502 Bad Gateway：网关障碍  
503 Server Unavailable：服务器当前不能处理客户端的请求，一段时间后可能恢复正常  

更专业的 http 知识请阅读[《HTTP权威指南》](https://book.douban.com/subject/10746113/)
