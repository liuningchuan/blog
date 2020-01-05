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

- **corePoolSize**：线程池的核心线程数量，即使是空闲的线程
- **maximumPoolSize**：线程池允许的最大线程数量
- **keepAliveTime**：当线程池中的线程数量大于corePoolSize的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime
- **unit**：keepAliveTime的时间单位
- **workQueue**：等待队列
- **threadFactory**：定义线程池中创建的线程，如线程名称、优先级等
- **handler**：线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize时的任务拒绝策略


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

可以看到，缓存型线程池允许创建的最大线程数为Integer.MAX_VALUE，当线程空闲60s之后就会回收。

缓存型线程池通常用于一些生存期很短的异步型任务，因此在一些面向连接的daemon型server中用得不多。

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