集合（六）Queue
===
###（一）Iterable
获取Iterator对象，提供遍历删除功能，是除了 Map 之类的集合类的基类，HashMap通过keySet方法也能获取key的Set对象进行遍历，通过entrySet方法就能获取 value 的Set对象。

###（二）Collection
集合基础操作，增删清除及容量。

###（三）Queue
1. FIFO 链表，新增插入移除功能，也支持查找或者直接出栈操作。
2. add和 offer 的区别：当无法插入链表时，add 抛出异常，offer 返回 boolean 值。移除函数remove和poll类似。获取但不删除函数element和peek也类似。

###（四）Deque
双向队列，操作同Queue类似，只是分成对链表头或者尾的操作。如Queue中的add分化为Deque中的addFirst和addLast。

###（五）