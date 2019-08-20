# 线程的阻塞与唤醒

**用 LockSupport 的 park\(\)  和  unpark\(\)**

## suspend与resume

Java废弃 suspend\(\) 去挂起线程的原因，是因为 suspend\(\) 在导致线程暂停的同时，并不会去释放任何锁资源。其他线程都无法访问被它占用的锁。直到对应的线程执行 resume\(\) 方法后，被挂起的线程才能继续，从而其它被阻塞在这个锁的线程才可以继续执行。 但是，如果 resume\(\) 操作出现在 suspend\(\) 之前执行，那么线程将一直处于挂起状态，同时一直占用锁，这就产生了死锁。而且，对于被挂起的线程，它的线程状态居然还是 Runnable。

## wait与notify

wait与notify必须配合synchronized使用，因为调用之前必须持有锁，wait会立即释放锁，notify则是同步块执行完了才释放

## await与singal

Condition类提供，而Condition对象由new ReentLock\(\).newCondition\(\)获得，与wait和notify相同，因为使用Lock锁后无法使用wait方法

## park与unpark

LockSupport是一个非常方便实用的线程阻塞工具，它可以在线程任意位置让线程阻塞。和Thread.suspenf\(\)相比，它弥补了由于resume\(\)在前发生，导致线程无法继续执行的情况。和Object.wait\(\)相比，它不需要先获得某个对象的锁，也不会抛出IException异常。可以唤醒指定线程。

总结

### wait 与 await 区别：

wait 与 notify 必须配合 synchronized 使用，因为调用之前必须持有锁，wait 会立即释放锁，notify 则是同步块执行完了才释放 因为 Lock 没有使用 synchronized 机制，故无法使用 wait 方法区操作多线程，所以使用了 Condition 的 await 来操作

#### Lock 实现主要是基于 AQS，而 AQS 实现则是基于 LockSupport，所以说 LockSupport 更底层，所以使用park 效率会高一些

