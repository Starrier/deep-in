# Exchanger

```java
public class ExchangerTest {

    private static final Exchanger<String> exchagner = new Exchanger<>();

    private static ExecutorService threadPool = Executors.newFixedThreadPool(2);

    public static void main(String[] args) {
        threadPool.execute(()->{
            try {
                String A = "银行流水账 A";
                System.out.println("Exchanger Before :" + A);
                exchagner.exchange(A);
                System.out.println("Exchanger After :" + A);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        threadPool.execute(()->{
            try {
                String B = "银行流水账 B";
                System.out.println("Exchanger Before :" + B);
                exchagner.exchange(B);
                System.out.println("Exchanger After :" + B);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        threadPool.shutdown();
    }

}

```

#### 结果：

```text
Exchanger Before :银行流水账 A
Exchanger Before :银行流水账 B
Exchanger After :银行流水账 B
Exchanger After :银行流水账 A
```

