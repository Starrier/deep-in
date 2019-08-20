---
title: ConcurrentHashMap
date: 2019-02-13 17:15:08
tags: DataStruct
index_img: ../post_img/taoyifenxi.jpeg
---



### 线程不安全的原因

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
