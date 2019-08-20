---
title: redis-get-start
date: 2019-02-16 01:52:53
tags: redis
index_img: ../post_img/enum.jpeg
---

## Redis

### Redis 为什么那么快

1. 完全基于内存，绝大部分请求是纯粹是内存操作，非常快速。数据存在内存中，类似于 HashMap，HashMap 的优势就是查找和操作的时间复杂度都是 O\(1\)。
2. 数据结构中，对数据的操作也简单，Redis 中的数据结构是专门进行设计的。
3. 采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁的操作，没有因为可能出现死锁而到导致的性能消耗。
4. 使用多路 I/O 复用模型，非阻塞 I/O。
5. 使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis 直接自己构建了 VM 机制，因为一般的系统调用系统函数的话，会浪费一定的时间去请求。

#### 多路 I/O 复用模型

多路 I/O 复用模型是利用 select、poll、epoll 可以同时监察多个流的 I/O 事件的能力，在空闲的时候，会把当前线程阻塞掉，当一个或多个流有 I/O 事件时，就从阻塞状态中唤醒，于是程序就会轮询一遍所有的流（epoll 是只轮询那些真正发出了事件的流），并且只依次按顺序处理就绪的流，这种做法剧避免了大量的无用操作。

这里的“多路”是指多个网络连接，“复用”指的是复用同一个线程，采用多路 I/O 复用技术可以让单个线程高效的处理多个连接请求（尽量减少网络 IO 的时间消耗），且 Redis 在内存中操作数据的速度非常快，也就是说内存的操作不会成为影响 Redis 性能的瓶颈，主要由以上几点造就了 Redis 具有很高的吞吐量。

### Redis 为什么是单线程的

Redis是基于内存的操作，CPU不是Redis的瓶颈，Redis的瓶颈最有可能是机器内存的大小或者网络带宽。既然单线程容易实现，而且CPU不会成为瓶颈，那就顺理成章地采用单线程的方案了

### Redis 内存淘汰机制

Redis 内存淘汰指的是用户存储的一些键可以被 Redis 主动地从实例中删除，从而产生读 miss 的情况。Redis 最常见的[应用场景](https://github.starrier.org/2019/02/16/redis-action)是**缓存**和**持久存储**。

