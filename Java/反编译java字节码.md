反编译java字节码
===
###（一）反编译方法
####（1）javap
jdk自带的javap就是用来反编译成java字节码的。

    public class StringDemo {

	    /**
	     * @param args
	     */
	    public static void main(String[] args) {
    		String a = "Hello";
	        String b = " 王";
	        String str = a + b + " !";
	        System.out.println(str);
	    }
    }
编译生成class文件StringDemo.class  
运行命令javap -c StringDemo.class

    public class com.peterwang.demo.StringDemo {
      public com.peterwang.demo.StringDemo();
        Code:
           0: aload_0       
           1: invokespecial #8                  // Method java/lang/Object."<init>":()V
           4: return        

      public static void main(java.lang.String[]);
        Code:
           0: ldc           #16                 // String Hello
           2: astore_1      
           3: ldc           #18                 // String  王
           5: astore_2      
           6: new           #20                 // class java/lang/StringBuilder
           9: dup           
          10: aload_1       
          11: invokestatic  #22                 // Method java/lang/String.valueOf:(Ljava/lang/Object;)Ljava/lang/String;
          14: invokespecial #28                 // Method java/lang/StringBuilder."<init>":(Ljava/lang/String;)V
          17: aload_2       
          18: invokevirtual #31                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
          21: ldc           #35                 // String  !
          23: invokevirtual #31                 // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
          26: invokevirtual #37                 // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
          29: astore_3      
          30: getstatic     #41                 // Field java/lang/System.out:Ljava/io/PrintStream;
          33: aload_3       
          34: invokevirtual #47                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
          37: return        
    }
####（2）jad命令
jad比javap更强大，还能反编译出源代码，也支持同时生成源代码和字节码。  
先下载[jad](http://varaneckas.com/jad/)，在mac平台下进入命令所在文件夹执行  
./jad StringDemo.class
生成StringDemo.jad文件，用文本打开

    // Decompiled by Jad v1.5.8g. Copyright 2001 Pavel Kouznetsov.
    // Jad home page: http://www.kpdus.com/jad.html
    // Decompiler options: packimports(3) 
    // Source File Name:   StringDemo.java

    package com.peterwang.demo;

    import java.io.PrintStream;

    public class StringDemo
    {

        public StringDemo()
        {
        }

        public static void main(String args[])
        {
            String a = "Hello";
            String b = " \u738B";
            String str = (new StringBuilder(String.valueOf(a))).append(b).append(" !").toString();
            System.out.println(str);
        }
    }
如果要同时显示字节码，执行  
./jad -a -o StringDemo.class  
生成StringDemo.jad文件，用文本打开

    // Decompiled by Jad v1.5.8g. Copyright 2001 Pavel Kouznetsov.
    // Jad home page: http://www.kpdus.com/jad.html
    // Decompiler options: packimports(3) annotate 
    // Source File Name:   StringDemo.java

    package com.peterwang.demo;

    import java.io.PrintStream;

    public class StringDemo
    {

        public StringDemo()
        {
        //    0    0:aload_0         
        //    1    1:invokespecial   #8   <Method void Object()>
        //    2    4:return          
        }

        public static void main(String args[])
        {
            String a = "Hello";
        //    0    0:ldc1            #16  <String "Hello">
        //    1    2:astore_1        
            String b = " \u738B";
        //    2    3:ldc1            #18  <String " \u738B">
        //    3    5:astore_2        
            String str = (new StringBuilder(String.valueOf(a))).append(b).append(" !").toString();
        //    4    6:new             #20  <Class StringBuilder>
        //    5    9:dup             
        //    6   10:aload_1         
        //    7   11:invokestatic    #22  <Method String String.valueOf(Object)>
        //    8   14:invokespecial   #28  <Method void StringBuilder(String)>
        //    9   17:aload_2         
        //   10   18:invokevirtual   #31  <Method StringBuilder StringBuilder.append(String)>
        //   11   21:ldc1            #35  <String " !">
        //   12   23:invokevirtual   #31  <Method StringBuilder StringBuilder.append(String)>
       //   13   26:invokevirtual   #37  <Method String StringBuilder.toString()>
        //   14   29:astore_3        
            System.out.println(str);
        //   15   30:getstatic       #41  <Field PrintStream System.out>
        //   16   33:aload_3         
        //   17   34:invokevirtual   #47  <Method void PrintStream.println(String)>
        //   18   37:return          
        }
    }

###（二）编译字节码作用
反编译jar和class查看java源码在很多情况下能帮忙更深入理解一些源代码，但反编译成java字节码能更了解一些底层编译器的工作机制。  
通过上面的反编译生成的字节码，可以看到String在JVM中是用unicode编码的，如"王"是"\u738B",String的拼接操作是通过StringBuilder实现的。