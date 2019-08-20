# synchronize

Java 任何 一个对象都有唯一一个与之关联的锁。这种锁被称为监视器（Monitor）或者（Intrinsic Lock）。内部锁是一种排他锁，它能够保障原子性、可见性和有序性。

内部锁通过 synchronize 关键字实现，可用来修饰方法以及代码块（｛｝ 包裹的代码）

作为锁句柄的变量通常采用 final 修饰。这是因为锁句柄变量的值一旦改变，会导致执行同一个同步快的多个新城实际上使用不同的锁，从而导致竞态。因此，我们通常使用 private 修饰锁句柄的常量。

```java
private final Object lock = new Object();
```

```java
public class synchronizeTest{
    public static synchronized void testMethod(){} 
}
//等价于
public class synchronizeTest{
    public static void testMethod(){
          synchronized(synchronizeTest.class){}
    }
}
```

内部锁的调度

Java 虚拟机会为每个内部锁分配一个入口集（Entry Set），入口集中的线程被称为相应内部锁的等待线程。Java 虚拟机对内部锁的调度仅支持非公平调度。

