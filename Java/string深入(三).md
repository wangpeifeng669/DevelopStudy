string深入（三）
===
###（一）String知识点
1. String的"+"运算符被编译器重载，底层实现采用StringBuffer或StringBuilder。  
2. String 在 java 中是采用 Unicode 格式即 UTF-16，具体可以查看官方文档 [Character](https://docs.oracle.com/javase/8/docs/api/java/lang/Character.html#unicode)。
3. 对 String 进行操作如大小写转换、连接、截取都会产生新的 String 对象，垃圾回收频繁，用StringBuffer或StringBuilder进行操作能提升性能。StringBuilder较常用，StringBuffer在StringBuilder基础上增加了同步操作现场安全。

###（二）String源码分析
####（1）String类定义
实现Comparable接口，可以对 String 进行比较，compareTo是 native 方法。
####（2）性能优化
1. 为了提升对ascii单字符的快速生成，对 ASCII 进行了初始化，方便每次快速生成。
    
       private static final char[] ASCII;
       static {
          ASCII = new char[128];
          for (int i = 0; i < ASCII.length; ++i) {
            ASCII[i] = (char) i;
          }
       }
       public static String valueOf(char value) {
          String s;
          if (value < 128) {
            s = new String(value, 1, ASCII);
          } else {
            s = new String(0, 1, new char[] { value });
          }
          s.hashCode = value;
          return s;
       }
2. hashcode 的缓存，延迟计算 hashcode 的值，每次计算完存储起来下次使用，因为 String 的不可变性所以可以这么操作。

       public int hashCode() {
        int hash = hashCode;
        if (hash == 0) {
            if (count == 0) {
                return 0;
            }
            final int end = count + offset;
            final char[] chars = value;
            for (int i = offset; i < end; ++i) {
                hash = 31*hash + chars[i];
            }
            hashCode = hash;
        }
        return hash;
       }

###（三）StringBuilder源码分析
内部实现方式是采用 char 数组存储，初始容量INITIAL_CAPACITY为16，对数据的 append 操作都是对字符串调用 native 方法System.arraycopy操作，效率较高。append 前会检测数组容量是否不够，不够进行enlargeBuffer加大容量，对原数组进行搬迁。