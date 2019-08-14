---
title: redis-data-type
date: 2019-02-16 02:14:06
tags: redis
index_img: ../post_img/redis-type.jpeg
---

## Redis 数据类型

Redis 的常用有数据类型：

 1. String
 2. Hash
 3. Set
 4. Zset
 5. List

### 1. String

String 数据结构是简单的 key-value 数据类型，普通的 K/V 存储都可以归为此类，即可完全实现目前 Memcached 功能，并且效率高效。还可以享受 Redis 的定时持久化，操作日志及 Replication 等功能。String 类型的值最大能存 512 MB。除了提供 Memcached 一样的 get、set、incr、decr 等操作外，Redis 还提供了下面一些操作；

 1. 获取字符串长度
 2. 往字符串中 append 内容
 3. 设置和获取字符串的某一段内容
 4. 设置及获取字符串的某一位(bit)
 5. 批量设置一系列字符串的内容

使用场景：常规 K/V 缓存应用，常规计数：微博数，粉丝数。

实现方式：String 在 redis 内部存储默认就是一个字符串，被 redisObject 引用，当遇到 incr，decr 等操作时会转成数值型进行计算，此时 redisObject 的 encoding 字段为 int。

### 2. Hash

常用命令：hget，hset，hgetall 等。

应用场景：存储用户信息对象等。

用户 ID 为查找的 key，存储的 value 用户对象包括姓名，年龄，生日等信息，如果用普通的 K/V 结构来存储，主要有一下两种方式：

第一种：将用户 ID 作为查找 key，把其他信息封装成一个对象以序列化的方式存储，这种方式的缺点，增加了序列化/反序列化的开销，并且在需要修改其中一项信息时，需要把整个对象取回，并且修改操作需要对并发进行保护，引入 CAS 等复杂问题。

第二种：用户信心对象有多少成员就存多少个 K/V 对，用户 ID + 对应属性的名称作为唯一标识来取得对应属性的值，虽然省去了序列化开下和并发问题，但是用户 ID 为重复存储，如果存在大量存在这样的书籍，内存浪费会非常严重。

也就是说，key 仍然是用户 ID，value 是一个 Map，这个 Map 的 key 是成员的属性名，value 是属性值，这样对数据的修改和存取都可以直接通过其内部 Map 的操作，由于 redis 单线程模型的缘故，这个遍历操作可能会比较耗时，而另外其他客户端的请求完全不响应，需要这点。

实现方式：

上面已经说到的 Redis Hash 对应的 value 内部实际是一个 HashMap，实际这里会有两种不同实现，这个 Hash 的成员比较少时，Redis 为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用震中的 HashMap 结构，对应的 value redisObject 的 encoding 的 zipmap，当成员数量增大时会自动转成真正的 HashMap，此时 encoding 为 ht。

### 3. List

常用命令： lpush,rpush,lpop,rpop，lrang 等。

应用场景：

redis list 的应用场景非常多，也是 redis 最重要的数据结构之一，比如 twitter 的关注列表，粉丝列表等都可以用 redis 的 list 结构来实现。

List 就是链表。使用 List 结构，我们可以轻松地实现最新消息排行等功能。List 的另一个应用就是消息队列，可以利用 List 的 PUSH 操作，将任务存在 List 中，然后工作线程再用 POP 操作将任务取出进行执行。Redis 还提供了操作 List 的 API，你可以查询，删除 List 中的某段元素。

实现方式：

Redis list 的实现为一个双向链表，即可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，redis 内部的很多实现，包括发送缓冲列队等也都是用的这个数据结构。

Redis list 是每个子元素都是 String 类型的双向链表，可以通过 push 和 pop 操作从列表的头部或尾部添加或删除元素，这样的 list 可以作为栈，也可以作为队列。

### 4. Set

常用命令：sadd、spop、smembers、sunion 等

实现方式：

set 内部实现是一个 value 永远为 null 的 HashMap,实际就是通过计算 hash 的方式快速排重的，这也是 set 能提供判断一个成员是否在集合内的原因。

### 5. zset

常用命令：zadd、zrange、zrem、zcard 等

实现方式：

Redis sorted set 的内部使用 HashMap 和跳跃表（SkipList）来保证数据的存储和有序，HashMap 里放的是成员到 score 的映射，而跳跃表里存放的是所有的成员，排序依据是 HashMap 里存的 score，使用跳跃表的结构可以获得比较高的查找效率，并且在实现上比较简单。