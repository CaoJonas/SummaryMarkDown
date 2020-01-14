# FutureTask

*![FutureTask继承关系](java_source_imgs/FutureTask.jpg)

> * <p>A {@code FutureTask} can be used to wrap a {@link Callable} or* {@link Runnable} object.  Because {@code FutureTask} implements* {@code Runnable}, a {@code FutureTask} can be submitted to an* {@link Executor} for execution.
>
>   FutureTask 可以实现了 RunnableFuture 接口，可以接收 Callable 参数和 Runnable 参数，也可以作为任务 由Executor.submit(futureTask);

==但是要记住的是， FutureTask 只是实现了 Future和 Runnable接口，需要利用executor.submit(futureTask) 和 new Thread（futureTask）.start() 才能开始执行==

### 参数为 callable

```java
Callable<Integer> callable = new Callable() {
     @Override
     public Integer call() throws Exception {
          for (int i = 0; i < 100; i++) {

               try {
                    Thread.sleep(random.nextInt(50));
                    throw new Exception("抛出异常");
               } catch (InterruptedException e) {
                    e.printStackTrace();
               }
               testSecondEx.autoAdd();

          }
          return testSecondEx.printNum();
     }
};
FutureTask<Integer> futureTask = new FutureTask<Integer>(callable);
executor.submit(futureTask);
System.out.println(futureTask.get(100, TimeUnit.SECONDS));
executor.shutdown();
```

### 参数为 Runnale

> 参数 runnable ，是要执行的计算， 而 Result 参数则是再runnable 执行结束后，返回的结果

```java
/**
     * Creates a {@code FutureTask} that will, upon running, execute the
     * given {@code Runnable}, and arrange that {@code get} will return the
     * given result on successful completion.
     *
     * @param runnable the runnable task
     * @param result the result to return on successful completion. If
     * you don't need a particular result, consider using
     * constructions of the form:
     * {@code Future<?> f = new FutureTask<Void>(runnable, null)}
     * @throws NullPointerException if the runnable is null
     */
public FutureTask(Runnable runnable, V result) {
     this.callable = Executors.callable(runnable, result);
     this.state = NEW;       // ensure visibility of callable
}
```

```java
Runnable run1 = () -> {
     System.out.println("调用了 Runnale方法");
};
Integer num = 10;
FutureTask<Integer> futureTask = new FutureTask<Integer>(run1, num);
executor.submit(futureTask);
System.out.println(futureTask.get());
// 输出结果为 10 
```

==修订说明:这与这个类以前的版本不同，以前的版本依赖于AbstractQueuedSynchronizer，主要是为了避免在取消竞争期间保持中断状态给用户带来意外。当前设计中的同步控制依赖于通过CAS更新的“state”字段来跟踪完成情况，以及一个简单的Treiber堆栈来保存等待的线程。==

==风格提示:与往常一样，我们绕过了使用AtomicXFieldUpdaters的开销，而是直接使用了不安全的内部特性。==

==此任务的运行状态，最初是新的。运行状态仅在set、setException和cancel方法中转换为终端状态。在完成期间，状态可以采用暂态值来完成(在设置结果时)或中断(仅在中断运行程序以满足取消(true)时)。从这些中间状态到最终状态的转换使用更便宜的有序/延迟写操作，因为值是惟一的，不能进一步修改。==

## 构造函数：

### public FutureTask(Runnable runnable, V result)

> result 是 在 runnable 执行完毕之后返回的结果，如果不希望放回任何结果，可以
>
> Future<?> f = new FutureTask<Void>(runnable, null)；
>
> FutureTask<?> f = new FutureTask<Void>(runnable, null)；

```JAVA
public FutureTask(Runnable runnable, V result) {
     this.callable = Executors.callable(runnable, result);
     this.state = NEW;       // ensure visibility of callable
}
```

