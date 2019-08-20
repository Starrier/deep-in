# synchronized 和 lock

### 区别

* 用法区别
* 性能区别
* 锁机制区别

synchronize 与 Lock 都是可重入锁，同一个进程再次进入同步代码的时候，可以使用自己已经获取到的锁。

synchronize 是悲观锁机制，独占锁，而 Lock.ReentrantLock 则每次不加锁，而是假设没又冲突二区完成某种操作，如果因为冲突失败就重试，直到成功为止。

ReentrantLock获取锁定与三种方式：

1. lock\(\)，如果获取了锁，立即返回，如果别的线程持有锁，当前线程则一直处于休眠状态，直到获取锁。
2. tryLock\(\),如果获取了锁，立即返回 true，如果别的线程正在持有锁，返回 false。
3. tryLock\(long timeout,TimeUnit unit\)，如果获取了锁，立即返回 true，如果有别的线程正在持有锁，会等待参数给定的时间，在等待的过程中，如果获取了锁，立即返回 true，如果等待超时，返回 false。
4. lockInterruptibly，如果获取了锁，立即返回，如果没有获取锁，当前线程处于休眠状态，直到获得锁，或者当前线程被其他线程中断。

ReentrantLock 拥有Synchronized相同的并发性和内存语义，此外还多了 锁投票，定时锁等候和中断锁等候  
线程A和B都要获取对象O的锁定，假设A获取了对象O锁，B将等待A释放对O的锁定，  
如果使用 synchronized ，如果A不释放，B将一直等下去，不能被中断  
如果 使用ReentrantLock，如果A不释放，可以使B在等待了足够长的时间以后，中断等待，而干别的事情  
  
  
2、synchronized是在JVM层面上实现的，不但可以通过一些监控工具监控synchronized的锁定，而且在代码执行时出现异常，JVM会自动释放锁定，但是使用Lock则不行，lock是通过代码实现的，要保证锁定一定会被释放，就必须将unLock\(\)放到finally{}中  
  
3、在资源竞争不是很激烈的情况下，Synchronized的性能要优于ReetrantLock，但是在资源竞争很激烈的情况下，Synchronized的性能会下降几十倍，但是ReetrantLock的性能能维持常态；

5.0的多线程任务包对于同步的性能方面有了很大的改进，在原有synchronized关键字的基础上，又增加了ReentrantLock，以及各种Atomic类。了解其性能的优劣程度，有助与我们在特定的情形下做出正确的选择。

总体的结论先摆出来：

synchronized：   
在资源竞争不是很激烈的情况下，偶尔会有同步的情形下，synchronized是很合适的。原因在于，编译程序通常会尽可能的进行优化synchronize，另外可读性非常好，不管用没用过5.0多线程包的程序员都能理解。

ReentrantLock:   
ReentrantLock提供了多样化的同步，比如有时间限制的同步，可以被Interrupt的同步（synchronized的同步是不能Interrupt的）等。在资源竞争不激烈的情形下，性能稍微比synchronized差点点。但是当同步非常激烈的时候，synchronized的性能一下子能下降好几十倍。而ReentrantLock确还能维持常态。

Atomic:   
和上面的类似，不激烈情况下，性能比synchronized略逊，而激烈的时候，也能维持常态。激烈的时候，Atomic的性能会优于ReentrantLock一倍左右。但是其有一个缺点，就是只能同步一个值，一段代码中只能出现一个Atomic的变量，多于一个同步无效。因为他不能在多个Atomic之间同步。

