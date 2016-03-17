集合（三）Hashmap
===
###（一）哈希算法
哈希算法，将原始数据通过[散列函数](https://zh.wikipedia.org/zh/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B8)映射为较短的固定长度的二进制值，简称哈希值。  
hash算法有两个基本特点：可重复和不可逆。理论上计算出的哈希值是可重复的，但好的散列函数基本不会出现这种情况。不可逆指，知道哈希值无法推算出原始数据。  
哈希算法一般用于快速查找（如HashMap）和加密算法（如[MD5](http://baike.baidu.com/view/7636.htm?fr=aladdin)）。

###（二）java中的hashcode
java中对象基类Object有hashcode方法，native调用默认返回对象内存地址，hashcode返回不定长的10进制数。  
Java对于eqauls方法和hashCode方法是这样规定的：  
1、如果两个对象相同（equal），那么它们的hashCode值一定要相同；2、如果两个对象的hashCode相同，它们并不一定相同。 
hashcode可以重写，String重写了hashcode。

	public int hashCode() {  
	    int h = hash;  
	        int len = count;  
	    if (h == 0 && len > 0) {  
	        int off = offset;  
	        char val[] = value;  
	  
	            for (int i = 0; i < len; i++) {  
	                h = 31*h + val[off++];  
	            }  
	            hash = h;  
	        }  
	        return h;  
	    }  
s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]   
使用 int 算法，这里 s[i] 是字符串的第 i 个字符，n 是字符串的长度，^ 表示求幂。（空字符串的哈希码为 0。）
###（三）hashmap源码分析
数据格式：数组和链表结合的拉链法哈希表。
![image](http://img.blog.csdn.net/20140922213505461?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ3BlaWZlbmc2Njk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
###（四）简单实现WeakHashmap

	/**
	 * 仿造实现WeakHashMap
	 *
	 * @author peter_wang
	 * @create-time 16/3/14 09:46
	 */
	public class WeakHashMapToy<K, V> {
	    /**
	     * 默认的{@link WeakHashMapToy#mEntryArray}长度
	     */
	    private static final int DEFAULT_SIZE = 16;
	    /**
	     * key被回收时对应value被放入的队列，方便清除操作
	     */
	    private ReferenceQueue<K> mReferenceQueue;
	
	    private Entry<K, V>[] mEntryArray;
	
	    public WeakHashMapToy() {
	        mReferenceQueue = new ReferenceQueue<>();
	        mEntryArray = new Entry[DEFAULT_SIZE];
	    }
	
	    /**
	     * 获取对应key的hash值，用来计算存储位置
	     *
	     * @param key 需要存储的key
	     * @return key的hash 值
	     */
	    private static int getKeyHash(Object key) {
	        return key.hashCode();
	    }
	
	    public V get(K key) {
	        poll();
	
	        int index = getKeyHash(key) % mEntryArray.length;
	        if (mEntryArray[index] == null) {
	            return null;
	        }
	        Entry<K, V> entry = mEntryArray[index];
	        while (entry != null && entry.get() != null) {
	            if (entry.get().equals(key)) {
	                return entry.mValue;
	            } else {
	                entry = entry.mNext;
	            }
	        }
	        return null;
	    }
	
	    public void put(K key, V value) {
	        poll();
	
	        int index = getKeyHash(key) % mEntryArray.length;
	        if (mEntryArray[index] == null) {
	            mEntryArray[index] = new Entry<>(key, value, mReferenceQueue);
	        } else {
	            Entry<K, V> entry = mEntryArray[index];
	            Entry<K, V> beforeEntry = entry;
	            //查找是否存在对应的key
	            while (entry != null) {
	                if (key.equals(entry.get())) {
	                    entry.mValue = value;
	                    return;
	                } else {
	                    beforeEntry = entry;
	                    entry = entry.mNext;
	                }
	            }
	
	            //查找失败创建新的item放在链表最后
	            beforeEntry.mNext = new Entry<>(key, value, mReferenceQueue);
	        }
	    }
	
	    /**
	     * 移除已被回收的
	     */
	    private void poll() {
	        Entry<K, V> mRemoveEntry = (Entry<K, V>) mReferenceQueue.poll();
	        while (mRemoveEntry != null) {
	            removeItem(mRemoveEntry);
	            mRemoveEntry = (Entry<K, V>) mReferenceQueue.poll();
	        }
	    }
	
	    /**
	     * 移除指定元素
	     *
	     * @param removeEntry 要移除的元素
	     */
	    private void removeItem(Entry<K, V> removeEntry) {
	        int index = removeEntry.mHash % mEntryArray.length;
	        Entry<K, V> entry, beforeEntry = null;
	        entry = mEntryArray[index];
	        while (entry != null) {
	            if (entry == removeEntry) {
	                if (beforeEntry == null) {
	                    mEntryArray[index] = entry.mNext;
	                } else {
	                    beforeEntry.mNext = entry.mNext;
	                }
	            } else {
	                beforeEntry = entry;
	                entry = entry.mNext;
	            }
	        }
	    }
	
	    private static final class Entry<K, V> extends WeakReference<K> {
	        /**
	         * 保存key的hash值，用来key被回收时，也能找到存储的位置
	         */
	        private int mHash;
	
	        private V mValue;
	        /**
	         * 链表结构，指向下个元素
	         */
	        private Entry<K, V> mNext;
	
	        public Entry(K key, V value, ReferenceQueue<K> queue) {
	            super(key, queue);
	            mHash = getKeyHash(key);
	            mValue = value;
	        }
	    }
	}
###（五）Hashmap知识点
1. HashMap 初始化有个threshold代表容量警示线，每次put元素进入前会判断，超过则要双倍扩容doubleCapacity，一般相当于总容量的3/4。
2. HashMap 是非线程安全的，如果使用线程安全的HashMap使用Hashtable、ConcurrentHashMap或Collections.synchronizedMap。  
   更多 HashMap 线程非安全分析看[这篇文章](http://coolshell.cn/articles/9606.html)和[这篇文章](http://my.oschina.net/xianggao/blog/393990)。  
   本质上是因为扩容过程中进行多线程操作，元素搬迁重组中容易引起死锁。
3. 获取 key 存放位置采用  
   int hash = Collections.secondaryHash(key);  
   不直接使用 key 的 hashcode，而是 hashcode 后再进行分散处理，防止元素过于集中。
4. 使用modCount表示每次数据改动，防止在调用HashIterator遍历元素的时候多线程操作又改动了数据。