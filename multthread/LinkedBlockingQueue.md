# LinkedBlockingQueue

内部由单链表实现，只能从 head 取元素，从 tail 添加元素。添加元素和获取元素都有独立的锁，也就是说 LinkedBlockingQueue 是读写分离的，读写操作可以并行执行。LinkedBlockingQueue 采用可重入锁（ReentrantLock）来保证在并发情况下的安全。

## 取数据

 1. take()：首选，队列为空时，返回空。
 2. poll()：弹出队顶元素，队列为空时，返回空。
 3. peek()：和 poll 类似，返回队顶元素，但顶元素不弹出。队列为空时返回 null。
 4. remove(Object o)：移除某个元素，队列为空时，抛出异常。成功时返回 true。

 