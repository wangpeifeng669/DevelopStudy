AsyncTask源码分析
===
###（一）源码分析
基于 SDK4.2版本的源码分析  
####（1）构造器

    public AsyncTask() {
       mWorker = new WorkerRunnable<Params, Result>() {
            public Result call() throws Exception {
                 mTaskInvoked.set(true);

                 Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);
                  //noinspection unchecked
                 return postResult(doInBackground(mParams));
               }
       };

       mFuture = new FutureTask<Result>(mWorker) {
            @Override
            protected void done() {
                try {
                    postResultIfNotInvoked(get());
                    } catch (InterruptedException e) {
                        android.util.Log.w(LOG_TAG, e);
                    } catch (ExecutionException e) {
                        throw new RuntimeException("An error occured while executing doInBackground()", e.getCause());
                    } catch (CancellationException e) {
                        postResultIfNotInvoked(null);
                    }
                }
            };
       }
      
   构造器只创建俩变量，本质上是调用FutureTask执行线程操作并返回执行完的结果。
####（2）AsyncTask.execute
    public final AsyncTask<Params, Progress, Result> execute(Params... params) {
       return executeOnExecutor(sDefaultExecutor, params);
    }
发起线程操作。
####（3）AsyncTask.executeOnExecutor
    public final AsyncTask<Params, Progress, Result> executeOnExecutor(Executor exec,Params... params) {

        onPreExecute();

        mWorker.mParams = params;
        exec.execute(mFuture);

    }
线程操作前先调用onPreExecute，再将线程操作放入exec线程，exec线程默认为sDefaultExecutor。
####（4）sDefaultExecutor
    private static class SerialExecutor implements Executor {
        final ArrayDeque<Runnable> mTasks = new ArrayDeque<Runnable>();
        Runnable mActive;

        public synchronized void execute(final Runnable r) {
           mTasks.offer(new Runnable() {
              public void run() {
                try {
                  r.run();
                } finally {
                  scheduleNext();
                }
              }
           });
           if (mActive == null) {
              scheduleNext();
           }
        }

        protected synchronized void scheduleNext() {
           if ((mActive = mTasks.poll()) != null) {
                THREAD_POOL_EXECUTOR.execute(mActive);
           }
        }
    }
sDefaultExecutor是SerialExecutor对象，本质上是一个单一线程池，用ArrayDeque线性队列存放线程操作对象，执行完一个将下一个放入线程池。

###（二）使用分析
####（1）用于几秒钟操作的线程，在类说明中也有提到。
AsyncTasks should ideally be used for short operations (a few seconds at the most.) If you need to keep threads running for long periods of time, it is highly recommended you use the various APIs provided by the java.util.concurrent package such as Executor, ThreadPoolExecutor and FutureTask.  
主要原因从源码中分析可知，单一线程池操作线程，如果线程操作时间过长，其他线程会处于等待状态。
####（2）sdk3.0前后对比
3.0之前是直接用ThreadPoolExecutor线程池执行线程，可以同时并发多个线程，但是因为怕并发过多超过CORE_POOL_SIZE+MAXIMUM_POOL_SIZE+sPoolWorkQueue.size的长度，会出现崩溃，3.0以后默认采用单一线程池结构。
####（3）可以调用executeOnExecutor方法，传入自定义的线程操作类，不使用单一线程池。
        Executor exec = new ThreadPoolExecutor(15, 200, 10,TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>());  
        new AsyncTask().executeOnExecutor(exec); 

