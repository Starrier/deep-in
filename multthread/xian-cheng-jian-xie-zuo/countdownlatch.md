# CountDownLatch

Thread.join\(\) 实现的是一个线程等待另一个线程结束。有时候一个线程可能只需要等待其他线程执行的特定操作结束即可，而不必等待这些线程终止。我们可以使用条件变量（Condition），也可以直接使用工具类 CountDownLatch\(java.util.concurrent.CountDownLatch\)。

CountDownLatch 可以用来实现一个（或者多个）线程等待其他线程完成一组特定的操作之后才继续运行。这组操作称为**先决操作**。

CountDownLatch 的使用是一次性的：一个 CountDownLatch 实例只能够实现一次等待和唤醒。

如果 CountDownLatch 内部计数器由于程序的错误而永远无法达到 0，那么响应实例上的等待线程会一直处于 WAITING 状态，解决方法：

1. 确保素有 CountDownLatch.countDown\(\) 调用都位于代码中正确的位置。
2. 等待线程在等待先决操作完成的时候指定一个时间限制。此时我们可以使用 CountDownLatch.await\(\) 的另一个版本，其声明如下：

```java
public boolean await(long timeout,TimeUnit unit)throws InterruptedException
```

CountDownLatch.await\(long,TimeUnit\) 允许指定一个超时时间，在该事件内如果相应 CountDownLatch 实例的计数器值仍然达到 0，那么所有执行该实例的 await 方法的线程都会被唤醒。该方法的返回值可用于区分其返回是否是由于等待超时。

{% hint style="warning" %}
 CountDownLatch 内部计数器值达到 0 后，其值就恒定不变了，后续执行该 CountDownLatch 实例的 await 方法的任何一个线程都不会被暂停。为了避免等待线程永远被暂停，CountDownLatch.countDown\(\) 调用必须放在代码中总是可以被执行到的地方，比如 finally 块中。
{% endhint %}

CountDownLatch 的构造器参数既可以表示先决操作的数量，也可以表示先决操作需要被执行的次数。

```java
public class CyclicBarrierTest {

    static CyclicBarrier c = new CyclicBarrier(2);

    public static void main(String[] args) throws InterruptedException , BrokenBarrierException {
         Thread thread= new Thread(()->{
             try {
                 c.await();
             } catch (Exception e) {
                 e.printStackTrace();
             }
         });
         thread.start();
         thread.interrupt();
        try {
            c.await();
        } catch (Exception e) {
            e.printStackTrace();
            System.out.println(c.isBroken());
        }
    }
}

```

