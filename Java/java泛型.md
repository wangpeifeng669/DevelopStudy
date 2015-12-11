java泛型
===
java 泛型是 JDK5引入的新特性，允许在定义类接口或者方法时，使用类型参数，在使用时再用具体类型替换。
###（一）历史
对于操作的类型不固定的情况，jdk5之前的处理

    public class GenericsBeforeObject {
    	private Object object;
    
    	public GenericsBeforeObject(Object object) {
    		this.object = object;
    	}
    
    	Object get() {
	    	return object;
    	}
    }
    
    GenericsBeforeObject object = new GenericsBeforeObject(new Integer(1));
	System.out.println((Integer)object.get());
使用的时候必须强制类型转换，主要是集合类中比如List，每次获取到元素要进行强制转换，而且能插入任意类型的元素，容易在运行时产生错误。  
**泛型改进版**

    public class GenericsObject<T> {
    	private T t;
    
	    public GenericsObject(T t) {
	    	this.t = t;
    	}
    
    	T get() {
    		return t;
    	}
    } 

###（二）案例
####（1）泛型类
    public class GenericsObject<T> {
    	private T t;
    
	    public GenericsObject(T t) {
	    	this.t = t;
    	}
    
    	T get() {
    		return t;
    	}
    } 
    
调用方式

    GenericsObject<Integer> object = new GenericsObject<Integer>(1);
	System.out.println(object.get());
	
####（2）泛型接口
    public interface GenericsObject<T> {
    	public T get() {}；
    } 

####（3）泛型方法
	public static <T> void get(T t){
		System.out.println(t);
	}
###（三）原理分析
####（1）类型擦除
java 泛型基本是在编译期间实现，生成的 java 字节码中不包含泛型的类别信息，在编译时会去除，这个过程称类型擦除。
比如 List<String>、List<Integer>在编译后都是 List。这种实现原理主要是源于早期 JDK 中使用 List，为了做兼容。  
**擦除前  **

    public class GenericsObject<T> {
    	private T t;
    
	    public GenericsObject(T t) {
	    	this.t = t;
    	}
    
    	T get() {
    		return t;
    	}
    } 
**擦除后**

    public class GenericsObject {
    	private Object t;
    
	    public GenericsObject(Object t) {
	    	this.t = t;
    	}
    
    	Object get() {
    		return t;
    	}
    } 


####（2）类型擦除的影响
    ArrayList<String> arrayList1=new ArrayList<String>();  
        arrayList1.add("abc");  
        ArrayList<Integer> arrayList2=new ArrayList<Integer>();  
        arrayList2.add(123);  
        System.out.println(arrayList1.getClass()==arrayList2.getClass());//true
泛型类不存在ArrayList\<String>.class，都是ArrayList.class。

	public static void method(List<String> list) {
		System.out.println("invoke method1");
	}

	public static void method(List<Integer> list) {
		System.out.println("invoke method2");
	}
比如上面的代码编译不通过，就是因为编译后两个method方法的参数都是List，在编译期间就先提示出错。

    private static void add(List<Object> list) {
		System.out.println(list.get(0));
	}
	
	List<String> list = new ArrayList<String>();
	add(list);//编译出错
上面的代码，List\<String>无法传值给List\<Object>，因为类型不同，若编译器允许传值，则在add中对list的操作都必须强制类型转换，有违设计初衷。

    public void wildcard(List<?> list) {
    list.add(1);//编译错误 
    }  
List<?>为通配符，可以传入任意List泛型如ArrayList\<String>，但不能进行add等操作，只能get读取，因为类型未知，无法插入。也可以通过上下界限制未知类型的范围，List<? extends Number>说明List中可能包含的元素类型是Number及其子类。