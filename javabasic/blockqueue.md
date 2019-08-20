# BlockingQueue

多线程环境中，可以通过队列实现数据共享。当队列中没有数据的情况下，消费者的所有线程都会被自动阻塞（挂起），直到有数据放入队列。当队列中填满数据的情况下，生产者的所有线程都会被自定阻塞（挂起），直到队列中有空的位置，线程被自动唤醒。

## 放入数据

1. offer（E）

如果可以，将 E 加到 BlockingQueue 中，即如果 BlockingQueue 可以容纳，则返回 true，否则返回 false。

2. offer（E o，long timeout，TimeUnit unit）

可以设定等待时间，如果在指定时间内，还不能往队列中加入，则返回失败。

3. put（anObject） 

把anObject加到BlockingQueue里,如果BlockQueue没有空间,则调用此方法的线程被阻断。直到BlockingQueue里面有空间再继续.


> 

2. put（E）
3. add（E）
4. offer（E,long,TImeUnit）

获取数据

1. poll(time):

取走BlockingQueue里排在首位的对象,若不能立即取出,则可以等time参数规定的时间,取不到时返回null;

2. poll(long timeout, TimeUnit unit)：

从BlockingQueue取出一个队首的对象，如果在指定时间内、队列一旦有数据可取，则立即返回队列中的数据。否则知道时间超时还没有数据可取，返回失败。

3. take():

取走BlockingQueue里排在首位的对象,若BlockingQueue为空,阻断进入等待状态直到 BlockingQueue有新的数据被加入; 

4. drainTo():

一次性从BlockingQueue获取所有可用的数据对象（还可以指定获取数据的个数），通过该方法，可以提升获取数据效率；不需要多次分批加锁或释放锁。