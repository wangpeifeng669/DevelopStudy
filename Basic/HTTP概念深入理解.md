HTTP概念深入理解
===

###（一）URI、URL和URN之间的区别与联系
* URI：Uniform Resource Identifier，统一资源标识符；
* URL：Uniform Resource Locator，统一资源定位符；
* URN：Uniform Resource Name，统一资源名称。
其中，URL,URN是URI的子集。
URL 必须是绝对地址，URI 可以是相对地址，如../icons/logo.gif

###（二）GET与POST区别
1. get是从服务器上获取数据，post是向服务器传送数据。
2. get是把参数数据队列加到提交表单的ACTION属性所指的URL中，值和表单内各个字段一一对应，在URL中可以看到。post是通过HTTP post机制，将表单内各个字段与其内容放置在HTML HEADER内一起传送到ACTION属性所指的URL地址。用户看不到这个过程。
3. 对于get方式，服务器端用Request.QueryString获取变量的值，对于post方式，服务器端用Request.Form获取提交的数据。
4. get传送的数据量较小，不能大于2KB。post传送的数据量较大，一般被默认为不受限制。但理论上，IIS4中最大量为80KB，IIS5中为100KB。
5. get安全性非常低，post安全性较高。但是执行效率却比Post方法好。
6. get请求参数直接放在url中作为QueryString，post请求参数是放在http body中。
建议：
1. get方式的安全性较Post方式要差些，包含机密信息的话，建议用Post数据提交方式；
2. 在做数据查询时，建议用Get方式；而在做数据添加、修改或删除时，建议用Post方式；

###（三）URL 语法
1. 协议名
http://
2. 主机与端口号
www.baidu.com:8080 无端口号默认位80
3. 用户名与密码
多用于 ftp
4. 路径
http://www.jobs-hardware.com:80/seasonal/index.html
seasonal/index.html就是路径
5. 参数
http://www.jobs-hardware.com:80/seasonal:type=1/index.html
:type=1就是参数，为服务器提供所需的参数
6. 查询字符串
http://www.jobs-hardware.com:80/seasonal/index.html?item=1
?item=1就是查询条件
7. 片段
http://www.jobs-hardware.com:80/seasonal/index.html&type=1#computer
\#computer就是片段，片段只给前端定位使用

URL 字符集是通过转义序列，用有限的 ASCII 字符集对任意字符值或数据进行编码，实现可移植性和完整性。

###（四）HTTP数据传输本质
HTTP over TCP over IP，对应应用层-》传输层-》网络层

###（五）TCP连接建立过程中为什么需要“三次握手”
client发出的第一个连接请求报文段并没有丢失，而是在某个网络结点长时间的滞留了，以致延误到连接释放以后的某个时间才到达server。本来这是一个早已失效的报文段。但server收到此失效的连接请求报文段后，就误认为是client再次发出的一个新的连接请求。于是就向client发出确认报文段，同意建立连接。假设不采用“三次握手”，那么只要server发出确认，新的连接就建立了。由于现在client并没有发出建立连接的请求，因此不会理睬server的确认，也不会向server发送数据。但server却以为新的运输连接已经建立，并一直等待client发来数据。这样，server的很多资源就白白浪费掉了。采用“三次握手”的办法可以防止上述现象发生。例如刚才那种情况，client不会向server的确认发出确认。server由于收不到确认，就知道client并没有要求建立连接。

