---
description: (Compare And Swap)
---

# CAS

在计算机科学中，比较和交换\(Compare And Swap\) 是用于实现多线程同步的原子指令。它将内存位置的内容与给定值进行比较，只有在相同的情况下，将该内存位置的内容修改位新的给定值。这是作为单个原子操作完成的。原子性保证新值基于最新信息计算；如果该值在同一时间被另一个线程更新，则写入将失败。操作结果必须说明是否进行替换；这可以通过一个简单的布尔值响应（这个变体通常称为比较和设置），或通过返回从内测位置读取的值来完成。

## 无锁的概念 <a id="&#x65E0;&#x9501;&#x7684;&#x6982;&#x5FF5;"></a>

在谈论无锁概念时，总会关联起乐观派与悲观派，对于乐观派而言，他们认为事情总会往好的方向发展，总是认为坏的情况发生的概率特别小，可以无所顾忌地做事，但对于悲观派而已，他们总会认为发展事态如果不及时控制，以后就无法挽回了，即使无法挽回的局面几乎不可能发生。这两种派系映射到并发编程中就如同加锁与无锁的策略，即加锁是一种悲观策略，无锁是一种乐观策略，因为对于加锁的并发程序来说，它们总是认为每次访问共享资源时总会发生冲突，因此必须对每一次数据操作实施加锁策略。而无锁则总是假设对共享资源的访问没有冲突，线程可以不停执行，无需加锁，无需等待，一旦发现冲突，无锁策略则采用一种称为CAS的技术来保证线程执行的安全性，这项CAS技术就是无锁策略实现的关键，下面我们进一步了解CAS技术的奇妙之处。

### 参数

*  V   表示要更新的变量
*  E   表示预期值
*  N   表示新值

![](../../.gitbook/assets/cas.png)

### Unsafe里的CAS 操作相关

CAS是一些CPU直接支持的指令，也就是我们前面分析的无锁操作，在Java中无锁操作CAS基于以下3个方法实现，在稍后讲解Atomic系列内部方法是基于下述方法的实现的。

```java
public navtive boolean compareAndSwapObject(Object object,long offset,
                                            Object expected,Object x);
```

* object          给定对象
* offset          对象内存偏移量
* expected    期望值
* x                  要设置的值

```java
public final native boolean compareAndSwapObject(Object object, long offset,
                                                 Object expected, Object x);
public final native boolean compareAndSwapInt(Object object, long offset,
                                               int expected,int x);
public final native boolean compareAndSwapLong(Object o, long offset,
                                                long expected,long x); 
```

###  挂起与恢复 

将一个线程进行挂起是通过park方法实现的，调用 park后，线程将一直阻塞直到超时或者中断等条件出现。unpark可以终止一个挂起的线程，使其恢复正常。Java对线程的挂起操作被封装在 LockSupport类中，LockSupport类中有各种版本pack方法，其底层实现最终还是使用Unsafe.park\(\)方法和Unsafe.unpark\(\)方法

```java
//线程调用该方法，线程将一直阻塞直到超时，或者是中断条件出现。
public native void park(boolean isAbsolute,long time);
```

```java
/终止挂起的线程，恢复正常
.java.util.concurrent包中挂起操作都是在LockSupport类实现的，
public native void unpark(Object thread);
```



