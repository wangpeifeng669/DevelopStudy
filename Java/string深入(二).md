string深入（二）
===
###（一）Hashcoce 算法分析
	public int hashCode() {  
	        int h = hash;  
	        if (h == 0 && count > 0) {  
	            int off = offset;  
	            char val[] = value;  
	            int len = count;  
	  
	  
	            for (int i = 0; i < len; i++) {  
	                h = 31*h + val[off++];  
	            }  
	            hash = h;  
	        }  
	        return h;  
	    } 
**算法如下所示：  **  
s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]   

**计算一下cat的hashcode值：  **  
c，a，t 的ascii码值分别为99,97,116；字符个数3  
总共3次循环  
第一次循环：h1=31*0+val[0]=val[0]='c'=99  
第二次循环：h2=31*h1+val[1]=31*99+'a'=31*99+97=3166  
第三次循环：h3=31*h2+val[2]=31*3166+'t'=31*3166+116=98262  
所以cat的hashcode值为98262。  

**使用数字31的原因**  
[Effective Java](http://book.douban.com/subject/3360807/)书提及：The value 31 was chosen because it is an odd prime. If it were even and the multiplication overflowed, information would be lost, as multiplication by 2 is equivalent to shifting. The advantage of using a prime is less clear, but it is traditional. A nice property of 31 is that the multiplication can be replaced by a shift and a subtraction for better performance: 31 * i == (i << 5) - i. Modern VMs do this sort of optimization automatically.之所以选择31是因为它是一个奇素数。如果是偶数，则与2相乘等价于移位元素，溢出信息就会丢失。素数的好处不明显，只是习惯用素数计算散列。31还有个很好的特性，即用移位和减法代替乘法，有更好的性能，31*i==(i<<5)-i，JVM 会自动优化。

**因为 String 不可变，对 hashcode 的计算进行了 lazy load，计算完缓存到了 hash 变量，可以重复使用。**  
###（二）String 设置成 Final 不可变的原因
####（1）String常量池
String使用频率较大，设计者开辟了专门的 String对象池进行字符串共享使用，避免过多创建 String 对象，而String Pool 必须使 String 不可变。如  

	String s1 = "Java";  
	String s2 = "Java";
如果 String 是可变的，将"Java"对象变成"C++"，则 s2也会变成"C++"，导致混乱。
####（2）安全性
String 的广泛使用如URL请求、操作本地文件，不可变能确保安全性，防止在操作过程中突然改变路径。多线程使用中，String 变量较常见，设置为不可变不需要考虑多线程问题。
####（3）性能优化
集合类如 HashMap 等经常用 String 作为 key，进行操作过程中经常需要计算 hashcode，在第一部分Hashcoce 算法分析中已经说明，String的不可变可以对 hashcode 进行缓存，提升性能。

###（三）String的不可变性剖析
	String str = new String();
	
	str  = "Hello";
	System.out.println(str);  //Prints Hello
	
	str = "Help!";
	System.out.println(str);  //Prints Help!
str是指针，"Hello"和"Help!"是两个 String 对象，指针可以改变，String对象不可以改变。String对象存放在String常量池中，不可以被改动，除非使用反射机制，但是滥用反射是很危险的。

    String s="aaa";  
    String str="aaa";  
    System.out.println(s.hashCode()+"/"+str.hashCode());  
    Field f=str.getClass().getDeclaredField("value");  
    f.setAccessible(true);  
    f.set(str,new char[]{'b','b','b','b'});  
    System.out.println(str);  
    System.out.println(s+"/"+str);  
    System.out.println(s.hashCode()+"/"+str.hashCode()); 
    输出结果：
    96321/96321 
	bbb 
	bbb/bbb 
	96321/96321
###（四）String知识点
1. String的"+"运算符被编译器重载，底层实现采用StringBuffer或StringBuilder。  
2. String 在 java 中是采用 Unicode 格式即 UTF-16，具体可以查看官方文档 [Character](https://docs.oracle.com/javase/8/docs/api/java/lang/Character.html#unicode)。
3. 对 String 进行操作如大小写转换、连接、截取都会产生新的 String 对象，垃圾回收频繁，用StringBuffer或StringBuilder进行操作能提升性能。StringBuilder较常用，StringBuffer在StringBuilder基础上增加了同步操作现场安全。