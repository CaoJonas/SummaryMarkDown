# Thread&&ThreadGroup

## ThreadGroup

### 创建线程组

```java
/**
     * Creates an empty Thread group that is not in any Thread group.
     * This method is used to create the system Thread group.
     */
private ThreadGroup() {     // called from C code
     this.name = "system";
     this.maxPriority = Thread.MAX_PRIORITY;
     this.parent = null;
}
```

### 创建线程组

> 创建的线程组的名字 为==name==,但是线程的父线程组 为当前运行的线程所属的线程组。

```java
/**
     * Constructs a new thread group. The parent of this new group is
     * the thread group of the currently running thread.
     * <p>
     * The <code>checkAccess</code> method of the parent thread group is
     * called with no arguments; this may result in a security exception.
     *
     * @param   name   the name of the new thread group.
     * @exception  SecurityException  if the current thread cannot create a
     *               thread in the specified thread group.
     * @see     java.lang.ThreadGroup#checkAccess()
     * @since   JDK1.0
     */
public ThreadGroup(String name) {    
     this(Thread.currentThread().getThreadGroup(), name);
}
Runnable run = () -> {	// 当前运行的线程为 th1, th1 所属线程的名字为 th1&th2;
     ThreadGroup th = new ThreadGroup("到底是谁");
     System.out.println(th.getParent().getName());
     System.out.println(th.getName());
};
ThreadGroup threadGroup= new ThreadGroup("th1&th2");
Thread th1 = new Thread(threadGroup, run, "你才这是线程级");
th1.start();
/*
th1&th2
到底是谁
*/
```

### 创建线程组

> 创建线程组，并指定当前线程组的父线程组
>
> 并且要检查父线程组的权限

```java
/**
     * Creates a new thread group. The parent of this new group is the
     * specified thread group.
     * <p>
     * The <code>checkAccess</code> method of the parent thread group is
     * called with no arguments; this may result in a security exception.
     *
     * @param     parent   the parent thread group.
     * @param     name     the name of the new thread group.
     * @exception  NullPointerException  if the thread group argument is
     *               <code>null</code>.
     * @exception  SecurityException  if the current thread cannot create a
     *               thread in the specified thread group.
     * @see     java.lang.SecurityException
     * @see     java.lang.ThreadGroup#checkAccess()
     * @since   JDK1.0
     */
public ThreadGroup(ThreadGroup parent, String name) {
     this(checkParentAccess(parent), parent, name);
}
```

### setMaxPriority

> 如果当前线程设置的 优先级 大于 10 或者小于 1； 优先级就不会改变，但是如果当前线程组有父线程组，如果 父线程组的优先级 为 6， 而设置当前线程组 的优先级 为 8， 这个时候优先级就会设置成夫线程组的最大优先级（以父线程组为准）

```java
/**
* The minimum priority that a thread can have.
*/
public final static int MIN_PRIORITY = 1;
/**
* The default priority that is assigned to a thread.
*/
public final static int NORM_PRIORITY = 5;
/**
* The maximum priority that a thread can have.
*/
public final static int MAX_PRIORITY = 10;
ThreadGroup threadGroup = new ThreadGroup("我是线程组");
threadGroup.setMaxPriority(6);
ThreadGroup threadGroupChild = new ThreadGroup(threadGroup, "我是子线程组");
// 如果 优先级高于 父 线程组
threadGroupChild.setMaxPriority(8);
System.out.println(threadGroupChild.getMaxPriority());  // 6
// 如果小于等于 父线程组
threadGroupChild.setMaxPriority(3);
System.out.println(threadGroupChild.getMaxPriority());  // 3
```

## Thread

> 线程是程序中执行的线程。Java虚拟机允许应用程序拥有多个并发运行的执行线程。
>
> 每个线程都有一个优先级。优先级高的线程优先于优先级低的线程执行。每个线程可能被标记为守护进程，也可能不被标记为守护进程。当在某个线程中运行的代码创建一个新线程对象时，新线程的优先级最初设置为与创建线程的优先级相等，并且只有在创建线程是一个守护进程时才作为守护进程线程。
>
> 当Java虚拟机启动时，通常有一个非守护进程线程(它通常调用一些指定类的名为main的方法)。Java虚拟机继续执行线程，直到出现以下情况之一:
>
> 1. 类运行时的退出方法已被调用，并且安全管理器已允许执行退出操作。
> 2. 非守护进程线程的所有线程都已死亡，要么从对run方法的调用返回，要么抛出一个在run方法之外传播的异常。
>
> 有两种方法可以创建新的执行线程。一种方法是将一个类声明为Thread的子类。这个子类应该覆盖类Thread的run方法。然后可以分配并启动子类的实例。例如，计算大于指定值的素数的线程可以写成如下形式:
>
> ```java
> // 继承类
> class PrimeThread extends Thread {
>     long minPrime;
>     PrimeThread(long minPrime) {
>         this.minPrime = minPrime;
>     }
> 
>     public void run() {
>         // compute primes larger than minPrime
>         . . .
>     }
> }
> // 实现了 Runnable 接口
> class PrimeRun implements Runnable {
>     long minPrime;
>     PrimeRun(long minPrime) {
>         this.minPrime = minPrime;
>     }
> 
>     public void run() {
>         // compute primes larger than minPrime
>         . . .
>     }
> }
> 
> ```
>
> 

### 创建线程

#### public Thread() {}默认构造函数

> nextThreadNum -- 静态的 获取线程名称  threadInitNumber 自增， synchronized

```java
/* For autonumbering anonymous threads. */
private static int threadInitNumber;
private static synchronized int nextThreadNum() {
     return threadInitNumber++;
}
```

#### public Thread(ThreadGroup group, ....)

> 如果没有设置线程组，该线程所属于当前运行的线程的线程组

```java
Thread th3 = new Thread("内部线程");
System.out.println(th3.getThreadGroup()); // main
```

#### start

> 在启动线程时， threadStatus 必须是 0

```java
public synchronized void start() {
    /**
     * This method is not invoked for the main method thread or "system"
     * group threads created/set up by the VM. Any new functionality added
     * to this method in the future may have to also be added to the VM.
     *
     * A zero status value corresponds to state "NEW".
     */
    if (threadStatus != 0)
        throw new IllegalThreadStateException();
    /* Notify the group that this thread is about to be started
     * so that it can be added to the group's list of threads
     * and the group's unstarted count can be decremented. */
    group.add(this);
    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
              it will be passed up the call stack */
        }
    }
}
```

#### run

> 如果这个线程是使用一个单独的Runnable run对象构造的，那么将调用该Runnable对象的run方法;否则，此方法什么也不做并返回。
>
> Thread的子类应该覆盖此方法。

```java
@Override
public void run() {
    if (target != null) {
        target.run();
    }
}
```

#### interrupt()

> 除非当前线程正在中断自身(这是始终允许的)，否则将调用此线程的checkAccess方法，这可能导致抛出SecurityException。
>
> If this thread is blocked in an invocation of the [`wait()`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#wait--), [`wait(long)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#wait-long-), or [`wait(long, int)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#wait-long-int-) methods of the [`Object`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html) class, or of the [`join()`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#join--), [`join(long)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#join-long-), [`join(long, int)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#join-long-int-), [`sleep(long)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#sleep-long-), or [`sleep(long, int)`](https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html#sleep-long-int-), methods of this class, then its interrupt status will be cleared and it will receive an [`InterruptedException`](https://docs.oracle.com/javase/8/docs/api/java/lang/InterruptedException.html).
>
> 如果在上面的方法中中断线程，会爆出` ·InterruptException ·` 
>
> `如果这个线程在中断机制的I/O操作中被阻塞`，那么通道将被关闭，线程的中断状态将被设置，并且线程将收到一个ClosedByInterruptException。
>
> 如果这个线程在`选择器`中被阻塞，那么线程的中断状态将被设置，并且它将立即从选择操作返回，可能带有一个非零值，就像调用了选择器的唤醒方法一样。
>
> 如果前面的条件都不成立，那么这个线程的中断状态将被设置。
>
> `中断非活动的线程不需要有任何影响。`
>
> 该方法会清除中断标志位。`**需要注意的是，当抛出InterruptedException时候，会清除中断标志位，也就是说在调用isInterrupted会返回false。**`

```java
Thread th1 = new Thread(()-> {
    for(int i = 0; i < 10000; i++) {
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
});
Thread th2 = new Thread(() -> {
    try {
        Thread.sleep(2000);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    th1.interrupt();
    System.out.println(th1.isInterrupted()); // false
});
th1.start();
th2.start();
th1.join();
th2.join();

// false
```

#### interrupted()

> `测试当前线程是否被中断。线程的中断状态被这个方法清除`。换句话说，如果这个方法被连续调用两次，那么第二个调用将返回false(除非当前线程在第一个调用清除其中断状态并在第二个调用检查它之前再次中断)。
>
> `·如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。·`
>
> 由于线程在中断时未处于活动状态而被忽略的线程中断将通过此方法返回false来反映。
>
> isInterrupted(True)  // 调用私有方法， 表示清空状态， 和 isInterrupted() 则是调用 isInterrupted(false)

```java
public static boolean interrupted() {
    return currentThread().isInterrupted(true);
}
```

#### isInterrupted()

> `测试这个线程是否已经被中断`。线程的中断状态不受此方法的影响。
>
> 由于线程在中断时未处于活动状态而被忽略的线程中断将通过此方法返回false来反映。

```java
public boolean isInterrupted() {
    return isInterrupted(false);
}
```

#### [join](https://www.cnblogs.com/JuncaiF/p/11269934.html)

###### [为什么join会阻塞主（父）线程？](https://blog.csdn.net/qq_20919883/article/details/100695018)

###### [Java join()使用及源码分析](https://blog.csdn.net/zhaominpro/article/details/78063820)

> ==超时为0表示永远等待==

> 阻塞的是父线程，而不是子线程，  join 是一个同步方法, 有人把 join 方法改写成下面的形式，在main 线程中调用 `t.join()` 方法，就是首先获取` th`的锁，进入代码块， 但是执行 `this.wait()` ；也就是当前线程会被阻塞，进入等待其他线程唤醒` th `对象上的等待线程
>
> th 调用 join 方法，然后在 join 方法中又调用 wait() 方法，在调用 wait() 方法时，就是一个普通变量，th.wait() 就是将当前对象
>
> join 其实会让调用者线程进入等待状态，`并释放调用者线程的所占有的资源`，进入当前对象的等待队列，等待当前对象的其他线程唤醒, `释放给子线程调用`
>
> ```java
> public fianl void join(long millis) {
>     synchronized(this) {
>         this.wait();
>     }
> }
> syncronized(B){
> ...
> B.wait
> ...
> }
> ```

```java
public static void main(String ...args) {
    Thread th = new Thread();
    th.join();
}
public final synchronized void join(long millis)
throws InterruptedException {
    long base = System.currentTimeMillis();
    long now = 0;
    if (millis < 0) {
        throw new IllegalArgumentException("timeout value is negative");
    }
    if (millis == 0) {
        while (isAlive()) {
            wait(0);
        }
    } else {
        while (isAlive()) {
            long delay = millis - now;
            if (delay <= 0) {
                break;
            }
            wait(delay);
            now = System.currentTimeMillis() - base;
        }
    }
}
```

####  sleep

> public static native void sleep(long millis)方法显然是Thread的静态方法，很显然它是让当前线程按照指定的时间休眠，其休眠时间的精度取决于处理器的计时器和调度器。`·需要注意的是如果当前线程获得了锁，sleep方法并不会失去锁·`。sleep方法经常拿来与Object.wait()方法进行比价，这也是面试经常被问的地方。
>
> 1. `只是会让出时间片`
> 2. 如果线程在睡眠状态被中断，将会抛出IterruptedException中断异常
> 3. 在哪个线程里面调用sleep()方法就阻塞哪个线程。
> 4. 线程睡眠到期自动苏醒，并返回到可运行状态（就绪），不是运行状态。
> 5. sleep 休眠的线程并不会失去任何的监视器（可以理解为成锁）。

#### yield()

> ==只是让出时间片，不释放锁，并且会参与时间片的竞争==

> 1. Java线程中的Thread.yield( )方法，译为线程让步。顾名思义，就是说当一个线程使用了这个方法之后，它就会把自己CPU执行的时间让掉，
> 2. *让自己或者其它的线程运行，`注意是让自己或者其他线程运行`，并不是单纯的让给其他线程。yield()的作用是让步。它能让当前线程由“运行状态”进入到“就绪状态”，从而让其它具有`相同优先级`的等待线程获取执行权；但是，并不能保证在当前线程调用yield()之后，其它具有相同优先级的线程就一定能获得执行权；也有可能是当前线程又进入到“运行状态”继续运行！( 举个例子：一帮朋友在排队上公交车，轮到Yield的时候，他突然说：我不想先上去了，咱们大家来竞赛上公交车。然后所有人就一块冲向公交车，**有可能是其他人先上车了，也有可能是Yield先上车了。)  ***
> 3. 但是线程是有优先级的，优先级越高的人，就一定能第一个上车吗？这是不一定的，优先级高的人仅仅只是第一个上车的概率大了一点而已，**最终第一个上车的，也有可能是优先级最低的人。并且**所谓的优先级执行，是在大量执行**次数中才能体现出来的。
> 4. Thread.yield() 方法，使当前线程由执行状态，变成为就绪状态，让出cpu时间，在下一个线程执行时候，此线程有可能被执行，也有可能没有被执行。

#### wait

> wait 方法会释放资源，等待其他线程唤醒
>
> > `wait(0)的参数如果是零，那就相当于 wait() 需要同一个对象的其他线程唤醒`
> >
> > ==wait(0)的参数如果是零，那就相当于 wait() 需要同一个对象的其他线程唤醒==
>
> 比较有意思的是： 当前线程释放资源，进入等待池中，进入 wait 状态或者timewait ，在被其他线程唤醒的时候，
>
> （1） 进入锁池（可能包含多个线程，竞争锁，进入阻塞）因此，会进入 blocked
>
> （2） 进入锁池，立即获得锁，变成 Runnable，等待线程调度

### [等待池和锁池](https://blog.csdn.net/emailed/article/details/4689220)

> 等待池和锁池的理解：当调用了wait()方法，释放锁资源和Cpu资源，线程进入等待池，当其他线程调用notify()会从等待池任意选择一个线程调入锁池，notifyAll()会调用所有等待池线程进入锁池，锁池里的对象可以竞争锁，优先级高的获得锁的能力更强，获得锁的线程可以进入就绪态继续执行，执行完之后释放锁，然后锁池里的线程再继续竞争。
>
> [等待池和锁池经典 并不是等待池进入锁池](https://www.cnblogs.com/tiancai/p/9371655.html)
>
> A，B，C都为了获取obj 对象的锁，同一时刻只有一个线程获取锁，而其他线程会直接进入锁池，直到获取锁，出锁池；但是如果有现成在获取锁之后， 调用 wait 方法，就会进入等待池，释放锁，等待已获取锁的其他线程唤醒。
>
> 不过有意思的是： A在调用 wait 之后，线程会进入该对象的等待池，知道同一个对象的 B 线程调用 notifyAll or notify 方法，唤醒在等待池中的线程，进入锁池（锁池中的对象是由于获取锁失败，正在等待获取锁的阻塞线程），B 线程在调用 欢喜那个方法之后，并未释放锁， 而是将当前线程的程序执行完毕（因为我们知道 wait 或者 notify 都是在同步状态下获取的，所以就是等当前线程退出同步块），才会释放锁，位于锁池中的线程才会竞争获取 资源锁。
>
> 并且，如果当前线程在调用 wait() 方法之后，无其他线程调用同一个对象的 唤醒方法，当前线程绝对不会醒来（非超时wait，或被中断），只有其他线程唤醒才可以

![](java_source_imgs\wait_thread_wait_blocked.png)

### sleep 和 wait 的区别

> 1. ·`而synchronized里有sleep就是定时炸弹了。`
>
> 两者主要的区别：
>
> 1. sleep()方法是Thread的静态方法，而wait是Object实例方法
> 2. `wait()方法必须要在同步方法或者同步块中调用，也就是必须已经获得对象锁`。而sleep()方法没有这个限制可以在任何地方种使用。另外，`wait()方法会释放占有的对象锁`，使得该线程进入等待池中，等待下一次获取资源。`而sleep()方法只是会让出CPU并不会释放掉对象锁`
> 3. newth.wait()   尽管 newTh 是一个线程，但是这里只是把它当作一个普通的对象，要做的就是首先获取 newTh 对象的内置锁，然后 调用 newTh.wait() 所处的     线程才会被阻塞。 
> 4. sleep()方法在休眠时间达到后如果再次获得CPU时间片就会继续执行，而wait()方法必须等待Object.notift/Object.notifyAll通知后，才会离开等待池，并且再次获得CPU时间片才会继续执行

### sleep 和 yield 的区别

> 1. 都是静态方法
> 2. 都会让出时间片
> 3. 都不会释放锁
> 4. sleep 会让当亲线程进入等待状态，让出的时间片会被其他线程竞争，而yield 让出的时间片，只会让同等优先级的线程竞争， 自己也会参与竞争，并且有可能获得锁。

### [线程阻塞](https://frank-lam.github.io/fullstack-tutorial/#/JavaArchitecture/03-Java并发编程?id=线程阻塞)

>  线程可以阻塞于四种状态：
>
> - 当线程执行 Thread.sleep() 时，它一直阻塞到指定的毫秒时间之后，或者阻塞被另一个线程打断；
> - 当线程碰到一条 wait() 语句时，它会一直阻塞到接到通知 notify()、被中断或经过了指定毫秒时间为止（若制定了超时值的话）
> - 线程阻塞与不同 I/O 的方式有多种。常见的一种方式是 InputStream 的 read() 方法，该方法一直阻塞到从流中读取一个字节的数据为止，它可以无限阻塞，因此不能指定超时时间；
> - 线程也可以阻塞等待获取某个对象锁的排他性访问权限（即等待获得 synchronized 语句必须的锁时阻塞）。
>
> 注意，并非所有的阻塞状态都是可中断的，以上阻塞状态的前两种可以被中断，后两种不会对中断做出反应

### [4. 中断](https://frank-lam.github.io/fullstack-tutorial/#/JavaArchitecture/03-Java并发编程?id=_4-中断)

> 一个线程执行完毕之后会自动结束，如果在运行过程中发生异常也会提前结束。
>
> 通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于**阻塞、限期等待或者无限期等待**状态，那么就会抛出 InterruptedException，从而提前结束该线程。`但是不能中断 I/O 阻塞和 synchronized 锁阻塞。`
>
> 对于以下代码，在 main() 中启动一个线程之后再中断它，由于线程中调用了 Thread.sleep() 方法，因此会抛出一个 InterruptedException，从而提前结束线程，不执行之后的语句。

### 中断无效

> `如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。`

### 线程被唤醒后，是从头还是从上次阻塞的地方执行

> 首先线程是不可逆的，在进入阻塞状态会保存线程状态，知道被线程调度，从保存状态开始执行
>
> wait 是在同步代码块中才能使用，因此，不用担心数据

### `In·terruptionException·`

> 如果任何线程中断了当前线程。当抛出此异常时，当前线程的中断状态将被清除。

### stop 被停止

> 1. 即刻停止run()方法中剩余的全部工作，包括在catch或finally语句中，并抛出ThreadDeath异常(通常情况下此异常不需要显示的捕获)，因此可能会导致一些清理性的工作的得不到完成，如文件，数据库等的关闭。
> 2. 会立即释放该线程所持有的所有的锁，导致数据得不到同步的处理，出现数据不一致的问题。从上面的程序验证结果来看，stop()确实是不安全的。它的不安全主要是针对于第二点：释放该线程所持有的所有的锁。一般任何进行加锁的代码块，都是为了保护数据的一致性，如果在调用thread.stop()后导致了该线程所持有的所有锁的突然释放，那么被保护数据就有可能呈现不一致性，其他线程在使用这些被破坏的数据时，有可能导致一些很奇怪的应用程序错误。

### suspend()和resume()必须要成对出现，否则非常容易发生死锁。

> 因为suspend方法并不会释放锁，如果使用suspend的目标线程对一个重要的系统资源持有锁，那么没任何线程可以使用这个资源直到要suspend的目标线程被resumed，如果一个线程在resume目标线程之前尝试持有这个重要的系统资源锁再去resume目标线程，这两条线程就相互死锁了，也就冻结线程。

### wait(0) 和 join(0)

> 一定要注意，尽管这些都包含带有参数的方法，但是 0 在这两个方法中表示 永久， 在 sleep 方法中就是等待 0