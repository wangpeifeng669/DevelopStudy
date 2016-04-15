多线程（三） Thread 知识点
===
###（一）Thread 和 Runnable 的区别
1. Thread 是类，Runnable 是接口，java 不支持多继承，继承Thread则不能再继承其他类，如果不需要修改Thread中的功能，可以直接使用Runnable更灵活。  
2. Runnable代表需要执行的任务，可以被 Thread 、Executors线程池等执行，逻辑上分离出来，设计更优化。
3. Runnable任务可以被多次调用，而 Thread 不可以被重复调用。

###（二）Thread优先级
java 线程的优先级从1-10，数字越大任务越紧急，默认为5。JVM 标准优先调度优先级较高的线程，但是一方面优先级处理是随机的取决于底层调度策略，另一方面有些底层系统没有那么多优先级，所以不能完全依赖线程优先级控制线程的状态。

###（三）ThreadGroup
每个线程都对应一个线程组，创建时可以指定放置的线程组，默认情况下跟启动线程的线程同一组。  
线程组和线程池是两个不同的概念，他们的作用完全不同，前者是为了方便线程的管理，后者是为了管理线程的生命周期，复用线程，减少创建销毁线程的开销。  
在<<Effective Java>>中不建议使用ThreadGroup，因为很多方法没考虑线程安全，java jdk已废除ThreadGroup。

###（四）ThreadLocal
线程局部变量，线程内共享，线程间互斥，为每个线程保存变量的副本。

	public class ThreadLocalDemo implements Runnable {
		private ThreadLocal<Integer> mTlNum = new ThreadLocal<Integer>();
	
		/**
		 * @param args
		 */
		public static void main(String[] args) {
			ThreadLocalDemo demo = new ThreadLocalDemo();
			Thread thread1 = new Thread(demo, "first thread");
			thread1.start();
			Thread thread2 = new Thread(demo, "second thread");
			thread2.start();
		}
	
		public void run() {
			accessStudent();
		}
	
		private void accessStudent() {
			String currentThreadName = Thread.currentThread().getName();
			System.out.println(Thread.currentThread().getName() + " is running");
			Random random = new Random();
			int randomNum = random.nextInt(100);
			System.out.println("random num:" + randomNum);
			Integer num = getNum();
			num = randomNum;
			System.out.println(currentThreadName + " first thread age is:" + num);
			try {
				Thread.sleep(500);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(currentThreadName + " second thread age is:" + num);
		}
	
		private Integer getNum() {
			Integer num = mTlNum.get();
			if (num == null) {
				num = new Integer(0);
				mTlNum.set(num);
			}
			return num;
		}
	}
	first thread is running
	second thread is running
	random num:49
	random num:32
	first thread first thread age is:49
	second thread first thread age is:32
	first thread second thread age is:49
	second thread second thread age is:32
同一个线程中的变量保存不变，不会受其他线程影响。  
ThreadLocal使用场合主要解决多线程中数据数据因并发产生不一致问题。ThreadLocal为每个线程的中并发访问的数据提供一个副本，通过访问副本来运行业务，这样的结果是耗费了内存，单大大减少了线程同步所带来性能消耗，也减少了线程并发控制的复杂度。

###（五）Thread使用注意
####（1）Thread类只能启动一次
Thread类只能 start 调用一次，再次调用会抛出IllegalThreadStateException异常。
####（2）Thread类stop等函数已不建议使用
线程任务停止运行只能借助 boolean 值设置，在线程任务内自省判断来自己停止。