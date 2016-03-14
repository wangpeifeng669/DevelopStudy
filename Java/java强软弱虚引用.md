java强软弱虚引用
===
###（一）历史
java 软弱虚引用是JDK1.2引入的新特性，1.2之前所有对象都是强引用，只有对象可触及被引用才能使用，相当于生活中的东西，要么留着用，要么丢弃，只能二选一。从jdk1.2开始，对象分成强引用、软引用、弱引用和虚引用。
###（二）概念介绍
####（1）强引用（StrongReference）
一般的对象引用默认都是强引用，GC不会回收他，除非不再被引用。
####（2）软引用（SoftReference）
当内存不足并进行GC时，会回收。
####（3）弱引用（WeakReference）
当促发GC时，会回收。
####（4）虚引用（PhantomReference）
使用永远为空，主要是为了跟踪对象被垃圾回收器回收的活动。
####（4）引用队列（ReferenceQueue）
创建引用对象时可以设置，当对象被回收时会被放入ReferenceQueue，通过判断ReferenceQueue里面的对象，能找到被回收的对象，可以进行一些必要的回收操作。
###（三）使用场景
参考[这篇文章](http://stackoverflow.com/questions/299659/what-is-the-difference-between-a-soft-reference-and-a-weak-reference-in-java)及[这篇文章](http://www2.sys-con.com/itsg/virtualcd/java/archives/0507/shields/index.html)
####（1）弱引用
多用于只需要对原有对象进行装饰调用，不需要持有对象的情境，而且不会造成内存泄露。
#####案例一
做图片缓存时，用HashMap绑定Bitmap和url，HashMap<Bitmap,Url>，但是因为HashMap对Bitmap进行了强引用，导致Bitmap无法被回收，这时候需要手动管理内存，违背java初衷。此时使用WeakHashMap只需要保存弱引用的key，若key不再被调用则自动清除，不会强引用key这样容易导致内存泄露。
#####案例二
Android Activity中的Handler内部类，强引用了外部Activity，可以将其改成静态内部类，并弱引用外部Activity，解决内存泄露问题。

	/**
	 * handler抽象类，防止内存泄露
	 * 
	 * @author peter_wang
	 * @create-time 2013-11-14 上午11:08:12
	 */
	public abstract class WeakReferenceHandler<T>
	    extends Handler {
	    private WeakReference<T> mReference;
	
	    public WeakReferenceHandler(T reference) {
	        mReference = new WeakReference<T>(reference);
	    }
	
	    @Override
	    public void handleMessage(Message msg) {
	        if (mReference.get() == null)
	            return;
	        handleMessage(mReference.get(), msg);
	    }
	
	    protected abstract void handleMessage(T reference, Message msg);
	}

	public class AccountActivity extends BasicActivity {
	    private MyHandler mHandler = new MyHandler(this);
	
	    private static class MyHandler
	            extends WeakReferenceHandler<AccountActivity> {
	
	        public MyHandler(AccountActivity reference) {
	            super(reference);
	        }
	
	        @Override
	        protected void handleMessage(AccountActivity activity, Message msg) {
	            switch (msg.what) {
	                case HandlerControlMsg.ACCOUNT_INFO_SUCCESS:
	                break;
	
	                case HandlerControlMsg.ACCOUNT_INFO_FAIL:
	                break;
	
	                default:
	                    break;
	            }
	        }
	    }
	  }
	}
	
#####案例三
自己实现的简单WeakHashMap。

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

####（2）软引用
多用于创建耗时，生命周期较长并且不需要改变的对象，通常用来做数据缓存或者减少创造的消耗，例如数据库连接。