# join

Thread.join\(\) 可以使当前线程等待目标线程结束之后才继续运行。Thread.join\(\) 还有另一个如下声明版本：

```java
public final void join (long millis) throws InterruptedException
```

join\(long\) 允许我们指定一个超时时间，如果目标线程没有在指定时间内终止，那么当前线程也会继续运行。join\(long\) 实际上就是使用了 wait/notify 来实现的。

```java
   public final synchronized void join(long millis)
    throws InterruptedException {
        long base = System.currentTimeMillis();
        long now = 0;

        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (millis == 0) {
            while (isAlive()) {
                wait(0);
            }
        } else {
            while (isAlive()) {
                long delay = millis - now;
                if (delay <= 0) {
                    break;
                }
                wait(delay);
                now = System.currentTimeMillis() - base;
            }
        }
    }
```

join\(long\) 是一个同步方法。它检测到目标线程未结束的时候会调用 wait 方法来暂停当前线程，知道目标线程已经终止。这里，当前线程等于相当于等待线程，其所需保护条件是“目标线程已终止”（Thread.isAlive\(\) 返回值是 false）。Java 虚拟机会在目标线程的 run\(\) 方法运行结束后执行该线程（对象）的 notifyAll 方法来通知所有等待线程。可见这里的目标线程充当了同步对象的角色，而 Java 虚拟机中 notifyAll 方法的执行线程则是通知线程。另外，join\(long\) 正是上述所展示的实现等待超时控制的方法来使用 wait\(long\) 方法的。

Thread.join\(\) 调用相当于 Thread.join\(0\)  调用。

```java
public class Join {
    private static class Domino  implements Runnable {
        private  Thread thread;

        public Domino(Thread thread) {
            this.thread=thread;
        }

        @Override
        public void run() {
            try{
                thread.join();
            }catch (InterruptedException e){
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " terminate.");
        }
    }

    public static void main(String[] args) throws Exception {

        Thread previous =Thread.currentThread();
        for (int i = 0; i < 10; i++) {
            Thread thread = new Thread(new Domino(previous), String.valueOf(i));
            thread.start();;
            previous =thread;
        }
        TimeUnit.SECONDS.sleep(5);
        System.out.println(Thread.currentThread().getName() + "ternimate");
    }
}

```

