# Future

>提供异步计算
>
>利用get 检索结果，只有等计算结束，才能返回结果，要不只能进入阻塞状态
>
>cancel 可以进行取消计算，如果计算已完成，取消操作无效，可以等进行进入阻塞状态，也可以发生取消操作
>
>如果 Future 没有返回的结果，例如用 executor 提交一个 runnable 对象，无返回结果。 调用 get 将返回 null
>
>未来表示异步计算的结果。提供的方法用于检查计算是否完成、等待计算完成以及检索计算结果。结果只能在计算完成后使用get方法检索，必要时可以阻塞，直到准备好为止。取消由cancel方法执行。提供了其他方法来确定任务是正常完成还是被取消。一旦计算完成，就不能取消计算。如果为了可取消性而使用Future，但不提供可用的结果，可以声明形式Future&lt; &gt;的类型。并作为基础任务的结果返回null。
>
>FutureTask类是实现Runnable的Future的实现，因此可以由执行程序执行。例如，以上带提交的构造可以替换为:
>
>内存一致性影响:异步计算所采取的操作发生——在另一个线程中相应的Future.get()之后的操作发生之前。
>
>```java
> FutureTask<String> future =
>   new FutureTask<String>(new Callable<String>() {
>     public String call() {
>       return searcher.search(target);
>   }});
> executor.execute(future);
>```

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

# FutureTask

> 可取消的异步计算。这个类提供了Future的基本实现，其中包含启动和取消计算、查询计算是否完成以及检索计算结果的方法。该结果只能在计算完成后检索;如果计算尚未完成，则get方法将阻塞。一旦计算完成，就不能重新启动或取消计算(除非使用runAndReset()调用计算)。FutureTask可用于包装Callable或Runnable对象。因为FutureTask实现了Runnable，所以可以将FutureTask提交给执行器执行。除了充当独立类之外，这个类还提供了在创建自定义任务类时可能有用的受保护功能。

## CompletableFuture

> 未来可能被明确完成(设置它的值和状态)，并可能被用作一个CompletionStage，支持依赖的功能和行动，在它完成时触发。
>
> 当两个或多个线程试图完成、completeexception或取消CompletableFuture时，只有一个线程成功。除了这些直接操纵状态和结果和相关方法,CompletableFuture实现接口CompletionStage以下政策:行动提供了依赖完成non-async方法可能执行的线程完成当前CompletableFuture,或其他任何一个完成方法的调用者。所有没有显式执行器参数的异步方法都使用ForkJoinPool.commonPool()执行(除非它不支持至少两个并行级别，在这种情况下，会创建一个新线程来运行每个任务)。为了简化监视、调试和跟踪，所有生成的异步任务都是标记接口CompletableFuture.AsynchronousCompletionTask的实例。所有CompletionStage方法都是独立于其他公共方法实现的，因此一个方法的行为不会受到子类中其他方法重写的影响。
>
> CompletableFuture也通过以下策略实现了Future:由于(不像FutureTask)这个类对导致它被完成的计算没有直接控制，取消被视为只是另一种形式的例外完成。方法cancel与completeexception (new CancellationException())具有相同的效果。方法iscompletedexception()可用于确定CompletableFuture是否以任何异常方式完成。在使用CompletionException完成异常的情况下，方法get()和get(long, TimeUnit)抛出一个ExecutionException，其原因与在相应的CompletionException中持有的原因相同。为了简化大多数上下文中的使用，这个类还定义了join()和getNow(T)方法，在这些情况下直接抛出CompletionException。