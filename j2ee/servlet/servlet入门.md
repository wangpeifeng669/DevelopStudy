servlet入门
===

###（一）servlet简介
Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。  

servlet 架构和作用如下
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/servlet/servlet%E7%AE%80%E4%BB%8B_1.jpg?raw=true)  

最主要的是servlet 和 filter 这两项技术，一个实现对请求和返回数据的处理，一个实现请求和返回的过滤。大部分 web 框架都是基于servlet，学好这个知识点，可以为熟悉框架底层做铺垫。

###（二）servlet 简单 demo
在本地安装完 jdk、eclipse for EE、tomcat 之后，就可以通过 eclipse 创建 servlet。  
先创建一个动态 web 项目
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/servlet/servlet%E7%AE%80%E4%BB%8B_2.png?raw=true)  
再建 servlet
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/servlet/servlet%E7%AE%80%E4%BB%8B_3.png?raw=true)
代码如下：

	/**
	 * Servlet implementation class HelloWorld
	 */
	@WebServlet("/HelloWorld")
	public class HelloWorld extends HttpServlet {
		private static final long serialVersionUID = 1L;
	       
	    /**
	     * @see HttpServlet#HttpServlet()
	     */
	    public HelloWorld() {
	        super();
	    }
	
		/**
		 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
		 */
		protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			PrintWriter out = response.getWriter();
			out.println("Hello, World!");
		}
	
		/**
		 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
		 */
		protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			doGet(request, response);
		}
	
	}
运行 servlet，右键servlet文件选择 run on server->tomcat 运行
![image](https://github.com/wangpeifeng669/DevelopStudy/blob/master/j2ee/pic/servlet/servlet%E7%AE%80%E4%BB%8B_4.png?raw=true)