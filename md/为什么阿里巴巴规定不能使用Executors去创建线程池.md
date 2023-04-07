## 为什么阿里巴巴规定不能使用Executors去创建线程池？

```
【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这
样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

说明：Executors 返回的线程池对象的弊端如下：

1） FixedThreadPool 和 SingleThreadPool：
允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。
2） CachedThreadPool：
允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。
```

```java
// 队列使⽤LinkedBlockingQueue，队列⻓度为 Integer.MAX_VALUE，可能造成堆积，导致OOM
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}


// 队列使⽤LinkedBlockingQueue，队列⻓度为 Integer.MAX_VALUE，可能造成堆积，导致OOM
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}


//线程池⾥⾯允许最⼤的线程数是Integer.MAX_VALUE，可能会创建过多线程，导致OOM
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}


//线程池⾥⾯允许最⼤的线程数是Integer.MAX_VALUE，可能会创建过多线程，导致OOM
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
    return new ScheduledThreadPoolExecutor(corePoolSize);
}
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```

