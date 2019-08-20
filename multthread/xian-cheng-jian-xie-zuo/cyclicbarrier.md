# CyclicBarrier

CycliCBarrier 内部使用了一个条件变量 trip 来实现等待/通知。CyclicBarrier 内部实现使用了分代（Generation）的概念用于表示 CyclicBarrier 实例是可以重复使用的。除最后一个线程外的任何一个参与方都相当于一个等待线程，这些线程所使用的保护条件是“当前分代内，尚未执行 await 方法的参与方个数（parties）为 0”。当前分代的初始状态是 parties 等于参与方总数（通过构造器中的 parties 参数指定）。CyclicBarrier.await\(\) 每被执行一次会使相应实例的 parties 值减少 1 。最后一个线程相当于通知线程，它执行 CyclicBarrier.await\(\) 会使相应实例的 parties 值变为 0，此时该线程会先执行 barrierAction.run\(\)，然后再指向 trip.signalAll\(\) 来唤醒所有等待线程。接着，开始下一个分代，即，使得 CycllicBarrier 的 parites 值又重新恢复为其初始值。

典型应用场景

1. 使迭代算法并发化。
2. 在测试代码中模拟高并发。

{% hint style="danger" %}
如果代码对 CyclicBarrier.await\(\) 调用不是放在一个循环中，并且使用 CyclicBarrier 的目的也不是为了模拟高并发操作，那么此时对 CyclicBarrier 的使用可能是一种滥用。

可以用 Thread.join\(\)/CountDownLatch 来执行等待线程。
{% endhint %}

```java
public class BankWaterService implements Runnable {

    private CyclicBarrier c = new CyclicBarrier(4, this);

    private Executor executor = Executors.newFixedThreadPool(4);

    private ConcurrentHashMap<String, Integer> sheetBankWaterCount = new ConcurrentHashMap<String, Integer>();

    private void count() {
        for (int i = 0; i < 4; i++) {
            executor.execute(()->{
                sheetBankWaterCount.put(Thread.currentThread().getName(),1);
                try {
                    c.await();
                } catch (InterruptedException| BrokenBarrierException e) {
                    e.printStackTrace();
                }
            });
        }
    }


    @Override
    public void run() {
        int result=0;
        for (Map.Entry<String, Integer> sheet : sheetBankWaterCount.entrySet()) {
            result+=sheet.getValue();
        }
        sheetBankWaterCount.put("result", result);
        System.out.println(result);
    }

    public static void main(String[] args) {
        BankWaterService bankWaterService=new BankWaterService();
        bankWaterService.count();
    }
}

```

