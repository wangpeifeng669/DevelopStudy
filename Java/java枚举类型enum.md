java枚举类型enum
===
###（一）enum 定义
[Enum](https://docs.oracle.com/javase/tutorial/java/javaOO/enum.html)，枚举类型，jdk1.5新增类型，是一组固定的常量组成合法值的类型。  
###（二）enum 使用

	/** Types of foobangs. */
	public enum FB_TYPE {
 	   GREEN, WRINKLED, SWEET, 
 	   /** special type for all types combined */
 	   ALL;
	}

	/** 
	 *Counts number of foobangs.
 	 * @param type Type of foobangs to count
 	 * @return number of foobangs of type
 	 */
	public int countFoobangs(FB_TYPE type){}  
调用方法

    int sweetFoobangCount = countFoobangs(FB_TYPE.SWEET);

###（三）enum 使用场景
枚举出所有需要处理的类型，也可以用下面的方法实现。

	private final static int GREEN    = 0;
	private final static int WRINKLED = 1;
	private final static int SWEET    = 2;
	private final static int ALL      = 3;
与 enum 的区别在于：  
1.  enum 声明更简单  
若需要处理的类型越多，普通的枚举方式写的代码量和规格都会更复杂  
2.  enum是类型安全的([type-safe](http://stackoverflow.com/questions/260626/what-is-type-safe))  
编译前就会检测类型是否正确，防止运行时异常，类似  
    
    int foo = "bar";  
如果写成  

    int sweetFoobangCount = countFoobangs(99);
则会编译出错，不会在运行时出错。  
3.  enum有自己的命名空间，防止赋值时逻辑错误，新增删除类型也不需要大量修改原有代码  
比如下面普通枚举声明
    
    private final static int GREEN    = 0;
	private final static int WRINKLED = 1;
	private final static int SWEET    = 2;
	private final static int ALL      = 3;

	private final static int DOWNLOAD_TYPE_UPGRADE       = 0;
	private final static int DOWNLOAD_TYPE_TTS           = 1;
	private final static int DOWNLOAD_TYPE_EXTRA		 = 2;
	private final static int DOWNLOAD_TYPE_GOOGLEVOICE   = 3;
赋值可以这样

    int color = GREEN；
    int color = DOWNLOAD_TYPE_UPGRADE；
    int color = 0；
若GREEN修改赋值为1，则另外两种赋值方式会导致隐性的逻辑错误。
4.  输出的信息量不足
普通的枚举声明输出是对应的值，比如GREEN输出0；枚举类型输出的是定义的名称
    
    /** Types of foobangs. */
	public enum FB_TYPE {
 	   GREEN, WRINKLED, SWEET, 
 	   /** special type for all types combined */
 	   ALL;
	}
    System.out.println(FB_TYPE.GREEN);
输出 GREEN。

总结：当定义一组同类型的常量组时，用enum枚举类型能进行编译验证，防止赋值为不合法常量，在运行时产生错误。

参考文献：  
http://crunchify.com/why-and-for-what-should-i-use-enum-java-enum-examples/  
http://stackoverflow.com/questions/4709175/what-are-enums-and-why-are-they-useful