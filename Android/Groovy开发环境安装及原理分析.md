Groovy开发环境安装及原理分析
===
###（一）Groovy开发环境安装
Groovy官方提供[多种方式](http://www.groovy-lang.org/install.html)进行安装：  
Windows 下推荐 binary 包配置环境变量  
Mac、Linux、Cygwin系统采用SDKMAN方式  
直接嵌入程序推荐Maven直接引入
####Mac 安装 Groovy
打开命令行终端输入

	$ curl -s get.sdkman.io | bash
配置环境变量

	$ source "$HOME/.sdkman/bin/sdkman-init.sh"
安装 Groovy

	$ sdk install groovy
下载完成后验证

	$ groovy -version
###（二）Groovy开发入手
终端运行

	$ vim test.groovy
输入

	println "hello, world!"
保存关闭文件后终端运行

	$ groovy test.groovy
终端输出

	hello, world!
###（三）Groovy原理分析
Groovy 是基于 JVM 的脚本语言，运行时会转化为 java 的 class 字节码。  
在终端对刚刚的test.groovy 文件进行编译

	groovyc -d classes test.groovy
groovyc是groovy编译命令，-d classes 是将编译后的 class 文件放入 classes 文件夹。  
用 jd-gui 打开classes文件夹中的test.class文件。  

	public class test extends Script
	{
	  public test()
	  {
	    test this;
	    CallSite[] arrayOfCallSite = $getCallSiteArray();
	  }
	
	  public test(Binding context)
	  {
	    super(context);
	  }
	
	  public static void main(String[] args)
	  {
	    CallSite[] arrayOfCallSite = $getCallSiteArray();
	    arrayOfCallSite[0].call(InvokerHelper.class, test.class, args);
	  }
	
	  public Object run()
	  {
	    CallSite[] arrayOfCallSite = $getCallSiteArray(); return arrayOfCallSite[1].callCurrent(this, "hello.world!"); return null;
	  }
	}
从代码中可得知：  
1. test.groovy转换成继承自 Script 类的 java 类test。  
2. 运行groovy test.groovy时，会执行 main 方法。  
3. 脚本中的代码在 run 方法中，函数则被定义在 test 方法中。