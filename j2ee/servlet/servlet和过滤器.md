servlet和过滤器
===

###（一）servlet生命周期
####1、init
servlet 初始化的时候调用，只调用一次。
####2、service
处理客户端的请求并返回客户端数据。service 方法在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。所以，不用对 service() 方法做任何动作，您只需要根据来自客户端的请求类型来重载 doGet() 或 doPost() 即可。每个客户端请求都会调用一个线程用service处理。
####3、doGet
处理客户端 get 请求。
####4、doPost
处理客户端 post 请求。
####5、destroy
servlet 销毁前调用，只调用一次。

示例：

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


###（二）Filter 过滤器
过滤器的作用：
1.在客户端的请求访问后端资源之前，拦截这些请求
2.在服务器的响应发送回客户端之前，处理这些响应
####1、Filter生命周期
过滤器是一个实现了 javax.servlet.Filter 接口的 Java 类。
#####（1）init
初始化过滤器。
#####（2）doFilter
对请求进行过滤。
#####（3）destroy
过滤器销毁前调用。

示例：拦截请求输出请求 uri  

	/**
	 * Servlet Filter implementation class HelloFilter
	 */
	@WebFilter(filterName = "HelloFilter", urlPatterns = "/*")
	public class HelloFilter implements Filter {
	
		/**
		 * Default constructor.
		 */
		public HelloFilter() {
			// TODO Auto-generated constructor stub
		}
	
		/**
		 * @see Filter#destroy()
		 */
		public void destroy() {
			System.out.println("Filter 结束");
		}
	
		/**
		 * @see Filter#doFilter(ServletRequest, ServletResponse, FilterChain)
		 */
		public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain)
				throws IOException, ServletException {
			// place your code here
			HttpServletRequest request = (HttpServletRequest) req;
			System.out.println("拦截 URI=" + request.getRequestURI());
			// pass the request along the filter chain
			chain.doFilter(request, res);
		}
	
		/**
		 * @see Filter#init(FilterConfig)
		 */
		public void init(FilterConfig fConfig) throws ServletException {
			System.out.println("Filter 初始化");
		}
	
	}

###（三） Listerner 监听
监听器Listener是application,session,request三个对象创建、销毁或者往其中添加修改删除属性时自动执行代码的功能组件。spring 中经常需要设置。  
定义方式：

	<listener>
	    <listener-class>com.listener.class</listener-class>
	</listener>