# Lock

在jdk1.5以后，增加了juc并发包且提供了Lock接口用来实现锁的功能，它除了提供了与synchroinzed关键字类似的同步功能，还提供了比synchronized更灵活api实现。

## ReentrantLock

可重入锁。如果当前线程t1通过调用lock方法获取了锁之后，再次调用lock，是不会再阻塞去获取锁的，直接增加重入次数就行了。与每次lock对应的是unlock，unlock会减少重入次数，重入次数减为0才会释放锁。

## ReentrantReadWriteLock

1. 可重入读写锁。读写锁维护了一个读锁，一个写锁。
2. 读锁同一时刻允许多个读线程访问。
3. 写锁同一时刻只允许一个写线程，其他读/写线程都需要阻塞。

## Lock 与 Synchroized

|类别|synchronized|Lock|
|---|---|---|
|存在层次|Java的关键字，在jvm层面上|是一个接口|
|锁的释放|1.已获取锁的线程执行完同步代码，释放锁2.线程执行发生异常，jvm会让线程释放锁|必须在finally中释放锁，不然容易造成线程死锁|
|锁的获取|假设A线程获得锁，B线程等待。如果A线程阻塞，B线程会一直等待|Lock有多种获取锁的方式，如lock、tryLock|
|锁状态|无法判断，只能阻塞|可以判断tryLock();tryLock(long time, TimeUnit unit)可避免死锁|
|锁类型|可重入，非公平，不可中断|可重入，可公平（两者皆可）|可中断：lockInterruptibly();|
|功能|功能单一|API丰富tryLock();tryLock(long time, TimeUnit unit)可避免死锁|

## AQS

Lock之所以能实现线程安全的锁，主要的核心是AQS,AQS提供了一个FIFO队列，可以看做是一个用来实现锁以及其他需要同步功能的框架。AQS的使用依靠继承来完成，子类通过继承自AQS并实现所需的方法来管理同步状态。例如常见的ReentrantLock，CountDownLatch等。

从使用上来说，AQS的功能可以分为两种：独占和共享。

独占锁模式下，每次只能有一个线程持有锁，ReentrantLock就是以独占方式实现的互斥锁。
共享锁模式下，允许多个线程同时获取锁，并发访问共享资源，比如ReentrantReadWriteLock。

## AQS 内部实现

同步器依赖内部的同步队列（一个FIFO双向队列）来完成同步状态的管理，当前线程获取同步状态失败时，同步器会将当前线程以及等待状态等信息构造成为一个节点（Node）并将其加入同步队列，同时会阻塞当前线程，当同步状态释放时，会把首节点中的线程唤醒，使其再次尝试获取同步状态

```Java
static final class Node {
    //表示节点的状态，包含SIGNAL、CANCELLED、CONDITION、PROPAGATE、INITIAL
    volatile int waitStatus;
    //前继节点
    volatile Node prev;
    //后继节点
    volatile Node next;
    //当前线程
    volatile Thread thread;
    //存储在condition队列中的后继节点
    Node nextWaiter; 
```
waitStatus节点的几种状态

CANCELLED，值为1.由于在同步队列中等待的线程等待超时或者被中断，需要从同步队列中取消等待，节点进入该状态将不会变化。

SIGNAL，值为-1，后继节点的线程处于等待状态，而当前节点的线程如果释放了同步状态或者被取消，将会通知后继节点，使后继节点得以运行。

CONDITION，值为-2，节点在等待队列中，节点线程等待在Condition上，当其他线程对Condition调用了signal()方法后，该节点将会从等待队列中转移到同步队列中，加入到对同步状态的获取中。

PROPAGATE，值为-3，表示下一次共享式同步状态获取将会无条件地被传播下去。

INITAL，值为0，初始状态。


设置尾节点

当一个线程成功地获取了同步状态（或者锁），其他线程将无法获取，转而被构造成为节点并加入同步队列，而这个过程必须保证线程安全，因此同步器提供了一个基于CAS的设置尾节点的方法：compareAndSetTail(Node expect,Nodeupdate)，它需要传递当前线程“认为”的尾节点和当前节点，只有设置成功后，当前节点才正式与之前的尾节点建立关联。

    /**
     * CAS tail field. Used only by enq.
     */
    private final boolean compareAndSetTail(Node expect, Node update) {
        return unsafe.compareAndSwapObject(this, tailOffset, expect, update);
    }
设置首节点

同步队列遵循FIFO，首节点是获取同步状态成功的节点，首节点的线程在释放同步状态时，将会唤醒后继节点，而后继节点将会在获取同步状态成功时将自己设置为首节点。

设置首节点是通过获取同步状态成功的线程来完成的，由于只有一个线程能够成功获取到同步状态，因此设置头节点的方法并不需要使用CAS来保证，它只需要将首节点设置成为原首节点的后继节点并断开原首节点的next引用即可。

    /**
     * CAS head field. Used only by enq.
     */
    private final boolean compareAndSetHead(Node update) {
        return unsafe.compareAndSwapObject(this, headOffset, null, update);
    }
CAS
在AQS中，除了本身的链表结构以外，还有一个很关键的功能，就是CAS。这个功能可以保证线程在高并发的情况下能安全的加入到AQS队列中。

在AQS设置首节点和尾节点的方法中，都用到了unsafe.compareAndSwapObject方法。Unsafe类是在sun.misc包下，不属于Java标准，但是很多Java的基础类库，包括一些被广泛使用的高性能开发库都是基于Unsafe类开发的，比如Netty、Hadoop、Kafka等，Unsafe类可认为是Java中留下的后门，提供了一些底层操作，如直接内存访问、线程调度等。

compareAndSwapObject是一个native方法

    /**
     * var1：需要改变的对象
     * var2：偏移量
     * var4：期待值
     * var5：更新后的值
     */
    public final native boolean compareAndSwapObject(Object var1, long var2, Object var4, Object var5);
它的作用是，若当前时刻的值var1等于预期值var4，则更新为期望值 var5，如果更新成功，则返回true，否则返回false。

ReentrantLock的实现原理
ReentrantLock - > lock()

    public void lock() {
        sync.lock();
    }
这个是获取锁的入口。sync是一个实现了AQS的抽象类，这个类的主要作用是用来实现同步控制的，sync有两个实现，一个是NonfairSync(非公平锁)、另一个是FailSync(公平锁)。

ReentrantLock - > NonfairSync.lock

    final void lock() {
        /*
          通过cas算法去改变state的值
          state=0表示无锁状态，state>0表示有锁状态
          这是跟公平锁的主要区别,一上来就试探锁是否空闲,如果可以插队，则设置获得锁的线程为当前线程
        */
        if (compareAndSetState(0, 1)) 
            //exclusiveOwnerThread属性是AQS从父类AbstractOwnableSynchronizer中继承的属性，用来保存当前占用锁的线程
            setExclusiveOwnerThread(Thread.currentThread());
        else
            acquire(1); //尝试去获取锁
    }
当锁被占用时，执行acquire(1)，以下都是对这个方法的详细分析

    /**
      * 尝试获取独占锁，获取成功则返回
      * 获取失败则继续自旋获取锁，并且判断中断标识，如果中断标识为true，则设置线程中断
      * addWaiter方法把当前线程封装成Node，并添加到队列的尾部
      */
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
AQS - > tryAcquire

该方法尝试获取锁，如果成功就返回，如果不成功，则把当前线程和等待状态信息构适成一个Node节点，并将结点放入同步队列的尾部。然后为同步队列中的当前节点循环等待获取锁，直到成功。

    protected final boolean tryAcquire(int acquires) {
        return nonfairTryAcquire(acquires);
    }
AQS - > nofairTryAcquire

    /**
     * Performs non-fair tryLock.  tryAcquire is implemented in
     * subclasses, but both need nonfair try for trylock method.
     */
    final boolean nonfairTryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        //获取当前的状态，默认情况下是0表示无锁状态
        int c = getState();
        if (c == 0) {
            //通过cas来改变state状态的值，如果更新成功，表示获取锁成功
            //这个操作外部方法lock()就做过一次，这里再做只是为了再尝试一次，尽量以最简单的方式获取锁。
            if (compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        {//如果当前线程等于获取锁的线程，表示重入，直接累加重入次数
        else if (current == getExclusiveOwnerThread()) {
            int nextc = c + acquires;
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            setState(nextc);
            return true;
        }
        //如果状态不为0，且当前线程不是owner，则返回false
        return false;
    }
AQS - >addWaiter

当前线程来请求锁的时候，如果锁已经被其他线程锁持有，当前线程会进入这个方法，这个方法主要是把当前线程封装成node，添加到AQS的链表中。

    /**
     * Creates and enqueues node for current thread and given mode.
     *
     * @param mode Node.EXCLUSIVE for exclusive, Node.SHARED for shared
     * @return the new node
     */
    private Node addWaiter(Node mode) {
        //创建一个独占的Node节点,mode为排他模式
        Node node = new Node(Thread.currentThread(), mode);
        // 尝试快速入队,如果失败则降级至full enq
        Node pred = tail; // tail是AQS的中表示同步队列队尾的属性，刚开始为null，所以进行enq(node)方法
        if (pred != null) {
            node.prev = pred;
            // 防止有其他线程修改tail,使用CAS进行修改,如果失败则降级至full enq
            if (compareAndSetTail(pred, node)) {
                pred.next = node; // 如果成功之后旧的tail的next指针再指向新的tail,成为双向链表    
                return node;
            }
        }
        // 如果队列为null或者CAS设置新的tail失败
        enq(node);
        return node;
    }
AQS- >enq

enq就是通过自旋操作把当前节点加入到队列中。

    /**
     * Inserts node into queue, initializing if necessary. See picture above.
     * @param node the node to insert
     * @return node's predecessor
     */
    private Node enq(final Node node) {
        //无限循环，为什么采用for(;;)，是因为它执行的指令少，不占用寄存器
        for (;;) {
            Node t = tail; //第一次循环时，head, tail都为null
            //如果tail为null则说明队列首次使用,需要进行初始化
            if (t == null) { // Must initialize
                //设置头节点,如果失败则存在竞争,留至下一轮循环           
                if (compareAndSetHead(new Node()))
                    //用CAS的方式创建一个空的Node作为头结点，因为此时队列中只一个头结点，所以tail也指向head
                    tail = head;
                    //第一次循环执行结束
            } else { 
                //进行第二次循环时，tail不为null，进入else区域。
                //将当前线程的Node结点的prev指向tail，然后使用CAS将tail指向Node
                //这部分代码和addWaiter代码一样，将当前节点添加到队列
                node.prev = t;
                if (compareAndSetTail(t, node)) {
                    //t此时指向tail,所以可以CAS成功，将tail重新指向Node。
                    //此时t为更新前的tail的值，即指向空的头结点，t.next=node，就将头结点的后续结点指向Node，返回头结点
                    t.next = node;
                    return t;
                }
            }
        }
    }
AQS- >acquireQueued

前面addWaiter返回了插入的节点，作为acquireQueued方法的入参，这个方法主要用于争抢锁。

    final boolean acquireQueued(final Node node, int arg) {
        boolean failed = true;
        try {
            boolean interrupted = false;
            for (;;) {
                //获取prev节点,若为null即刻抛出NullPointException
                final Node p = node.predecessor();
                //如果前驱为head才有资格进行锁的抢夺
                if (p == head && tryAcquire(arg)) {
                    //获取锁成功后就不需要再进行同步操作了,获取锁成功的线程作为新的head节点
                    setHead(node);
                    //凡是head节点,head.thread与head.prev永远为null, 但是head.next不为null
                    p.next = null; // help GC
                    failed = false; //获取锁成功
                    return interrupted;
                }
                //如果获取锁失败，则根据节点的waitStatus决定是否需要挂起线程
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    //若前面为true,则执行挂起,待下次唤醒的时候检测中断的标志
                    interrupted = true;
            }
        } finally {
            if (failed)
                //如果抛出异常则取消锁的获取,进行出队(sync queue)操作
                cancelAcquire(node);
        }
    }
原来的head节点释放锁以后，会从队列中移除，原来head节点的next节点会成为head节点。

AQS- >shouldParkAfterFailedAcquire

从上面的分析可以看出，只有队列的第二个节点可以有机会争用锁，如果成功获取锁，则此节点晋升为头节点。对于第三个及以后的节点，if (p == head)条件不成立，首先进行shouldParkAfterFailedAcquire(p, node)操作。

shouldParkAfterFailedAcquire方法是判断一个争用锁的线程是否应该被阻塞。它首先判断一个节点的前置节点的状态是否为Node.SIGNAL，如果是，是说明此节点已经将状态设置-如果锁释放，则应当通知它，所以它可以安全的阻塞了，返回true。

   /**
     * Checks and updates status for a node that failed to acquire.
     * Returns true if thread should block. This is the main signal
     * control in all acquire loops.  Requires that pred == node.prev.
     *
     * @param pred node's predecessor holding status
     * @param node the node
     * @return {@code true} if thread should block
     */
    private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
        int ws = pred.waitStatus; //前继节点的状态
        if (ws == Node.SIGNAL) //如果是SIGNAL状态，意味着当前线程需要被unpark唤醒
            /*
             * This node has already set status asking a release
             * to signal it, so it can safely park.
             */
            return true;
        /*
         * 如果前节点的状态大于0，即为CANCELLED状态时，则会从前节点开始逐步循环找到一个没有    
         * 被CANCELLED节点设置为当前节点的前节点，返回false。在下次循环执行 
         * shouldParkAfterFailedAcquire时，返回true。
         * 这个操作实际是把队列中CANCELLED节点剔除掉。
         */
        if (ws > 0) {
            /*
             * 如果前继节点是“取消”状态，则设置 “当前节点”的 “当前前继节点” 为 “‘原前继节
             *  点'的前继节点”。
             */
            do {
                node.prev = pred = pred.prev;
            } while (pred.waitStatus > 0);
            pred.next = node;
        } else { //如果前继节点为“0”或者“共享锁”状态，则设置前继节点为SIGNAL状态。
            /*
             * waitStatus must be 0 or PROPAGATE.  Indicate that we
             * need a signal, but don't park yet.  Caller will need to
             * retry to make sure it cannot acquire before parking.
             */
            compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
        }
        return false;
    }
AQS- >parkAndCheckInterrupt

如果shouldParkAfterFailedAcquire返回了true，acquireQueued会继续执行parkAndCheckInterrupt()方法，它是通过LockSupport.park(this)将当前线程挂起到WATING状态，它需要等待一个中断、unpark方法来唤醒它，通过这样一种FIFO的机制的等待，来实现了Lock的操作。

   /**
     * Convenience method to park and then check if interrupted
     *
     * @return {@code true} if interrupted
     */
    private final boolean parkAndCheckInterrupt() {
        //LockSupport提供park()和unpark()方法实现阻塞线程和解除线程阻塞
        LockSupport.park(this);
        return Thread.interrupted();
    }
ReentrantLock - > unlock()

下面是释放锁的过程。先调用AQS - > release方法，这个方法里面做两件事，1.释放锁 ；2.唤醒park的线程。

   /**
     * Attempts to release this lock.
     *
     * <p>If the current thread is the holder of this lock then the hold
     * count is decremented.  If the hold count is now zero then the lock
     * is released.  If the current thread is not the holder of this
     * lock then {@link IllegalMonitorStateException} is thrown.
     *
     * @throws IllegalMonitorStateException if the current thread does not
     *         hold this lock
     */
    public void unlock() {
        sync.release(1);
    }
 
   /**
     * Releases in exclusive mode.  Implemented by unblocking one or
     * more threads if {@link #tryRelease} returns true.
     * This method can be used to implement method {@link Lock#unlock}.
     *
     * @param arg the release argument.  This value is conveyed to
     *        {@link #tryRelease} but is otherwise uninterpreted and
     *        can represent anything you like.
     * @return the value returned from {@link #tryRelease}
     */
    public final boolean release(int arg) {
        if (tryRelease(arg)) {
            Node h = head;
            if (h != null && h.waitStatus != 0)
                unparkSuccessor(h);
            return true;
        }
        return false;
    }
ReentrantLock - > Sync.tryRelease

这个动作可以认为就是一个设置锁状态的操作，而且是将状态减掉传入的参数值（参数是1），如果结果状态为0，就将排它锁的Owner设置为null，以使得其它的线程有机会进行执行。 在排它锁中，加锁的时候状态会增加1（当然可以自己修改这个值），在解锁的时候减掉1，同一个锁，在可以重入后，可能会被叠加为2、3、4这些值，只有unlock()的次数与lock()的次数对应才会将Owner线程设置为空，而且也只有这种情况下才会返回true。

        protected final boolean tryRelease(int releases) {
            int c = getState() - releases; // 这里是将锁的数量减1
            if (Thread.currentThread() != getExclusiveOwnerThread())
                // 如果释放的线程和获取锁的线程不是同一个，抛出非法监视器状态异常
                throw new IllegalMonitorStateException();
            boolean free = false;
            // 由于重入的关系，不是每次释放锁c都等于0，
            // 直到最后一次释放锁时，才会把当前线程释放
            if (c == 0) {
                free = true;
                setExclusiveOwnerThread(null);
            }
            setState(c);
            return free;
        }
 

lock与unlock过程总结
在获取同步状态时，同步器维护一个同步队列，获取状态失败的线程都会被加入到队列中并在队列中进行自旋；移出队列（或停止自旋）的条件是前驱节点为头节点且成功获取了同步状态。在释放同步状态时，同步器调用tryRelease(int arg)方法释放同步状态，然后唤醒头节点的后继节点。

公平锁和非公平锁的区别
锁的公平性是相对于获取锁的顺序而言的，如果是一个公平锁，那么锁的获取顺序就应该符合请求的绝对时间顺序，也就是FIFO。 在上面分析的例子来说，只要CAS设置同步状态成功，则表示当前线程获取了锁，而公平锁则不一样，差异点有两个

        final void lock() {
            acquire(1);
        }
非公平锁在获取锁的时候，会先通过CAS进行抢占，而公平锁则不会

ReentrantLock - > FairSync.tryAcquire

        /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
这个方法与nonfairTryAcquire(int acquires)比较，不同的地方在于判断条件多了hasQueuedPredecessors()方法，也就是加入了【同步队列中当前节点是否有前驱节点】的判断，如果该方法返回true，则表示有线程比当前线程更早地请求获取锁，因此需要等待前驱线程获取并释放锁之后才能继续获取锁。

Condition
我们知道任意一个Java对象，都拥有一组监视器方法（定义在java.lang.Object上），主要包括wait()、notify()以及notifyAll()方法，这些方法与synchronized同步关键字配合，可以实现等待/通知模式。

JUC包提供了Condition来对锁进行精准控制，Condition是一个多线程协调通信的工具类，可以让某些线程一起等待某个条件（condition），只有满足条件时，线程才会被唤醒。

condition中两个最重要的方法：1.await，把当前线程阻塞挂起；2.signal，唤醒阻塞的线程。

AQS - > ConditionObject.await()

        /**
         * Implements interruptible condition wait.
         * <ol>
         * <li> If current thread is interrupted, throw InterruptedException.
         * <li> Save lock state returned by {@link #getState}.
         * <li> Invoke {@link #release} with saved state as argument,
         *      throwing IllegalMonitorStateException if it fails.
         * <li> Block until signalled or interrupted.
         * <li> Reacquire by invoking specialized version of
         *      {@link #acquire} with saved state as argument.
         * <li> If interrupted while blocked in step 4, throw InterruptedException.
         * </ol>
         */
        public final void await() throws InterruptedException {
            if (Thread.interrupted())
                throw new InterruptedException();
            //创建一个新的节点，节点状态为condition，采用的数据结构仍然是链表
            Node node = addConditionWaiter(); 
            //释放当前的锁，得到锁的状态，并唤醒AQS队列中的一个线程
            int savedState = fullyRelease(node);
            int interruptMode = 0;
            //如果当前节点没有在同步队列上，即还没有被signal，则将当前线程阻塞
            //isOnSyncQueue 判断当前 node 状态,如果是 CONDITION 状态,或者不在队列上了,就继续阻塞。
            //还在队列上且不是 CONDITION 状态了,就结束循环和阻塞
            while (!isOnSyncQueue(node)) { //第一次判断的是false，因为前面已经释放锁了
                LockSupport.park(this); //第一次总是 park 自己,开始阻塞等待
                //线程判断自己在等待过程中是否被中断了
                //如果没有中断,则再次循环,会在 isOnSyncQueue 中判断自己是否在队列上
                if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
                    break;
            }
            //当这个线程醒来,会尝试拿锁, 当 acquireQueued 返回 false 就是拿到锁了
            // interruptMode != THROW_IE -> 表示这个线程没有成功将 node 入队,但 signal 执行了 enq 方法让其入队了
            if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
                // 将这个变量设置成 REINTERRUPT
                interruptMode = REINTERRUPT;
            //如果 node 的下一个等待者不是null, 则进行清理,清理 Condition 队列上的节点
            //如果是 null ,就没有什么好清理的了
            if (node.nextWaiter != null) // clean up if cancelled
                unlinkCancelledWaiters();
            //如果线程被中断了,需要抛出异常.或者什么都不做
            if (interruptMode != 0)
                reportInterruptAfterWait(interruptMode);
        }
AQS - > ConditionObject.signal()

调用Condition的signal()方法，将会唤醒在等待队列中等待时间最长的节点（首节点），在唤醒节点之前，会将节点移到同步队列中。

       /**
         * Moves the longest-waiting thread, if one exists, from the
         * wait queue for this condition to the wait queue for the
         * owning lock.
         *
         * @throws IllegalMonitorStateException if {@link #isHeldExclusively}
         *         returns {@code false}
         */
        public final void signal() {
            //先判断当前线程是否获得了锁
            if (!isHeldExclusively())
                throw new IllegalMonitorStateException();
            Node first = firstWaiter; //拿到 Condition 队列上第一个节点
            if (first != null)
                doSignal(first);
        }
 
        /**
         * Removes and transfers nodes until hit non-cancelled one or
         * null. Split out from signal in part to encourage compilers
         * to inline the case of no waiters.
         * @param first (non-null) the first node on condition queue
         */
        private void doSignal(Node first) {
            do {
                //如果第一个节点的下一个节点是 null,那么最后一个节点也是 null
                if ( (firstWaiter = first.nextWaiter) == null)
                    lastWaiter = null; //将 next 节点设置成 null
                first.nextWaiter = null;
            } while (!transferForSignal(first) &&
                     (first = firstWaiter) != null);
        }
AQS - > transferForSignal

该方法先是 CAS 修改了节点状态，如果成功，就将这个节点放到 AQS 队列中，然后唤醒这个节点上的线程。此时，那个节点就会在 await 方法中苏醒。

 /**
     * Transfers a node from a condition queue onto sync queue.
     * Returns true if successful.
     * @param node the node
     * @return true if successfully transferred (else the node was
     * cancelled before signal)
     */
    final boolean transferForSignal(Node node) {
        /*
         * If cannot change waitStatus, the node has been cancelled.
         */
        if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
            return false;
 
        /*
         * Splice onto queue and try to set waitStatus of predecessor to
         * indicate that thread is (probably) waiting. If cancelled or
         * attempt to set waitStatus fails, wake up to resync (in which
         * case the waitStatus can be transiently and harmlessly wrong).
         */
        Node p = enq(node);
        int ws = p.waitStatus;
        // 如果上一个节点的状态被取消了, 或者尝试设置上一个节点的状态为 SIGNAL 失败了
        // SIGNAL 表示: 他的next 节点需要停止阻塞
        if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
            LockSupport.unpark(node.thread); // 唤醒输入节点上的线程
        return true; 
    }
LockSupport
LockSupport类是Java6引入的一个类，提供了基本的线程同步原语。LockSupport实际上是调用了Unsafe类里的函数，归结到Unsafe里，只有两个函数：

public native void unpark(Thread jthread);
 
public native void park(boolean isAbsolute, long time);
unpark函数为线程提供“许可(permit)”，线程调用park函数则等待“许可”。这个有点像信号量，但是这个“许可”是不能叠加的，“许可”是一次性的。

permit相当于0/1的开关，默认是0，调用一次unpark就加1变成了1.调用一次park会消费permit，又会变成0。 如果再调用一次park会阻塞，因为permit已经是0了。直到permit变成1.这时调用unpark会把permit设置为1。每个线程都有一个相关的permit，permit最多只有一个，重复调用unpark不会累积。

在使用LockSupport之前，我们对线程做同步，只能使用wait和notify，但是wait和notify其实不是很灵活，并且耦合性很高，调用notify必须要确保某个线程处于wait状态，而park/unpark模型真正解耦了线程之间的同步，先后顺序没有没有直接关联，同时线程之间不再需要一个Object或者其它变量来存储状态，不再需要关心对方的状态。
