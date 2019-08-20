# Lock

Java 锁 分为 内部锁（Intrinsic Lock） 和显示锁（Explicit Lock）。内部锁是通过 synchronized 关键字实现的；显示锁是通过 java.concurrent.locks.Lock 接口的实现类（java.concurrent.locks.ReentrantLock）实现的。

锁能够保护共享数据以实现线程安全，其作用包括保障 **原子性**。保障 **可见性** 和保障 **有序性**。

保障原子性：通过互斥实现，即一个锁一次只能被一个线程持有。

保障可见性：通过写线程冲刷处理器缓存和读线程刷新处理器缓存这两个动作实现。

保障有序性：写线程在临界区中所执行的一系列操作在读线程所执行的临界区看起来像是完全按照源代码执行的，即读线程读这些操作的感知顺序与源代码顺序一直。这是锁对原子性和可见性的保障结果。

1. 可重入性
2. 锁的争用与调度
3. 锁的粒度

锁的开销

1. 锁可能导致上下文切换。多个线程争用排他性资源可能会导致上下文切换。
2. 锁的不正确使用会导致线程活性故障。
3. 锁泄露。
4. 锁的不正确使用还可能会导致死锁、锁死等线程活性故障。

```java
Lock lock =new ReentrantLock();
     lock.lock();
     try{}
     finally{
           lock.unlock();
           }
```

在 finally 中释放锁，目的是保证在获取到锁之后，最终能释放锁。不要将获取锁的过程写在 try 中，因为如果在获取锁（自定义锁的实现）时发现异常，异常抛出的同时，也会导致锁无故释放。

| 返回类型 | 方法名 | 描述 |
| :--- | :--- | :--- |
| void | lock | 获取锁 |
| void | lockInterruptibly | 如果当前线程未被中断，则获取锁 |
| Condition  | newCondition | 返回绑定到此 Lock 实例的新 Condition 实例 |
| boolean | tryLock | 仅在调用时锁位空闲状态才获取该锁 |
| boolean | tryLock\(long time,TimeUnit unit\) | 如果在给定等待时间内空闲，并且当前线程未被中断，则获取该锁 |
| void | unlock | 释放锁 |

