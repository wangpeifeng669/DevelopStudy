annovation 原理及简单自定义
===

####（一）定义
[annovation](https://en.wikipedia.org/wiki/Java_annotation) 是在 java语言层中，添加在java 源码中的[元数据](http://www.ruanyifeng.com/blog/2007/03/metadata.html)，提供文档编制、编译器检查（@override）、编译时动态处理（如动态生成代码）、运行时动态处理（如得到注解信息）等功能，大大提升开发效率。

####（二）历史
在 acnnovation 出现之前已经有类似的如@transient和@deprecated，在jdk5中正式加入annovation，利用 java 自带工具 [apt](http://docs.oracle.com/javase/6/docs/technotes/guides/apt/GettingStarted.html) 进行处理，在 jdk6中允许自定义 annovation。

####（三）类别划分
从功能上划分，分成
#####1、文档标识
如@Documented,用以标注是否在javadoc中
#####2、编译检查
如@Override 标识该方法是重写
#####3、代码分析
如@Table等自定义关键字

####（四）原理分析
Annovation 的RetentionPolicy支持三种类别：
#####1、SourceCode
在编译时就会自动去除，仅存在于源码中，如@Overrride
#####2、Class
在编译后存于 class 文件中，在执行时不再使用。在编译时刻处理的时候，是分成多趟来进行的。如果在某趟处理中产生了新的Java源文件，那么就需要另外一趟处理来处理新生成的源文件。如此往复，直到没有新文件被生成为止。在完成处理之后，再对Java代码进行编译。apt利用AbstractProcessor识别annovation操作，具体实现见[这篇文章](http://blog.csdn.net/lmj623565791/article/details/43452969)。
#####3、Runtime
在运行时被 JVM 调用，基于反射原理实现调用读取信息


####（五）自定义 Annovation
使用@interface自定义注解时，自动继承了java.lang.annotation.Annotation接口，由编译程序自动完成其他细节。
#####1、编译时使用
利用AbstractProcessor识别annovation操作，在编译时执行一些操作，生成一些文件等操作，具体实现见[这篇文章](http://blog.csdn.net/lmj623565791/article/details/43452969)。
#####2、运行时使用
用反射获取annovation 的设置，进行运行时赋值等操作
简单实现了对 Filed 和 Meathod的赋值修改，[Github地址](https://github.com/wangpeifeng669/JavaAnnotation)。