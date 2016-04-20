多线程（六）happens-before介绍
===
在多线程中存在可见性问题，详见 多线程（五）互斥性和可见性，可见性遵循happens-before规则。
###（一）happens-before规则
happens-before规则：若A happens-before B，则操作A中的影响都能被B知道，影响包括修改主内存中共享变量值、发送消息、调用方法等。  
**java内存模型中的八条happens-before规则：**  
1、程序次序规则：单线程中，按照程序代码，前面的操作happens-before后面的操作。  
2、锁定规则：对于同一个锁，unlock的操作happens-before lock的操作。  
3、volatile变量规则：对volatile变量写操作happens-before读操作。  
4、传递性：如果操作A happens—before操作B，操作B happens—before操作C，那么可以得出A happens—before操作C。  
5、线程启动规则：Thread对象的start方法happens—before此线程的每一个动作。  
6、线程终止规则：线程的所有操作都happens—before对此线程的终止检测，可以通过Thread.join（）方法结束、Thread.isAlive（）的返回值等手段检测到线程已经终止执行。  
7、线程中断规则：对线程interrupt方法的调用happens—before发生于被中断线程的代码检测到中断时事件的发生。  
8、对象终结规则：一个对象的初始化完成（构造函数执行结束）happens—before它的finalize方法的开始。  

###（二）tb和hb的关系
tb（time-before）和hb(happens-before)没有直接的关系。tb指按照程序执行情况，操作在前的tb操作在后的。
####1、tb不代表hb

	/** 
	 * 多线程可见性测试 
	 *  
	 * @author peter_wang 
	 * @create-time 2015-1-12 下午3:56:29 
	 */  
	public class ThreadVisableDemo {  
	    private static int a = 0;  
	  
	    static class GetNumThread  
	        extends Thread {  
	        @Override  
	        public void run() {  
	            System.out.println(a);//B1  
	        }  
	    }  
	  
	    static class ChangeNumThread  
	        extends Thread {  
	        @Override  
	        public void run() {  
	            a = 1;//A1,A2  
	        }  
	    }  
	  
	    /** 
	     * @param args 
	     */  
	    public static void main(String[] args) {  
	        GetNumThread getNumThread = new GetNumThread();  
	        ChangeNumThread changeNumThread = new ChangeNumThread();  
	        changeNumThread.start();//C1  
	        getNumThread.start();//C2  
	    }  
	  
	}  
C1先于C2执行，存在C1 tb C2关系，但由上一篇博文可知，a不存在可见性，执行A1完数据不一定会及时刷新，B1最终输出可为0或1，C1 hb C2不存在。

####2、hb不代表tb
	a = 1；  //A1  
	b = 2;   //A2  
按照规则1，A1 hb A2，因为两段代码没存在数据关联(A1和A2互换不会影响运行结果)，JVM自动优化性能，可能存在编译代码重排序或执行指令代码重排序问题，所以可能存在A2 tb A1。