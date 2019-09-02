# ThreadLocal

 这个类提供了一个局部线程变量。这些变量不同于其所对应的常规变量，对于常规变量，每个线程只能访问（通过get或set方法）其自身所拥有的，独立初始化变量拷贝。在一个类中，ThreadLocal类型的实例是典型的**私有、静态**（_**private static**_）字段，因为我们可以将其作为线程的关联状态（比如：用户ID或者事务ID）

### ThreadLocal :

1.  get\(\) ：             返回当前线程拷贝的局部线程变量的值。
2. initialValue\(\)： 返回当前线程赋予局部线程变量的初始值。
3. remove\(\) ：      移除当前线程赋予局部线程变量的值。
4. set\(\)：               为当前线程拷贝的局部线程变量设置一个特定的值。

```java
public class ThreadLocalTest implements Runnable {

    private static final AtomicInteger nextId = new AtomicInteger(0);

    private static final ThreadLocal<Integer> threadId =
            new ThreadLocal<Integer>() {
                @Override
                protected Integer initialValue() {
                    return nextId.getAndIncrement();
                }
            };

    public int getThread() {
        return threadId.get();
    }

    private static final ThreadLocal<Date> startDate=
            new ThreadLocal<Date>(){
                    @Override
                    protected Date initialValue() {
                        return new Date();
                    }
            };


    @Override
    public void run() {
        System.out.printf("Strarting Thread: %s： % sn", getThread(), startDate.get());

        try {
            TimeUnit.SECONDS.sleep((int) Math.rint(Math.random() * 10));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.printf("Thread  Finished:&s:&sn", getThread(), startDate.get());
    }
}

```

使用场景

当有一些对象不满足线程安全，但是又想避免使用 synchronize 关键字、块时产生同步访问，那么让每个线程拥有自己的对象实例。

{% hint style="danger" %}
在服务器上，可能会保持一个线程池，那么 ThreadLoacl 变量会在响应客户端之前被移除，因为当前线程可能被下一个请求重复使用。而且，如果在使用完毕后不进行处理，它所保持的任何一个对类的引用，这个类会作为部署应用的一部分加载进啦，将被保留在永久堆栈中，永远不会被垃圾回收机制回收。
{% endhint %}


```java
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            map.set(this, value);
        } else {
            createMap(t, value);
        }
    }
```



```java
/**
 * 获取与 ThreadLocal 关联的映射。
*  在 Inheritable ThreadLocal中 重写。
 */
 ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
```

