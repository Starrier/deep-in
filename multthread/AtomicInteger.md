# AtomicInteger

AtomicInteger 是一个提供原子操作的 Integer 类，通过线程安全的方式操作加减。因此适用于高并发情况下使用。

AtomicInteger 是在使用非阻塞算法实现并发控制，在一些高并发程序中非常适合，但并不是每一种场景都适用。

AtomicInteger 使用 value 来保存值，value 是 volatile 的，保证了可见性。

对于 get 方法直接返回 value，对于自增一或添加值使用 CAS 自旋锁，使用了一个死循环，如果 CAS 返回 true 就可以退出循环。 

对于CAS 全称是compare and swap比较和交换,CAS需要三个操作数，一个是变量内存地址，一个是excepted过期值，一个是现在要更新的值，我们操作的时候仅当V符合旧预期的值的时候才能更新我们新的。对于它的自增的操作，首先是卸载一个for循环里面然后获得当前的值，给这个值+1，然后进行cvs要是失败会再次Get\(\)最新值再次写.

原子更新基本类型主要包括3个类：

1. AtomicBoolean 原子更新布尔类型
2. AtomicInteger   原子更新整型
3. AtomicLong       原子更新长整型

AtomicBoolean：原子更新布尔类型 AtomicInteger：原子更新整型 AtomicLong：原子更新长整型 这3个类的实现原理和使用方式几乎是一样的，这里我们以AtomicInteger为例进行分析，AtomicInteger主要是针对int类型的数据执行原子操作，它提供了原子自增方法、原子自减方法以及原子赋值方法等，鉴于AtomicInteger的源码不多，我们直接看源码

```java
public class AtomicInteger extends Number implenments java.io.Serialzable{

        private static final long serialVersionUID = 6214790243416807050L;
        // 获取指针类Unsafe
        private static final Unsafe unsafe = Unsafe.getUnsafe();
        //下述变量value在AtomicInteger实例对象内的内存偏移量
        private static final long valueOffset;

 static {
    try {
       //通过unsafe类的objectFieldOffset()方法，获取value变量在对象内存中的偏移
       //通过该偏移量valueOffset，unsafe类的内部方法可以获取到变量value对其进行取值或赋值操作
        valueOffset = unsafe.objectFieldOffset
            (AtomicInteger.class.getDeclaredField("value"));
    } catch (Exception ex) { throw new Error(ex); }
}
//当前AtomicInteger封装的int变量value private volatile int value;
public AtomicInteger(int initialValue) {
    value = initialValue;
}
public AtomicInteger() {
}
//获取当前最新值， 
public final int get() {
 return value;
  } 
  
//设置当前值，具备volatile效果，方法用final修饰是为了更进一步的保证线程安全。 
public final void set(int newValue) { 
value = newValue;
 } 
//最终会设置成newValue，
//使用该方法后可能导致其他线程在之后的一小段时间内可以获取到旧值，有点类似于延迟加载 
public final void lazySet(int newValue) { 
unsafe.putOrderedInt(this, valueOffset, newValue); } 
//设置新值并获取旧值，底层调用的是CAS操作即unsafe.compareAndSwapInt()方法 
public final int getAndSet(int newValue) { 
return unsafe.getAndSetInt(this, valueOffset, newValue);
 } 
 //如果当前值为expect，则设置为update(当前值指的是value变量) 
 public final boolean compareAndSet(int expect, int update) { 
 return unsafe.compareAndSwapInt(this, valueOffset, expect, update); }
  //当前值加1返回旧值，底层CAS操作 
  public final int getAndIncrement() 
  { return unsafe.getAndAddInt(this, valueOffset, 1); 
  } 
  //当前值减1，返回旧值，底层CAS操作 
  public final int getAndDecrement() {
   return unsafe.getAndAddInt(this, valueOffset, -1); } 
   //当前值增加delta，返回旧值，底层CAS操作 
   public final int getAndAdd(int delta) { 
   return unsafe.getAndAddInt(this, valueOffset, delta); } 
   //当前值加1，返回新值，底层CAS操作
    public final int incrementAndGet() { 
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1; } /
    /当前值减1，返回新值，底层CAS操作 public final int decrementAndGet() { 
    return unsafe.getAndAddInt(this, valueOffset, -1) - 1; } 
    //当前值增加delta，返回新值，底层CAS操作
     public final int addAndGet(int delta) {
      return unsafe.getAndAddInt(this, valueOffset, delta) + delta; } 
      //省略一些不常用的方法.... 
      }

```

通过上述的分析，可以发现AtomicInteger原子类的内部几乎是基于前面分析过Unsafe类中的CAS相关操作的方法实现的，这也同时证明AtomicInteger是基于无锁实现的，这里重点分析自增操作实现过程，其他方法自增实现原理一样。

我们发现AtomicInteger类中所有自增或自减的方法都间接调用Unsafe类中的getAndAddInt\(\)方法实现了CAS操作，从而保证了线程安全，关于getAndAddInt其实前面已分析过，它是Unsafe类中1.8新增的方法，源码如下

```java
//Unsafe类中的getAndAddInt方法
public final int getAndAddInt(Object o, long offset, int delta) { 
        int v;
        do {
            v = getIntVolatile(o, offset); 
           } while (!compareAndSwapInt(o, offset, v, v + delta)); 
        return v; 
 } 
```

通过一个while循环不断的重试更新要设置的值，直到成功为止，调用的是Unsafe类中的compareAndSwapInt方法，是一个CAS操作方法。这里需要注意的是，上述源码分析是基于JDK1.8的，如果是1.8之前的方法，AtomicInteger源码实现有所不同，是基于for死循环的，如下

```java
  //JDK 1.7的源码，由for的死循环实现，并且直接在AtomicInteger实现该方法，  
  public final int incrementAndGet() {
         for (;;) { 
              int current = get(); 
              int next = current + 1; 
         if (compareAndSet(current, next))
              return next; } 
  }
  //JDK1.8后，该方法实现已移动到Unsafe类中，直接调用getAndAddInt方法即可 
```



