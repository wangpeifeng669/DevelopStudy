servlet获取数据
===

###（一）获取表单数据
getParameter：获取表单信息，如  

	request.getParameter("first_name")
###（二）获取 HTTP 头信息
通过HttpServletRequest获取 HTTP 头信息

| 函数        | 作用           |
| ------------- |-------------|
| getCookies      | 获取所有 Cookie 对象|
| getSession      | 获取当前 session 会话      |
| getHeader(String name) | 获取指定请求头值   | 
| getContentLength() | 获取主体长度   | 

###（三）修改 HTTP 响应信息
通过HttpServletResponse修改 HTTP 响应信息

| 函数        | 作用           |
| ------------- |-------------|
| addCookie      | 添加 Cookie 对象|
| setStatus      | 设置响应状态码      |
| addHeader | 添加指定请求头信息   | 
| setContentLength() | 设置主体长度   | 