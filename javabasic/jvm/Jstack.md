# Jstack

Jstack 是 Java 虚拟机自带的一种堆栈跟踪工具。

## 功能

jstack 用于生成 java 虚拟机当前时刻的线程快照。线程快照是当前java虚拟机内每一条线程正在执行的方法堆栈的集合，生成线程快照的主要目的是定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待等。 线程出现停顿的时候通过jstack来查看各个线程的调用堆栈，就可以知道没有响应的线程到底在后台做什么事情，或者等待什么资源。 如果java程序崩溃生成core文件，jstack工具可以用来获得core文件的java stack和native stack的信息，从而可以轻松地知道java程序是如何崩溃和在程序何处发生问题。另外，jstack工具还可以附属到正在运行的java程序中，看到当时运行的java程序的java stack和native stack的信息, 如果现在运行的java程序呈现hung的状态，jstack是非常有用的。

Jstack 主要用来查看 Java 线程调用堆栈的，可以用来分析线程问题（如死锁）

## 线程状态

想要通过 jstack 命令用来分析线程的情况。

- new ，未启动的，不会出现在 Dump 中
- runnable ，在虚拟机内执行
- blocked，受阻塞，并等待监视器锁
- wating，无限期等待另一个线程执行特定操作
- TIMED_WAITING 有时限的等待另一个线程的特定操作
- terminated 已退出的

## Monitor

在多线程的 Java 语言中，实现线程之间的同步，就要说说 Monitor。Monitor 是 Java 中用以实现线程之间的互斥与协作的主要手段，可以看成是对象或 Class 锁。每一个对象都有且仅有一个 monitor。

#### 进入区（Entry Set)

表示线程通过 synchronized 要求获取对象的锁。如果对象未被锁住，则进入拥有者（The Owner)。否则，进入等待区（Wait Set）.一旦对象锁被其他下城释放。立即参与竞争。

拥有者（The Owner） 表示某一线程成功竞争到对象锁。

等待区（Wait Set）表示线程通过对象的 wait 方法，释放对象的锁，病在等待区等待被唤醒。

从图中可以看出，一个 Monitor在某个时刻，只能被一个线程拥有，该线程就是 “Active Thread”，而其它线程都是 “Waiting Thread”，分别在两个队列 “ Entry Set”和 “Wait Set”里面等候。在 “Entry Set”中等待的线程状态是 “Waiting for monitor entry”，而在 “Wait Set”中等待的线程状态是 “in Object.wait()”。 先看 “Entry Set”里面的线程。我们称被 synchronized保护起来的代码段为临界区。当一个线程申请进入临界区时，它就进入了 “Entry Set”队列。对应的 code就像：

```java
synchronized(obj){

}
```

### 调用修饰

表示线程在方法调用时,额外的重要的操作。线程Dump分析的重要信息。修饰上方的方法调用。

1. locked <地址> 目标：使用synchronized申请对象锁成功,监视器的拥有者。

通过 synchronized 关键字，成功获取到了对象的锁，成为监视器的拥有者，在临界区内操作。对象锁是可以重入的。

2. waiting to lock <地址> 目标：使用synchronized申请对象锁未成功,在迚入区等待。

通过 synchronized 关键字，没有获取到了对象的锁，线程在监视器的进入区等待。在调用栈顶出现，线程状态为 Blocked。

3. waiting on <地址> 目标：使用synchronized申请对象锁成功后,释放锁幵在等待区等待。

通过 synchronized 关键字，成功获取到了对象的锁后，调用了 wait 方法，进入对象的等待区等待。在调用栈顶出现线程状态为 WAITING 或 TIMED_WATING。

4. parking to wait for <地址> 目标

park 是基本的线程阻塞原语，不通过监视器在对象上阻塞。随 concurrent 包会出现的新的机制，synchronized 体系不同。

## 线程状态产生的原因

runnable:状态一般为RUNNABLE。

in Object.wait():等待区等待,状态为WAITING或TIMED_WAITING。

waiting for monitor entry:进入区等待,状态为BLOCKED。

waiting on condition:等待区等待、被park。

sleeping:休眠的线程,调用了Thread.sleep()。


Wait on condition 该状态出现在线程等待某个条件的发生。具体是什么原因，可以结合 stacktrace来分析。 最常见的情况就是线程处于sleep状态，等待被唤醒。 常见的情况还有等待网络IO：在java引入nio之前，对于每个网络连接，都有一个对应的线程来处理网络的读写操作，即使没有可读写的数据，线程仍然阻塞在读写操作上，这样有可能造成资源浪费，而且给操作系统的线程调度也带来压力。在 NewIO里采用了新的机制，编写的服务器程序的性能和可扩展性都得到提高。 正等待网络读写，这可能是一个网络瓶颈的征兆。因为网络阻塞导致线程无法执行。一种情况是网络非常忙，几 乎消耗了所有的带宽，仍然有大量数据等待网络读 写；另一种情况也可能是网络空闲，但由于路由等问题，导致包无法正常的到达。所以要结合系统的一些性能观察工具来综合分析，比如 netstat统计单位时间的发送包的数目，如果很明显超过了所在网络带宽的限制 ; 观察 cpu的利用率，如果系统态的 CPU时间，相对于用户态的 CPU时间比例较高；如果程序运行在 Solaris 10平台上，可以用 dtrace工具看系统调用的情况，如果观察到 read/write的系统调用的次数或者运行时间遥遥领先；这些都指向由于网络带宽所限导致的网络瓶颈。

[https://blog.csdn.net/zhangjunli/article/details/80436447](https://blog.csdn.net/zhangjunli/article/details/80436447)