# 线程池

### 作用

在程序启动的时候就创建若干线程来响应处理，它们被称为线程池，里面的线程叫工作线程

1. 降低资源消耗，通过重复利用已经创建的线程降低线程创建和销毁造成的消耗。
2. 提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行。
3. 提高线程的可管理性。

### 常用的线程池

1. Excutors.newCachedThreadPool\(\)                     无限线程池
2. Excutors.newFixedThreadPool\(nThreads\)        创建固定大小的线程池
3. Excutors.newSingleThreadPool\(\)                        创建单个线程的线程池
4. Excutors.newScheduledThreadPool\(\) 

### 实现

实际都是用 ThreadPoolExecutor 类实现

```java
ThreadPoolExecutor(int corePoolSize,int maxmumPoolSize,
                   long KeepAliveTime,TimeUnit unit,
                   BlockingQueue<Runnable> workQueue,
                   RejectedExcutionHandler handler)
```

* corePoolSize             线程池的基本大小
* maxmumPoolSize    线程池最大线程大小
* keepAliveTime          线程空闲后的存活时间
* unit                              线程空闲后的存活时间
* workQueue                 用于存放任务的阻塞队列
* handler                        当队列和最大线程池都满时的饱和策略

#### 使用方法

```java
threadPool.excute(new Job());
```

将任务提交到线程池中。核心逻辑此时为 excute\(\) 函数。

#### 线程池中的状态

```java
private static final int RUNNING = -1<<COUNT_BITS;
private static final int SHOUTDOWN = 0<<COUNT_BITS;
private static final int STOP = 1<<COUNT_BITS;
private static final int TIDYING = 2<<COUNT_BITS;
private static final int TERMINATED = 3<<COUNT_BITS;
```



* `RUNNING` 自然是运行状态，指可以接受任务执行队列里的任务
* `SHUTDOWN` 指调用了 `shutdown()` 方法，不再接受新任务了，但是队列里的任务得执行完毕。
* `STOP` 指调用了 `shutdownNow()` 方法，不再接受新任务，同时抛弃阻塞队列里的所有任务并中断所有正在执行任务。
* `TIDYING` 所有任务都执行完毕，在调用 `shutdown()/shutdownNow()` 中都会尝试更新为这个状态。
* `TERMINATED` 终止状态，当执行 `terminated()` 后会更新为这个状态。

![ThreadStaus](../../.gitbook/assets/threadstatus.jpg)

1. 获取当前线程池的状态。
2. 当前线程数量小于 coreSize 时创建一个新的线程运行。
3. 如果当前线程处于运行状态，并且写入阻塞队列成功。
4. 双重检查，再次获取线程状态；如果线程状态变了（非运行状态）就需要从阻塞队列移除任务，并尝试判断线程是否全部执行完毕。同时执行拒绝策略。
5. 如果当前线程池为空就新创建一个线程并执行。
6. 如果在第三步的判断为非运行状态，尝试新建线程，如果失败则执行拒绝策略。

