# Future

>提供异步计算
>
>利用get 检索结果，只有等计算结束，才能返回结果，要不只能进入阻塞状态
>
>cancel 可以进行取消计算，如果计算已完成，取消操作无效，可以等进行进入阻塞状态，也可以发生取消操作
>
>如果 Future 没有返回的结果，例如用 executor 提交一个 runnable 对象，无返回结果。 调用 get 将返回 null

```java
private static ExecutorService executor = Executors.newFixedThreadPool(8);
Future<String> future = executor.submit(new Callable<String>() {
     public String call() {
          return String(类型的)
     }
});
//如果是返回无类型的
Future<?> future = exexutor.submit(runnable);
```

```java
FutureTask<String> future = new FutureTask<String>(new Callable<String>() {
     public String call() {
          return searcher.search(target);
     }
});
executor.execute(future);
```

```JAVA
FutureTask<String> future = new FutureTask<String>(new Callable<String>() {
     public String call() {
          return searcher.search(target);
     }
});
new Thread(future).start();
```

#### ????????????????????????

```java
??????????????????????????????????????
* <p>Memory consistency effects: Actions taken by the asynchronous computation* <a href="package-summary.html#MemoryVisibility"> <i>happen-before</i></a>* actions following the corresponding {@code Future.get()} in another thread.
```

### cancel

> 1. 如果 该任务 已经完成或者已经被取消，则调用 cancel 失败
> 2. 任务一旦被取消，则不能重新开始
> 3. mayInterruptIfRunning 表示在线程执行过程中能不能 中断任务
> 4. 有意思的是，如果 cancel 返回 ture ,isDone() 返回true, isCancel() 返回 true

```java
/**
     * Attempts to cancel execution of this task.  This attempt will
     * fail if the task has already completed, has already been cancelled,
     * or could not be cancelled for some other reason. If successful,
     * and this task has not started when {@code cancel} is called,
     * this task should never run.  If the task has already started,
     * then the {@code mayInterruptIfRunning} parameter determines
     * whether the thread executing this task should be interrupted in
     * an attempt to stop the task.
     *
     * <p>After this method returns, subsequent calls to {@link #isDone} will
     * always return {@code true}.  Subsequent calls to {@link #isCancelled}
     * will always return {@code true} if this method returned {@code true}.
     *
     * @param mayInterruptIfRunning {@code true} if the thread executing this
     * task should be interrupted; otherwise, in-progress tasks are allowed
     * to complete
     * @return {@code false} if the task could not be cancelled,
     * typically because it has already completed normally;
     * {@code true} otherwise
     */
boolean cancel(boolean mayInterruptIfRunning);
```

### isCancelled

> 如果该任务是在任务完成之前被终止， 则返回 true

```java
/**
     * Returns {@code true} if this task was cancelled before it completed
     * normally.
     *
     * @return {@code true} if this task was cancelled before it completed
     */
boolean isCancelled();
```

### isDone

> 如果该任务已完成，则返回 true
>
> 完成的原因可能是   正常结束、异常或被取消（cancel())
>
> ==但是如果还在计算，就会返回false==, 而不是等结果执行完毕再返回

```java
/**
     * Returns {@code true} if this task completed.
     *
     * Completion may be due to normal termination, an exception, or
     * cancellation -- in all of these cases, this method will return
     * {@code true}.
     *
     * @return {@code true} if this task completed
     */
boolean isDone();
```

### get

> 进入阻塞，知道出现结果。
>
> 还有三个异常抛出

```java

/**
     * Waits if necessary for the computation to complete, and then
     * retrieves its result.
     *
     * @return the computed result
     * @throws CancellationException if the computation was cancelled
     * @throws ExecutionException if the computation threw an
     * exception
     * @throws InterruptedException if the current thread was interrupted
     * while waiting
     */
V get() throws InterruptedException, ExecutionException;
```

### get

> 也会进入阻塞知道结果出现，但是等待有时长限制。
>
> timeout 等待的最大时间
>
> unit 时间单位  TimeUnit.SECONDS
>
> ==futureTask.get(100, TimeUnit.SECONDS)==

```java
/**
     * Waits if necessary for at most the given time for the computation
     * to complete, and then retrieves its result, if available.
     *
     * @param timeout the maximum time to wait
     * @param unit the time unit of the timeout argument
     * @return the computed result
     * @throws CancellationException if the computation was cancelled
     * @throws ExecutionException if the computation threw an
     * exception
     * @throws InterruptedException if the current thread was interrupted
     * while waiting
     * @throws TimeoutException if the wait timed out
     */
V get(long timeout, TimeUnit unit)
     throws InterruptedException, ExecutionException, TimeoutException;
```

