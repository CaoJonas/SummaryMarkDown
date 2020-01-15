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



## Thread

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

