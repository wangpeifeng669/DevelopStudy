hashcode 的作用
===

对象基类 Object 中都有 hashcode 和 equal 方法，hashcode 主要应用于集合中做匹配对象是否相等。
在 Hashtable、HashSet、hashMap中使用hashcode做对象是否相等的匹配，查看[Hashtable 源码分析](http://www.cnblogs.com/skywang12345/p/3310887.html)，其数据结构是数组和链表结合的拉链法哈希表，通过hashcode 计算出对象存放的位置，若位置已有其他对象，则调用 equal 方法匹配是否相同，相同忽略插入，不同则建立链表顺序插入。如果没有这种机制，每次插入对象都必须匹配全部对象，效率极低。
ps：Set集合 (不允许放重复值)是基于Hashmap 实现。

![](https://github.com/wangpeifeng669/DevelopStudy/blob/master/Java/pic/hashcode%E7%9A%84%E4%BD%9C%E7%94%A8_pic.png?raw=true)

在使用过程中需要遵循规则：
1、如果两个对象相同（equal），那么它们的hashCode值一定要相同；
2、如果两个对象的hashCode相同，它们并不一定相同。

默认情况下，Object中的hashCode() 返回对象的32位jvm内存地址。 
在 String中重写了 hashcode 方法，算法s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]

    public int hashCode() {  
    int h = hash;  
    if (h == 0) {  
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
    
之所以选择31，是因为它是个奇素数，如果乘数是偶数，并且乘法溢出的话，信息就会丢失，因为与2相乘等价于移位运算。使用素数的好处并不是很明显，但是习惯上都使用素数来计算散列结果。31有个很好的特性，就是用移位和减法来代替乘法，可以得到更好的性能：31*i==(i<<5)-i。总之就是31是最佳素数，能提高分离计算性能。
