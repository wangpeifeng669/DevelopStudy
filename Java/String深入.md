String深入
===
###（一）基本概念理解
**堆**：存储对象等数据的区域。  
**栈**：保存线程执行的行为和数据引用。  
**常量池**：在堆中分配出来，专门存储不变量如String、float或者Integer。
###（二）String编码
java文件可以是任何编码保存，但是编译后都是unicode格式的class文件，在操作IO如文件、数据库、网络数据时，都需要进行编码转换。

###（三）String案例分析
####（1）案例1
	/**
	 * 测试常量池
	 * 首先查看常量池中是否存在"abc"，存在则s1直接引用，
	 * 不存在则在常量池中创建"abc"，s1再引用该地址；
	 * s2同s1,两者引用同一地址
	 */
	private static void test1(){
		 String s1 = "abc";
		 String s2 = "abc";
		 System.out.println(s1==s2);
	}
####（2）案例2

	/**
	 * 测试new String("");
	 * 首先在堆中分配内存new String并用栈中s1引用，
	 * 查看常量池中是否存在"abc"，存在则堆直接引用，
	 * 不存在则在常量池中创建"abc"，再引用该地址；
	 * s2同s1,也需要在堆中分配内存new String，所以s1s2引用地址堆不同。
	 */
	private static void test2(){
		 String s1 = new String("abc");
		 String s2 = new String("abc");
		 System.out.println(s1==s2);
	}
用jad反编译字节码查看，jad具体用法见先前的文章。

    private static void test2()
    {
        String s1 = new String("abc");
    //    0    0:new             #21  <Class String>
    //    1    3:dup             
    //    2    4:ldc1            #23  <String "abc">
    //    3    6:invokespecial   #25  <Method void String(String)>
    //    4    9:astore_0        
        String s2 = new String("abc");
    //    5   10:new             #21  <Class String>
    //    6   13:dup             
    //    7   14:ldc1            #23  <String "abc">
    //    8   16:invokespecial   #25  <Method void String(String)>
    //    9   19:astore_1        
        System.out.println(s1 == s2);
    //   10   20:getstatic       #28  <Field PrintStream System.out>
    //   11   23:aload_0         
    //   12   24:aload_1         
    //   13   25:if_acmpne       32
    //   14   28:iconst_1        
    //   15   29:goto            33
    //   16   32:iconst_0        
    //   17   33:invokevirtual   #34  <Method void PrintStream.println(boolean)>
    //   18   36:return          
    }
####（3）案例3
	/**
	 * 测试字符串常量编译优化
	 * 编译时查看到"abc"和"d"都是不变常量，会进行串连保存成"abcd"
	 */
	private static void test3(){
		 String s1 = "abc"+"d";
		 System.out.println(s1);
	}
反编译字节码如下

    private static void test3()
    {
        String s1 = "abcd";
    //    0    0:ldc1            #21  <String "abcd">
    //    1    2:astore_0        
        System.out.println(s1);
    //    2    3:getstatic       #23  <Field PrintStream System.out>
    //    3    6:aload_0         
    //    4    7:invokevirtual   #29  <Method void PrintStream.println(String)>
    //    5   10:return          
    }
####（4）案例4
	/**
	 * 测试字符串"+"拼接
	 * 字符串"+"拼接本质上是利用StringBuilder实现，
	 * 每执行一组拼接，需生成一个StringBuilder对象，
	 * 最后再调用StringBuilder.toString生成String对象
	 */
	private static void test4() {
		String s1 = "abc";
		String s2 = "abc";
		String s3 = s1 + s2 + "d";
		System.out.println(s3);
	}
反编译字节码如下

    private static void test4()
    {
        String s1 = "abc";
    //    0    0:ldc1            #21  <String "abc">
    //    1    2:astore_0        
        String s2 = "abc";
    //    2    3:ldc1            #21  <String "abc">
    //    3    5:astore_1        
        String s3 = (new StringBuilder(String.valueOf(s1))).append(s2).append("d").toString();
    //    4    6:new             #23  <Class StringBuilder>
    //    5    9:dup             
    //    6   10:aload_0         
    //    7   11:invokestatic    #25  <Method String String.valueOf(Object)>
    //    8   14:invokespecial   #31  <Method void StringBuilder(String)>
    //    9   17:aload_1         
    //   10   18:invokevirtual   #34  <Method StringBuilder StringBuilder.append(String)>
    //   11   21:ldc1            #38  <String "d">
    //   12   23:invokevirtual   #34  <Method StringBuilder StringBuilder.append(String)>
    //   13   26:invokevirtual   #40  <Method String StringBuilder.toString()>
    //   14   29:astore_2        
        System.out.println(s3);
    //   15   30:getstatic       #44  <Field PrintStream System.out>
    //   16   33:aload_2         
    //   17   34:invokevirtual   #50  <Method void PrintStream.println(String)>
    //   18   37:return          
    }