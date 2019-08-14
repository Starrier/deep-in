---
title: start-run
date: 2019-02-16 11:03:22
tags: thread
index_img: ../post_img/start-run.jpeg
---

## 多线程 start() 和 run() 方法的区别

### 1. start()

 1. 使该线程开始执行，Java 虚拟机调用该线程的 run 方法。
 2. 结果是两个线程并发地运行，当前线程（从调用返回给 start() 方法）和另一个线程（执行其 run ()）。
 3. 多次启动一个线程是非法的。特别是当线程已经结束执行后，不能再重新启动。

用 start() 方法来启动线程，真正实现了多线程运行，这时无需等待 run() 方法体代码执行完毕而直接继续执行下面的代码。通过调用 Thread 类的 start() 方法来启动一个线程，这是此线程就绪（可运行）状态，并没有运行，一旦得到 CPU 时间片，就开始执行 run() 方法，这里方法 run() 称为线程体，它包含了要执行的这个线程的内容， run() 方法运行结束，此线程随机终止。

### 2. run()

1. 如果该线程是使用独立的 Runnable 运行对象构造的，则调用该 Runnable 对象的 run() ;否则不执行任何操作并返回。
1. Thread 之类应该重写该方法。

### 总结

调用 start() 方法可以启动线程，而 run() 方法只是 Thread 的一个普通方法调用，还是在主线程里执行。start() 会导致 run() 方法被调用，run() 方法中的内容成为线程体，他就是这个线程所需要执行的工作。

用 start() 启动线程实现了真正意义上的启动线程，此时会出现异步执行的效果，即在线程 的创建和启动中所述的随机性。而如果使用 run() 方法来启动线程，就不是异步执行了，而是同步执行，不会达到使用线程的意义。

## Test

#### start()

```java
public class JavaTest {
    public static void main(String[] args) {
         Thread thread =new Thread(()->pong());
         thread.start();
         System.out.println("ping");
    }
    static void pong() {
        System.out.println("pong");
    }
}
```

#### 运行结果

```text
ping
pong
```

#### run()


```java
public class JavaTest2 {
    public static void main(String[] args) {
        Thread thread1 = new Thread(() -> pong());
        thread1.run();
        System.out.println("ping");
    }
    static void pong() {
        System.out.println("pong");
    }
}
```

#### 运行结果

```text
pong
ping
```

### 线程状态说明

1. 线程的实现有两种方式，一种是继承 Thread 类，二是实现 Runnable 接口。但不管怎么样，当我们 new 了 thread 实例之后，线程就进入了初始状态。
2. 当该对象调用 start() 方法，就进入可运行状态。
3. 进入可运行状态后，当该对象被操作系统选中，获得 CPU，时间片就会进入运行状态。
4. 进入运行状态后 case 就比较多，大致有如下情形：run() 方法或者 mian() 方法结束后，线程就会进入终止状态；当线程调用了自身的 sleep（） 方法或者其他线程的 join （） 方法，就会进入阻塞状态（该状态即停止当前线程，但并不释放所占用的资源）。
5. 当 sleep() 方法结束或者 join() 结束后，该线程进入可运行状态，继续等待 OS 分配时间片；当线程进入可，运行状态（注意，还没有运行），发现将要调用的资源被锁住（synchronize，lock），将会立即进入锁池状态，等待获取锁标记（这时的锁池里也许已经有了其他线程池在等待获取锁标记，这是它们处于队列状态，即先到先得），一旦线程获得锁标记后，就转入可运行状态，等待 OS 分配 CPU 时间片；当线程调动 wait() 方法后会进入等待队列（进入这个状态会释放所占用的所有资源，与阻塞状态不同）。
6. 进入这个状态之后，是不能自动唤醒的，必须依靠其他线程调用 notify() 或者 notifyAll() 方法才能被唤醒（由于 notify() 只是唤醒一个线程，但我们又不能确定具体唤醒的是哪个线程，也许我们需要唤醒的线程不能被唤醒，因此在实际使用中，一般都用 nofityAll() 方法，唤醒所有线程）。
7. 线程被唤醒之后会进入锁池，等待获取锁标记。当线程调用 stop() 方法，即,线程进入消亡状态，但由于 stop() 方法是不安全的，因此不鼓励大家使用，大家可以通过 run() 方法里面的条件变量实现线程的 stop()。
