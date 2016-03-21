集合（四）ArrayList和LinkedList
===
###（一）ArrayList
1. 数组结构存放元素，插入前如果容量不够，进行数据搬迁和自动扩容。
2. System.arraycopy迁移数组调用natvie高效处理。
3. 使用modCount表示每次数据改动，防止在调用ArrayListIterator遍历元素的时候多线程操作又改动了数据。
4. contains判断是否已存在某元素，是判断equals，如果存放元素的 equal 相同，则判断是否包含的时候会返回 true。同样 remove 对象的时候也是判断 equal，这点和 hashmap 不同，hashmap 还判断 hash 值。

###（二）LinkedList
1. 双向链表存放元素，起始状态只有一个头元素，头尾指针指向自身，add 新增元素存放后面。  
数据结构

    	private static final class Link<ET> {
	        ET data;
	
	        Link<ET> previous, next;
	
	        Link(ET o, Link<ET> p, Link<ET> n) {
	            data = o;
	            previous = p;
	            next = n;
	        }
	    }
   
2. modCount使用方式同ArrayList。
3. 头元素不是放入的元素，用size变量记录整个链表的数据量。
4. contains和 remove 同 ArrayList，用 equal 判断。


###（三）ArrayList和LinkedList对比
####（1）时间复杂度不同
| 对比      | ArrayList           | LinkedList  |
| ------------- |:-------------:| -----:|
| get(int index)      | O(1) | O(n) |
| add(E element)      | O(1)、O(n)(扩容时)      |   O(1) |
| add(int index, E element) | O(n)      |    O(n-index) |
| remove(int index)      | O(n-index) | O(n) |
| get(int index)      | O(1) | O(n) |  


####（2）使用场景
**ArrayList**：使用范围更广，随机访问效率高，频繁过多插入会导致多次扩容降低效率。
**LinkedList**：按顺序插入及删除效率高，初始化时带入原始数据效率高。

####（3）使用要点
1. 当你有大量的数据存储时，LinkedList数据结构多了头尾指针，存储量大，但是ArrayList数组经过扩容，容量大于实际存放的容量。
2. ArrayList初始容量为10，后续数据再多才扩容，如果预先知道数据量，可以初始化初始容量。
3. ArrayList在大多数情况下性能较优，通常情况下优先选择ArrayList。
