# Future

extends Thread 和 implements Runnable 的缺陷是，在执行完任务后，无法获取执行结果。

Future 就是对具体的 Runnable 或者 Callable 任务的执行结果进行取消，查询是否完成，获取任务结果。必要时可以通过 get 方法获取执行结果，该方法会阻塞知道任务返回结果。

``` java
public interface Future<V> {
2     boolean cancel(boolean mayInterruptIfRunning);
3     boolean isCancelled();
4     boolean isDone();
5     V get() throws InterruptedException, ExecutionException;
6     V get(long timeout, TimeUnit unit)
7         throws InterruptedException, ExecutionException, TimeoutException;
8 }
```

1. cancel():cancel()方法用来取消异步任务的执行。如果异步任务已经完成或者已经被取消，或者由于某些原因不能取消，则会返回false。如果任务还没有被执行，则会返回true并且异步任务不会被执行。如果任务已经开始执行了但是还没有执行完成，若mayInterruptIfRunning为true，则会立即中断执行任务的线程并返回true，若mayInterruptIfRunning为false，则会返回true且不会中断任务执行线程。
2. isCanceled():判断任务是否被取消，如果任务在结束(正常执行结束或者执行异常结束)前被取消则返回true，否则返回false。
3. isDone():判断任务是否已经完成，如果完成则返回true，否则返回false。需要注意的是：任务执行过程中发生异常、任务被取消也属于任务已完成，也会返回true。
4. get():获取任务执行结果，如果任务还没完成则会阻塞等待直到任务执行完成。如果任务被取消则会抛出CancellationException异常，如果任务执行过程发生异常则会抛出ExecutionException异常，如果阻塞等待过程中被中断则会抛出InterruptedException异常。
5. get(long timeout,Timeunit unit):带超时时间的get()版本，如果阻塞等待过程中超时则会抛出TimeoutException异常。

# FutureTask

Future只是一个接口，不能直接用来创建对象，FutureTask是Future的实现类，

FutureTask的继承图如下:



可以看到,FutureTask实现了RunnableFuture接口，则RunnableFuture接口继承了Runnable接口和Future接口，所以FutureTask既能当做一个Runnable直接被Thread执行，也能作为Future用来得到Callable的计算结果。

使用

FutureTask一般配合ExecutorService来使用，也可以直接通过Thread来使用。

