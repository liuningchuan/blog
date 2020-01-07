---
title: "Java线程池ThreadPoolExecutor入门"
tags:
- Java
categories:
- Java
catalog: true
cover: "/images/header_img/header-8.jpg"
date: 2019-12-29 20:54:24
---

多线程开发中，由于线程数量多，并且每个线程执行一段时间就结束，所以要频繁地创建线程，但是这样频繁地创建线程会大大降低系统的效率，频繁创建线程和销毁线程都会消耗时间。

使用线程池后减少了创建和销毁线程的次数，每个线程都可以被重复利用，颗执行多个任务；同时可以根据系统的承受能力，调整线程池中线程的数目，避免出现将系统内存消耗完的情况。

##### 线程池的优点

- 第一：降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- 第二：提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- 第三：提高线程的可管理性。线程是稀缺资源，如果无限制地创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一分配、调优和监控。

#### 什么时候使用线程池

- 单个任务处理时间比较短
- 需要处理的任务数量很大

Java线程池提供一个线程队列，队列中保存着所有等待状态的线程。线程池通常在执行大量异步任务时，由于减少了创建与销毁的额外开销，并且它们提供了一种限制和管理资源（包括线程）的方法，使得性能提升明显。

线程池的继承体系结构如下：

```java
java.util.concurrent.Executor
	|-- ExecutorService
		|-- ThreadPoolExecutor
		|-- ScheduledExceutorService
			|-- ScheduledThreadPoolExecutor
```

ThreadPoolExecutor作为线程池的实现类，执行线程池中每个线程提交的任务。ThreadPoolExecutor的构造函数如下：

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
            null :
            AccessController.getContext();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

| 序号 | 名称            | 类型                     | 定义             |
| ---- | --------------- | ------------------------ | ---------------- |
| 1    | corePoolSize    | int                      | 核心线程池大小   |
| 2    | maximumPoolSize | int                      | 最大线程池大小   |
| 3    | keepAliveTime   | long                     | 线程最大空闲时间 |
| 4    | unit            | TimeUnit                 | 时间单位         |
| 5    | workQueue       | BlockingQueue            | 线程等待队列     |
| 6    | threadFactory   | ThreadFactory            | 线程创建工厂     |
| 7    | Handler         | RejectedExecutionHandler | 拒绝策略         |



- **corePoolSize**：线程池的核心线程数量，即使是空闲的线程
- **maximumPoolSize**：线程池允许的最大线程数量
- **keepAliveTime**：当线程池中的线程数量大于corePoolSize的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime
- **unit**：keepAliveTime的时间单位
- **workQueue**：等待队列，当任务提交时，如果线程池中的线程数量大于等于corePoolSize的时候，把该任务封装成一个Work对象放入等待队列
- **threadFactory**：定义线程池中创建的线程，如线程名称、优先级等
- **handler**：线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize时的任务拒绝策略

当有新任务在execute()方法提交时，会执行以下判断：

1. 如果运行的线程少于corePoolSize，则创建新线程来处理任务，即使线程池中的其他线程是空闲的；
2. 如果线程池中的线程数量大于等于corePoolSize且小于maximumPoolSize，当workQueue未满的时候任务添加到workQueue中，当workQueue满时才创建新的线程去处理任务；
3. 如果设置的corePoolSize和maximumPoolSize相同，则创建的线程池的大小是固定的，这时如果有新任务提交，若workQueue未满，则将请求放入workQueue中，等待有空闲的线程去从workQueue中取任务并处理；
4. 如果运行的线程数量大于等于maximumPoolSize，且此时workQueue已经满了，则通过handler所指定的策略来处理任务。

所以，任务提交时，判断的顺序为corePoolSize -> workQueue -> maximumPoolSize。

#### 等待队列

当提交一个新的任务到线程池以后, 线程池会根据当前线程池中正在运行着的线程的数量来决定对该任务的处理方式，主要有以下几种处理方式:

**直接切换**：这种方式常用的队列是SynchronousQueue。SynchronousQueue是一个不存储元素的阻塞队列。每一个put操作必须等待一个take操作，否则不能继续添加元素。

**使用无界队列**：一般使用基于链表的阻塞队列LinkedBlockingQueue。如果使用这种方式，那么线程池中能够创建的最大线程数就是corePoolSize，而maximumPoolSize就不会起作用了。当线程池中所有的核心线程都是RUNNING状态时，这时一个新的任务提交就会放入等待队列中。

使用无界队列时下面几点需要注意：

1）当线程池中的线程数达到corePoolSize后，新任务将在无界队列中等待，因此线程池中的线程数不会超过corePoolSize。

2）使用无界队列时maximumPoolSize将是一个无效参数。

3）使用无界队列时keepAliveTime将是一个无效参数。

4）由于使用无界队列，运行中的FixedThreadPool（未执行方法shutdown()或shutdownNow()）不会拒绝任务（不会调用RejectedExecutionHandler.rejectedExecution方法）。

**使用有界队列**：一般使用ArrayBlockingQueue。使用该方式可以将线程池的最大线程数量限制为maximumPoolSize，这样能够降低资源的消耗，但同时这种方式也使得线程池对线程的调度变得更困难，因为线程池和队列的容量都是有限的值，所以要想使线程池处理任务的吞吐率达到一个相对合理的范围，又想使线程调度相对简单，并且还要尽可能的降低线程池对资源的消耗，就需要合理的设置这两个数量。

如果要想降低系统资源的消耗（包括CPU的使用率，操作系统资源的消耗，上下文环境切换的开销等）, 可以设置较大的队列容量和较小的线程池容量, 但这样也会降低线程处理任务的吞吐量。

如果提交的任务经常发生阻塞，那么可以考虑通过调用 setMaximumPoolSize() 方法来重新设定线程池的容量。

如果队列的容量设置的较小，通常需要将线程池的容量设置大一点，这样CPU的使用率会相对的高一些。但如果线程池的容量设置的过大，则在提交的任务数量太多的情况下，并发量会增加，那么线程之间的调度就是一个要考虑的问题，因为这样反而有可能降低处理任务的吞吐量。

![2333](https://upload-images.jianshu.io/upload_images/16015500-d9e006df2b789d2a.png?imageMogr2/auto-orient/strip|imageView2/2/w/1193)

通常使用使用Executors的工厂方法来创建ExecutorService。

```java
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(10); 
ExecutorService singleaThreadPool = Executors.newSingleThreadExecutor();
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);
```

#### newCachedThreadPool

缓存型线程池，先查看池中有没有以前建立的线程，如果有，就重用，如果没有，就建一个新的线程加入池中。如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。

newCachedThreadPool的源码如下：

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

> - corePoolSize = 0，maximumPoolSize = Integer.MAX_VALUE，即线程数量几乎无限制
> - keepAliveTime = 60s，线程空闲60s后自动结束
> - workQueue 为 SynchronousQueue 同步队列，这个队列类似于一个接力棒，入队出队必须同时传递，因为CachedThreadPool线程创建无限制，不会有队列等待，所以使用SynchronousQueue；

可以看到，缓存型线程池允许创建的最大线程数为Integer.MAX_VALUE，当线程空闲60s之后就会回收。

适用场景：快速处理大量耗时较短的任务，如Netty的NIO接受请求时，可使用CachedThreadPool。

缓存型线程池通常用于一些生存期很短的异步型任务，因此在一些面向连接的daemon型server中用得不多。适用于执行很多的短期异步任务的小程序，或者是负载较轻的服务器。

#### newFixedThreadPool

定长线程池，可控制线程最大并发数。如果当前需要执行的任务超过池大小，那么多出的任务处于等待状态，直到有空闲下来的线程执行任务，如果当前需要执行的任务小于池大小，空闲的线程也不会去销毁。

newFixedThreadPool的源码如下：

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
}
```

FixedThreadPool不能随时创建新的线程，任何时间点，最多只能由固定数目的活动线程存在，如果此时有新的线程要建立，只能放在另外的队列中等待。

FixedThreadPool多数针对一些很稳定很固定的正规并发线程，多用于服务器。

适用场景：可用于Web服务瞬时削峰，但需注意长时间持续高峰情况造成的队列阻塞。

> - corePoolSize与maximumPoolSize相等，即其线程全为核心线程，是一个固定大小的线程池，是其优势
> - keepAliveTime = 0 该参数默认对核心线程无效，而FixedThreadPool全部为核心线程
> - workQueue 为LinkedBlockingQueue（无界阻塞队列），队列最大值为Integer.MAX_VALUE。如果任务提交速度持续大余任务处理速度，会造成队列大量阻塞。因为队列很大，很有可能在拒绝策略前，内存溢出。是其劣势；
> - FixedThreadPool的任务执行是无序的；

#### newScheduledThreadPool

调度型线程池，支持定时及周期性任务执行，也是一个固定长度的线程池。

```java
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
}
```

#### newSingleThreadExecutor

线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。如果当前线程意外终止，会创建一个新线程继续执行任务，这和我们直接创建线程不同，也newFixedThreadPool(1)不同.

```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
}
```



#### 工作线程回收

默认情况下，只有线程池中的线程数大于corePoolSize时，核心线程之外的线程才会根据keepAliveTime设置的时间回收。如果线程池中的核心工作线程需要回收，必须满足三个条件：

- 参数allowCoreThreadTimeOut为true
- 该线程在keepAliveTime时间内获取不到任务，即空闲这么长时间
- keepAliveTime时间大于0，即在allowCoreThreadTimeOut设置为true时，ThreadPoolExecutor的keepAliveTime参数必须大于0

需要注意的是，allowCoreThreadTimeOut的设置需要在任务执行之前，一般在new一个线程池后设置。

```java
/**
 * Timeout in nanoseconds for idle threads waiting for work.
 * Threads use this timeout when there are more than corePoolSize
 * present or if allowCoreThreadTimeOut. Otherwise they wait
 * forever for new work.
 */
private volatile long keepAliveTime;

/**
 * If false (default), core threads stay alive even when idle.
 * If true, core threads use keepAliveTime to time out waiting
 * for work.
 */
private volatile boolean allowCoreThreadTimeOut;
```

#### 任务拒绝策略

> New tasks submitted in method execute(Runnable) will be rejected when the Executor has been shut down, and also when the Executor uses finite bounds for both maximum threads and work queue capacity, and is saturated. 

当线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize，如果还有任务到来就会采取任务拒绝策略，通常有以下四种策略：

- ThreadPoolExecutor.AbortPolicy：丢弃任务并抛出RejectedExecutionException异常.
- ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务
- ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常
- ThreadPoolExecutor.DiscardOldestPolicy：丢弃最前面的任务，然后重新执行任务(重复此过程)

线程池默认使用的是ThreadPoolExecutor.AbortPolicy拒绝策略。

#### ThreadFactory