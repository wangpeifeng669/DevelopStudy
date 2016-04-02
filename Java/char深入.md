char深入
===
###（一）char概念
字符类型 char 占2字节16位，通常用来表示单个字符，使用16位 unicode作为编码方式，utf-16存储方式，具体见 [Character](https://docs.oracle.com/javase/8/docs/api/java/lang/Character.html#unicode)。  

###（二）三种表示形式
1. 通过单个字符指定字符常量，如'a'。
2. 通过转义字符指定特殊字符，如'\n'。
3. 用 unicode 标识字符，如'\uxxxx',xxxx代表16位整数。

###（三）char知识点
1. 字符常量表示范围为'\u0000'-'\uFFFF'，共65536个字符，其中前256个字符同 ASCII 码。  
2. 新的unicode标准表示范围为'\u0000'-'\u10FFFF','\u0000'-'\uFFFF'之间的称为Basic Multilingual Plane (BMP)，大于'\uFFFF'的是新增的补充字符集。char是2字节只能表示16位的 BMP，而4字节的 int 则可以表示全部的unicode。
3. int 的表示范围大于 char，char 只能接受0-65535的 int，在这个范围之外编译器会提示错误。
4. java中只有int有符号型 而没有 unsign integer 和 signed之分。下面的代码溢出后显示负数。

		int iValue=233;  
		//强制把一个int类型的值转换成byte类型的值  
		byte bValue=(byte) iValue;  
		//将输出-23  
		System.out.println(bValue); 