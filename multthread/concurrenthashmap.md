# ConcurrentHashMap

## HashMap

线程不安全

多线程情况下，使用 HashMap 进行 put 操作会引起死循环，导致 CPU 利用率 100%。这是因为 HashMap 的 Entry 链表形成环形数据结构，一旦形成环形数据结构，Entry 的 next 节点永不为空，就会产生死循环获取 Entry。

```java
public class HashMapTest {
    public static void main(String[] args) throws InterruptedException {
        final HashMap<String, String> map = new HashMap<String, String>(2);
        Thread thread = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                new Thread(() -> {
                    map.put(UUID.randomUUID().toString(), "");
                }, "test" + i).start();
            }
        }, "test");
        thread.start();
        thread.join();
    }
}
```

## HashTable

HashTable 使用 synchronized 来保证线程安全，但在多线程竞争激烈的情况下，HashTable 的效率非常低。因为当一个线程访问 HashTable 的同步方法，其他线程也访问 HashTable 的同步方法时，会进入阻塞或轮询状态。如线程 1 使用 put 进行元素添加，线程 2 不但不能使用 put 方法添加元素，也不能使用 get 方法来获取元素，所以竞争越激烈，效率越低。

## ConcurrentHashMap

锁分段技术

HashTable 容器在竞争激烈的并发情况下，表现出效率低下的原因是所有访问 HashTable 的线程都必须竞争同一把锁，假如容器里有多把锁，每一把锁都用于锁容器其中一部分数据，那么当多线程访问容器里不同数据段的数据时，线程间就不会存在锁竞争，从而可以有效提高并发访问效率，这就是 ConcurrentHashMap 所使用的锁分段技术。首先将数据分成一段一段地存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。

