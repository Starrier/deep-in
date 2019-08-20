# Semaphore

Semaphore 类使一个计数信号量，必须由获取它们的线程释放，通常用于限制可以访问某些资源（物理或逻辑）线程数目。

默认是非公平锁。

一个信号量可以有 3 种操作，且他们都是原子的：

1. 初始化
2. 增加：会让一个线程解除阻塞
3. 减少：会让一个线程进入阻塞



```java
public class SemaphoreTest {
    private static final int THREAD_COUNT=30;

    private static ExecutorService threadPool = Executors.newFixedThreadPool(THREAD_COUNT);

    private static Semaphore semaphore = new Semaphore(10);

    public static void main(String[] args) {
        for (int i = 0; i < THREAD_COUNT; i++) {
            threadPool.execute(()->{
                try {
                    semaphore.acquire();
                    System.out.println("save data");
                    System.out.println("Thread name "+System.currentTimeMillis());
                    semaphore.release();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        threadPool.shutdown();
    }
}
```

