# 多线程问题汇总讨论  
**最近在准备后端开发岗位的面试，在准备阶段和面试结束后，遇到了很多问题，想找到问题的答案有的时候会花费大量的时间。但是找同事或者朋友去讨论这个问题，能够很快的得到问题的答案。我希望通过这个项目让java学习者提出自己的问题，并一起讨论**
##基础问题
>##并行跟并发有什么区别？【[知乎讨论链接](https://www.zhihu.com/question/608650446) 】 

在计算机科学中，"并行"和"并发"是两个相关但具有不同含义的概念。
并行（Parallelism）是指同时执行多个任务或操作。当系统具有多个处理单元（如多核处理器或分布式计算系统）时，可以同时执行多个任务，每个任务在不同的处理单元上运行。并行可以显著提高系统的处理能力和效率。
并发（Concurrency）是指在一个时间段内执行多个任务或操作。这些任务可能在同一个处理单元上交替执行，也可能在多个处理单元上并行执行。并发通常用于处理多个独立的任务或操作，并通过合理的任务调度和资源管理来实现更高的效率。
区别总结如下：
并行是指同时执行多个任务，利用多个处理单元同时进行工作。
并发是指在一个时间段内执行多个任务，这些任务可能在同一个处理单元上交替执行，也可能在多个处理单元上并行执行。
并行更强调同时性和同时处理多个任务的能力。
并发更强调任务的交替执行和任务调度的能力。
需要注意的是，并行和并发的概念并不是互斥的，它们可以同时存在。在某些情况下，可以同时利用并行和并发来提高系统的性能和响应能力。
![img_4.png](img_4.png)  
>##说说什么是进程和线程？【[知乎讨论链接](https://www.zhihu.com/question/603099141) 】   

进程（Process）和线程（Thread）是计算机科学中两个重要的概念，用于描述程序执行的基本单位。
进程是操作系统中一个正在执行的程序实例。每个进程都有自己的地址空间、内存、文件描述符、资源和状态。一个进程可以由一个或多个线程组成。进程之间相互独立，拥有各自的资源和内存空间，通过进程间通信（IPC）机制来进行数据交换和同步。
线程是进程中的一个执行单元。一个进程可以包含多个线程，这些线程共享进程的资源和上下文。线程拥有自己的栈空间和程序计数器，但共享进程的内存空间、文件描述符和其他系统资源。多线程可以同时执行不同的任务，提高程序的并发性和效率。
进程和线程之间有一些区别和关系：
资源开销：创建和维护进程的资源开销较大，而线程的资源开销较小，因为它们共享了进程的资源。
并发性：多个线程可以在同一时间内并发执行，而多个进程需要进行进程切换，实现并发执行。
通信和同步：在同一进程内的线程可以直接共享数据，通信和同步比较容易。而进程之间的通信需要使用特定的IPC机制，如管道、套接字、共享内存等。
容错性：一个线程的异常可能会导致整个进程的崩溃，但是在多进程环境中，一个进程的崩溃不会影响其他进程的运行。
执行方式：在多核处理器上，多个线程可以在不同的核心上并行执行，而进程只能在一个核心上执行。
总结来说，进程是操作系统中资源分配的基本单位，而线程是进程中执行的最小单位。进程用于实现程序的隔离和资源管理，线程用于实现程序的并发执行和任务的划分。
![img_5.png](img_5.png)  
相关讨论问题：协程了解吗？/java是否可以创建子进程？
>##说说线程有几种创建方式？  

创建线程的方式主要有以下几种：  
1. 继承Thread类：通过继承`java.lang.Thread`类创建线程。需要重写Thread类中的`run()`方法，该方法包含线程的执行逻辑。然后创建子类的实例，并调用`start()`方法启动线程。

```java
class MyThread extends Thread {
    public void run() {
        // 线程的执行逻辑
    }
}

// 创建线程并启动
MyThread thread = new MyThread();
thread.start();
```

2. 实现Runnable接口：通过实现`java.lang.Runnable`接口创建线程。需要创建一个实现`run()`方法的类，并将其作为参数传递给`Thread`类的构造函数。然后创建`Thread`对象，并调用`start()`方法启动线程。

```java
class MyRunnable implements Runnable {
    public void run() {
        // 线程的执行逻辑
    }
}

// 创建线程并启动
MyRunnable runnable = new MyRunnable();
Thread thread = new Thread(runnable);
thread.start();
```

3. 使用Callable和Future：通过实现`java.util.concurrent.Callable`接口创建线程。`Callable`接口允许线程返回一个结果，并可以通过`java.util.concurrent.Future`接口获取线程执行的结果。

```java
import java.util.concurrent.Callable;
import java.util.concurrent.FutureTask;

class MyCallable implements Callable<Integer> {
    public Integer call() throws Exception {
        // 线程的执行逻辑
        return 42;
    }
}

// 创建线程并启动
MyCallable callable = new MyCallable();
FutureTask<Integer> futureTask = new FutureTask<>(callable);
Thread thread = new Thread(futureTask);
thread.start();

// 获取线程的执行结果
int result = futureTask.get();
```

4. 使用Executor框架：通过`java.util.concurrent.Executor`和`java.util.concurrent.ExecutorService`接口，以及相关的实现类，如`ThreadPoolExecutor`，可以使用线程池来管理和复用线程。通过`submit()`方法提交实现`Runnable`或`Callable`接口的任务，线程池会自动调度任务的执行。

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

ExecutorService executor = Executors.newFixedThreadPool(5);
executor.execute(new Runnable() {
    public void run() {
        // 线程的执行逻辑
    }
});
executor.shutdown();
```

这些是常见的创建线程的方式，每种方式都适用于不同的场景。选择适合的方式取决于具体的需求和编程风格。另外，Java 8及以上版本还引入了函数式编程特性，可以使用Lambda表达式来简化线程的创建和执行，使代码更加简洁。
![img_6.png](img_6.png)
>##线程有哪些常用的调度方法?  

在Java中，线程的调度（scheduling）指的是操作系统或Java虚拟机决定线程何时运行的过程。以下是Java中常用的线程调度方法：  
sleep(long millis)：使线程进入休眠状态，暂停执行一段指定的时间（以毫秒为单位）。在指定时间结束后，线程会自动恢复执行。sleep() 方法不会释放线程持有的任何锁。  
yield()：提示线程调度器将当前线程切换到就绪状态，以便给其他具有相同优先级的线程执行的机会。但是，调度器是否遵循这个提示是不确定的，可能会忽略 yield() 调用。  
join()：等待调用该方法的线程终止。如果在一个线程中调用另一个线程的 join() 方法，那么当前线程将等待被调用线程执行完成后再继续执行。可以用于线程间的协作和顺序控制。  
wait()、notify() 和 notifyAll()：这些方法是在多线程并发编程中用于线程间的通信和同步的关键方法。wait() 使线程进入等待状态，释放对象锁，等待其他线程调用 notify() 或 notifyAll() 来唤醒。notify() 唤醒一个正在等待该对象锁的线程，而 notifyAll() 唤醒所有等待的线程。  
<font color=red size=5> 注意wait、notify、 notifyAll这几个方法都是Object的方法，主要是因为这几个方法是线程间同步的方法，而线程间通过synchronized进行同步，同步锁锁住的就是实例或者对象。那么问题就来了，synchronized是怎么同步的，具体的原理是什么？（后面会归纳）</font>  
interrupt()：用于中断当前线程的执行。当一个线程调用另一个线程的interrupt()方法时，被调用线程会收到一个中断信号，可以根据需要来处理中断。
isInterrupted()：用于检查当前线程是否被中断，返回一个布尔值。
interrupted()：用于检查当前线程是否被中断，并且会清除中断状态，返回一个布尔值。
这些调度方法提供了不同的控制和协调线程执行的方式。它们可以用于控制线程的执行顺序、线程之间的等待和通信，以及在特定情况下暂停或让出CPU资源。使用这些方法可以更好地管理和优化多线程程序的执行。  
![img_16.png](img_16.png)  
>##线程有哪些状态?  

下面是一个将线程状态整理成表格的示例：

| 线程状态           | 描述                                                         |
|-------------------|--------------------------------------------------------------|
| 新建状态 (New)    | 线程对象已创建但尚未启动                                      |
| 就绪状态 (Runnable) | 可运行状态，等待被调度执行                                    |
| 运行状态 (Running) | 线程正在执行中                                               |
| 阻塞状态 (Blocked) | 线程无法继续执行，因为等待某些条件（如等待输入/输出、锁等）  |
| 等待状态 (Waiting) | 线程调用了 wait()、join() 或 park() 等方法进入等待状态        |
| 超时等待状态 (Timed Waiting) | 类似于等待状态，但可以设置一个超时时间                     |
| 终止状态 (Terminated) | 线程执行完毕或出现异常，进入终止状态                          |  

![img_17.png](img_17.png)  
<font color=red size=5> 注意1：sleep是进入超时等待状态 (Timed Waiting),其实比较好记，只要参数包含时间的，都是进入等待状态 (Timed Waiting)</font>  
<font color=red size=5> 注意2：怎么区分等待状态和阻塞状态呢？(Timed Wait</font>  
等待状态会释放cpu资源同时释放锁（一般已经获取锁），需要等待其他线程的通知或者等待被join的线程执行完毕，（如Object.wait()、Thread.join()、LockSupport.park()等）主动进入该状态  
阻塞状态是释放cpu资源，没获取锁，获取锁或等待I/O操作完成  
上图有什么疑问可以直接查看Thread内部的状态
```java
public enum State {
    /**
     * Thread state for a thread which has not yet started.
     */
    NEW,

    /**
     * Thread state for a runnable thread.  A thread in the runnable
     * state is executing in the Java virtual machine but it may
     * be waiting for other resources from the operating system
     * such as processor.
     */
    RUNNABLE,

    /**
     * Thread state for a thread blocked waiting for a monitor lock.
     * A thread in the blocked state is waiting for a monitor lock
     * to enter a synchronized block/method or
     * reenter a synchronized block/method after calling
     * {@link Object#wait() Object.wait}.
     */
    BLOCKED,

    /**
     * Thread state for a waiting thread.
     * A thread is in the waiting state due to calling one of the
     * following methods:
     * <ul>
     *   <li>{@link Object#wait() Object.wait} with no timeout</li>
     *   <li>{@link #join() Thread.join} with no timeout</li>
     *   <li>{@link LockSupport#park() LockSupport.park}</li>
     * </ul>
     *
     * <p>A thread in the waiting state is waiting for another thread to
     * perform a particular action.
     *
     * For example, a thread that has called <tt>Object.wait()</tt>
     * on an object is waiting for another thread to call
     * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
     * that object. A thread that has called <tt>Thread.join()</tt>
     * is waiting for a specified thread to terminate.
     */
    WAITING,

    /**
     * Thread state for a waiting thread with a specified waiting time.
     * A thread is in the timed waiting state due to calling one of
     * the following methods with a specified positive waiting time:
     * <ul>
     *   <li>{@link #sleep Thread.sleep}</li>
     *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
     *   <li>{@link #join(long) Thread.join} with timeout</li>
     *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
     *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
     * </ul>
     */
    TIMED_WAITING,

    /**
     * Thread state for a terminated thread.
     * The thread has completed execution.
     */
    TERMINATED;
}
```  

>##什么是线程安全？如何实现线程安全？  
线程安全指的是多线程环境下的程序或代码能够正确地处理共享数据，保证多个线程同时访问时不会出现不一致、不可预期的结果。在线程安全的情况下，无论线程如何交替执行，都能保证程序的行为符合预期。  

| 线程安全概念    | 实现方式                                                     |
|----------------|--------------------------------------------------------------|
| 互斥锁 (Mutex)  | 使用 synchronized 关键字或 ReentrantLock 类来保护共享资源    |
| 原子操作 (Atomic Operation) | 使用原子操作类如 AtomicInteger、AtomicLong 来操作共享数据   |
| 同步容器类 (Synchronized Containers) | 使用线程安全的容器类如 Vector、Hashtable 和 ConcurrentHashMap |
| 线程安全的数据结构 | 使用线程安全的数据结构如 CopyOnWriteArrayList、ConcurrentLinkedQueue |
| 不可变对象 (Immutable Objects) | 设计不可变对象，状态无法被修改，避免了共享数据的竞争和同步 |
| 线程本地存储 (Thread-Local Storage) | 使用 ThreadLocal 类实现线程本地变量，每个线程拥有自己的副本 |  


>##ThreadLocal使用过吗？使用过程中存在什么问题？  
ThreadLocal感觉是个老大难问题，平常不怎么使用，但是面试却被经常问，之前一直不理解ThreadLocal到底是怎么实现的，其实搞清楚这几个类的关系，就很好理解了。
（1）Thread
![img_7.png](img_7.png)  
首先看Thread,里面是包含两个成员变量的。
ThreadLocal.ThreadLocalMap threadLocals和ThreadLocal.ThreadLocalMap inheritableThreadLocals
这两个都是ThreadLocal.ThreadLocalMap，我们先忽略inheritableThreadLocals，主要关注threadLocals
我们创建一个线程，就会有一个threadLocals，但是它是没有初始化的，具体初始化在什么地方呢？
![img_8.png](img_8.png)    
从调用关系上看，是在ThreadLocal里面初始化的，方法名称为createMap
![img_9.png](img_9.png)    
再往上跟踪，就是set和get两个方法中会调用createMap，如果使用过ThreadLocal的同学，一定就很了解ThreadLocal中的set和get方法了。  
因此，能够得出结论，thread中threadLocals是在线程内第一次使用ThreadLocal的时候初始化的。
（2）ThreadLocal
ThreadLocalMap是ThreadLocal中静态内部类，为什么会把ThreadLocalMap放到ThreadLocal中呢？可以思考一下（https://ask.csdn.net/questions/730831）
![img_10.png](img_10.png)  
ThreadLocalMap里面主要是Entry[] table;这里的Entry又是ThreadLocalMap的静态内部类，然后Entry又是继承自弱引用WeakReference<ThreadLocal<?>>，开始看这个结构，确实让人摸不着头脑，但是你把这几个内部类看做是单独的类，可能就好理解一些。
![img_11.png](img_11.png)    
这几个类的具体关系基本就是这样：
![img_12.png](img_12.png)  
具体数据储存的结构是这样：
![img_13.png](img_13.png)  
ThreadA初始化threadLocal1、threadLocal2、threadLocal3的过程是这样：
![img_14.png](img_14.png)  
梳理到这个地方，其实还是有很多疑问的。
疑问一：Entry[] table中的Entry其实是有一个key和value的，key作为软引用（threadLocal），如果在运行过程中发生gc,key被回收了是否会存在问题？
答案：没有问题的
这是因为key作为软引用（threadLocal），指向了我们定义的threadLocal，但是主线程中还存在指向threadLocal的强引用，是没办法被回收的。
疑问二：Thread中的threadLocalMap是随着Thread的结束，引用就会消失，应该不会导致内存泄露啊？为什么会说threadLocal容易导致内存泄露？
答案：Thread结束运行确实不会存在内存泄露的问题，但是我们在实际项目开发过程中，更多的时候是使用线程池来管理线程，核心线程一般会重复利用，这个时候threadLocalMap就会一直存在引用。这个时候就会存在内存泄露的问题。
疑问三：threadLocal是怎么导致内存泄露的？
答案：存在两个问题，第一个问题：是threadLocalMap中的entry[] table，中entry的key是主线程的threadLocal，当主线程（主线程可能是非核心线程，子线程是核心线程）被回收，对应的主线程对threadLocal的强引用就没有了。但是entry的key还引用着threadLocal，  
如果threadLocal比较大，那么可能就会存在内存泄露。（说白了就是这个key不需要了，但是threadLocalMap中还存储着）目前entry的key是软引用，一旦发生gc就可以回收。
第二个问题：key可以被回收，value怎么办呢？
【ThreadLocalMap.key到期之'探测是清理'+'启发式清理'流程(https://www.cnblogs.com/lihw/p/17215370.html)】
具体可以看看上面这个博客，从中可以看出在set\get\rehash的时候都会去清理key为null的entry,但是这种清理不一定能清理完全，并且需要依赖其他方法才能触发，仍然存在内存泄露的可能。避免内存泄露最好的方式就是再使用后remove一下。
>##线程池的原理，是什么/核心线程是否会被回收/怎么保证核心线程不回收/怎么自动回收核心线程？[知乎讨论链接](https://www.zhihu.com/question/603099141) (该问题可以从以下几个方面解答)    

>##线程池的核心参数有哪些？
首先说一下多线程的几个核心参数。  
1.corePoolSize:核心线程大小  线程池中，线程保活的最大数量（思考：核心线程是否可以回收？） 
2.maximumPoolSize：最大线程大小 （思考：一个线程怎么能区分其是核心线程还是非核心线程？非核心线程是怎么回收的？）  
3.keepAliveTime：线程的空闲的最大时间（思考：怎么判断线程空闲的时间）  
4.unit：超时（上面第三个参数的）的时间单位  
5.workQueue（BlockingQueue<Runnable>）：工作队列，这里的工作队列和内部类Worker不太一样，Worker可以理解成工作人员，workQueue是任务队列  
6.threadFactory：线程工厂,主要实现newThread方法就可以，一般 使用Executors中自带的DefaultThreadFactory
>##线程池的运行流程（原理）？
线程池运行的整体流程：  
![线程池的运行流程.png](线程池的运行流程.png)
>##核心线程是否会被回收？ 
想要了解这个问题需要理解  
1.线程池是否能区分出核心线程和非核心线程？   
2.线程是怎么被回收的？  
回答1：线程池是无法区分出核心线程和非核心线程
具体可以看一下worker里面的属性，并没有专门的属性区分核心线程和非核心线程
![img_2.png](img_2.png)
回答2：
具体看一下worker内部方法--runWorker
![img_3.png](img_3.png)
```java
    final void runWorker(Worker w) {
        Thread wt = Thread.currentThread();
        Runnable task = w.firstTask;
        w.firstTask = null;
        w.unlock(); // allow interrupts
        boolean completedAbruptly = true;
        try {
            while (task != null || (task = getTask()) != null) {
                w.lock();
                // If pool is stopping, ensure thread is interrupted;
                // if not, ensure thread is not interrupted.  This
                // requires a recheck in second case to deal with
                // shutdownNow race while clearing interrupt
                if ((runStateAtLeast(ctl.get(), STOP) ||
                     (Thread.interrupted() &&
                      runStateAtLeast(ctl.get(), STOP))) &&
                    !wt.isInterrupted())
                    wt.interrupt();
                try {
                    beforeExecute(wt, task);
                    Throwable thrown = null;
                    try {
                        task.run();
                    } catch (RuntimeException x) {
                        thrown = x; throw x;
                    } catch (Error x) {
                        thrown = x; throw x;
                    } catch (Throwable x) {
                        thrown = x; throw new Error(x);
                    } finally {
                        afterExecute(task, thrown);
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }
```
从runWorker方法好像只能看出来如果任务获取不到，工作线程就会被回收  
那么新的问题就来了？ 
keepAliveTime这个参数是用来判断线程空闲时长，具体回收过程按说需要依赖两个信息  
(1)是否超过最大空闲时间  
(2)是否为核心线程(前提是allowCoreThreadTimeOut==false)  
这两个信息在runWorker中都没有体现出来，真正导致线程回收的方法其实是在getTask()这个方法中  
具体解释可以看下面代码中的注释  
```java
 private Runnable getTask() {
        boolean timedOut = false; // Did the last poll() time out?

        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN && (rs >= STOP || workQueue.isEmpty())) {
                decrementWorkerCount();
                return null;
            }

            int wc = workerCountOf(c);

            // Are workers subject to culling?
            //如果allowCoreThreadTimeOut（允许核心线程可以超时）设置为ture,核心线程也可以回收
            boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;

            if ((wc > maximumPoolSize || (timed && timedOut))
                && (wc > 1 || workQueue.isEmpty())) {
                if (compareAndDecrementWorkerCount(c))
                    return null;
                continue;
            }

            try {
                //如果可以回收，那么通过workQueue.poll设置一定时间内获取队列顶层元素，如果获取超时就为null
                //如果不可以回收就通过workQueue.take进行阻塞
                Runnable r = timed ?
                    workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                    workQueue.take();
                if (r != null)
                    return r;
                timedOut = true; 
            } catch (InterruptedException retry) {
                timedOut = false;
            }
        }
    }
```
引申问题？阻塞队列是怎么实现take的  
>##线程池的拒绝策略有哪些？
线程池的拒绝策略指的是当线程池无法接受新任务时，如何处理这些被拒绝的任务。以下是几种常见的线程池拒绝策略：  
CallerRunsPolicy（调用者运行策略）：当线程池无法接受新任务时，将拒绝的任务返回给调用者，由调用者自行执行该任务。这种策略可以降低任务的提交速度，但可以保证任务不会丢失。  
AbortPolicy（中止策略）：当线程池无法接受新任务时，直接抛出RejectedExecutionException异常，阻止任务的提交。这种策略是默认的拒绝策略。  
DiscardPolicy（丢弃策略）：当线程池无法接受新任务时，直接丢弃该任务，不做任何处理。被丢弃的任务不会执行。 
DiscardOldestPolicy（丢弃最旧策略）：当线程池无法接受新任务时，丢弃等待时间最长的任务，然后尝试将新任务加入线程池。  
此外，还可以自定义拒绝策略，实现RejectedExecutionHandler接口，并实现自定义的拒绝策略。自定义拒绝策略可以根据具体的业务需求进行处理，例如将被拒绝的任务记录下来或者将其存入消息队列等待执行。
dubbo中的线程拒绝策略:  
(1)输出了一条警告级别的日志，日志内容为线程池的详细设置参数，以及线程池当前的状态，还有当前拒绝任务的一些详细信息。可以说，这条日志，使用dubbo的有过生产运维经验的或多或少是见过的，这个日志简直就是日志打印的典范，其他的日志打印的典范还有spring。得益于这么详细的日志，可以很容易定位到问题所在；  
(2)输出当前线程堆栈详情，这个太有用了，当你通过上面的日志信息还不能定位问题时，案发现场的dump线程上下文信息就是你发现问题的救命稻草；  
(3)继续抛出拒绝执行异常，使本次任务失败，这个继承了JDK默认拒绝策略的特性；  
Netty中的线程池拒绝策略:  
Netty中的实现很像JDK中的CallerRunsPolicy，舍不得丢弃任务。不同的是，CallerRunsPolicy是直接在调用者线程执行的任务。而 Netty是新建了一个线程来处理的。所以，Netty的实现相较于调用者执行策略的使用面就可以扩展到支持高效率高性能的场景了。但是也要注意一点，Netty的实现里，在创建线程时未做任何的判断约束，也就是说只要系统还有资源就会创建新的线程来处理，直到new不出新的线程了，才会抛创建线程失败的异常
activeMq中的线程池拒绝策略：  
activeMq中的策略属于最大努力执行型策略，当触发拒绝策略时，会在次努力一分钟。重新将任务塞进任务队列，当一分钟超时还没成功时，就抛出异常  
pinpoint中的线程池拒绝策略：
选择适当的拒绝策略取决于应用程序的需求和特点。需要根据具体情况，权衡任务的丢失与延迟，选择最合适的拒绝策略。
pinpoint的拒绝策略实现很有特点，和其他的实现都不同。他定义了一个拒绝策略链，包装了一个拒绝策略列表，当触发拒绝策略时，会将策略链中的rejectedExecution依次执行一遍  
>##线程池有哪几种工作队列？  

##java内存模型  


