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
                          ThreadFactory threadFactory) {
    this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, 
         threadFactory, defaultHandler);
}
```

- **corePoolSize**：线程池的核心线程数量，即使是空闲的线程
- **maximumPoolSize**：线程池允许的最大线程数量
- **keepAliveTime**：当线程池中的线程数量大于corePoolSize的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了keepAliveTime
- **unit**：keepAliveTime的时间单位
- **workQueue**：等待队列
- **threadFactory**：定义线程池中创建的线程，如线程名称、优先级等


通常使用使用Executors的工厂方法来创建ExecutorService。

```java
ExecutorService cachedThreadPool = Executors.newCachedThreadPool();
ExecutorService fixedThreadPool = Executors.newFixedThreadPool(10); 
ExecutorService singleaThreadPool = Executors.newSingleThreadExecutor();
ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);
```

#### newCachedThreadPool

缓存型线程池，先查看池中有没有以前建立的线程，如果有，就重用，如果没有，就建一个新的线程加入池中。如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程。

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

线程池中的工作线程回收需要满足三个条件：

- 参数allowCoreThreadTimeOut为true
- 该线程在keepAliveTime时间内获取不到任务，即空闲这么长时间
- 当前线程池大小 > 核心线程池大小corePoolSize

需要注意的是，allowCoreThreadTimeOut的设置需要在任务执行之前，一般在new一个线程池后设置；在allowCoreThreadTimeOut设置为true时，ThreadPoolExecutor的keepAliveTime参数必须大于0.

> ```java
> /**
>  * Timeout in nanoseconds for idle threads waiting for work.
>  * Threads use this timeout when there are more than corePoolSize
>  * present or if allowCoreThreadTimeOut. Otherwise they wait
>  * forever for new work.
>  */
> private volatile long keepAliveTime;
> 
> /**
>  * If false (default), core threads stay alive even when idle.
>  * If true, core threads use keepAliveTime to time out waiting
>  * for work.
>  */
> private volatile boolean allowCoreThreadTimeOut;
> ```

#### ThreadFactory