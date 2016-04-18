多线程（四） JMM(java memory model)
===
###（一）java内存区域概况
jvm运行java程序时把所管理的内存分成几个部分：方法区、java栈、本地方法栈、java堆、pc程序计数器。  
class字节码装载解析后，在多线程环境中，方法区和java堆数据共享，每个线程自带pc程序计数器和java栈，栈帧中包含方法的所有状态（局部变量、传参、返回值、运算中间结果等）。对共享数据需要考虑多线程并发问题。  
更详细内容可参考[《深入理解JVM虚拟机》](https://book.douban.com/subject/24722612/)。

###（二）计算机系统原型
1.内存和处理器速度不同，缓存的出现，导致数据不同步  
多线程都有缓存备份，回写可能会导致数据错误  
2.处理器和编译器对代码和指令的优化，导致运算乱序  
只要不影响运算结果，处理器可以调整代码执行顺序，多线程操作中会导致数据错误

###（三）JMM概况
JMM（java momery model）提供了线程进行通讯时，确保互斥性和可见性。
####（1）解决内存可见性问题
![image](http://img.blog.csdn.net/20150108141719394?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ3BlaWZlbmc2Njk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
共享数据放主内存中，每个线程有自己的工作内存。  
线程A读取数据时，先从主内存拷贝数据到工作内存，进行运算后再从工作内存回写到主内存，线程间无法相互访问对方的数据，通过主内存实现通讯。  
JMM可以控制指定共享数据实现内存可见性，写入主内存时，同步更新其他线程中的变量值，如volatile变量的使用。
####（2）禁止重排序
编译器优化代码，不改变运算结果的情况下，可以实现代码重排序；处理器执行指令，不改变运算结果的情况下，可以实现指令重排序。  
JMM可以禁止编译器代码重排序和处理器执行指令重排序（通过加入内层屏蔽）。  
多线程并发问题代码剖析  

	/** 
	 * 代码重排序demo 
	 *  
	 * @author peter_wang 
	 * @create-time 2015-1-8 下午3:06:02 
	 */  
	public class CodeChangeDemo {  
	  
	    private static boolean isChange = false;  
	  
	    private static class ChangeThread  
	        extends Thread {  
	        public void run() {  
	            System.out.println("ChangeThread start"); //1  
	            isChange = true;                          //2  
	        };  
	    };  
	  
	    private static class GetChangeThread  
	        extends Thread {  
	        public void run() {  
	            System.out.println("GetChangeThread start");  
	            if (isChange) {                           //3  
	                isChange = false;  
	                System.out.println("change success");  
	            }  
	            else {  
	                System.out.println("change no success");  
	                System.exit(0);  
	            }  
	        };  
	    };  
	  
	    /** 
	     * @param args 
	     */  
	    public static void main(String[] args) {  
	        while (true) {  
	            ChangeThread changeThread = new ChangeThread();  
	            GetChangeThread getChangeThread = new GetChangeThread();  
	            changeThread.start();  
	            getChangeThread.start();  
	            // 防止线程过多，等执行完上面俩线程再继续  
	            while (Thread.activeCount() > 1) {  
	  
	            }  
	        }  
	    }  
	  
	}  
运行结果：

	ChangeThread start
	GetChangeThread start
	change success
	ChangeThread start
	GetChangeThread start
	change success
	ChangeThread start
	GetChangeThread start
	change no success
进程退出可能原因：  
1.线程ChangeThread中代码顺序调整，1和2调换，执行顺序1-3-2  
2.线程ChangeThread执行完，工作内存中的isChange没有回写回主内存，导致3中isChange不是最新值。