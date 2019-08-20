---
title: redis-action
date: 2019-02-16 02:02:43
tags: redis
index_img: ../post_img/enum.jpeg
---

## Redis 的应用场景

### 1. 缓存：热数据

热点数据（经常被查询，但是不经常修改或者删除的数据），首选 Redis 缓，有强大的 QPS 和非常好的稳定性，相比 Memcached，拥有更丰富的数据类型。内存中的数据也提供 AOF 和 RDB 等持久化机制可以选择。

### 2. 计数器（持久化）

诸如统计点击数等应用。由于单线程，可以避免并发问，不会出错。命令：INCRBY。

### 3. 队列

相当于消息系统（如 ActiveMQ，RabbitMQ 等。对一致性有要求，应该使用 RocketMQ 等。

### 4. 位图

用户系统签到，去重登录次数统计，某用户是否在线状态等待

位图操作：`setbit`, `getbit`, `bitcount`

原理：

redis 内构件一个足够长的数组，每个数组元素只能是 0 和 1，然后这个数组的下标 index 用来表示我们上面例子里面的用户 id（必须是数字），那么很显然，这个几亿长的大数组就能通过下标和元素值（0 和 1） 来构建一个记忆系统。

### 5. 分布式锁与单线程机制

验证前端的重复请求（可以自由扩展类似情况），可以通过 redis 进行过滤：每次请求将 request IP、参数、接口等 hash 作为 key 存储进 redis（冥等性请求），设置多长时间有效期，然后下次请求过来时，先在 redis 中检索有没有这个 key，进而验证是不是一定时间内过来的重复提交。

秒杀系统，基于 redis 是单线程的特性，防止出现数据库“爆破”。

全局增量 ID 生成，类似“秒杀”。

### 6. 最新列表

例如新闻列表页面最新的新闻列表，如果总数很大的情况下，尽量不要使用 `select a from A limit 10`,尝试使用 redis 的 `LPUSH` 命令构建 list，一个一个顺序塞入即可。如果内存不小心清除了，查询不到 key，用 mysql 查询并初始化一个 list 到 redis 中即可。

### 7. 排行榜

谁的得分高，排名就往前。命令：`ZADD`。

``` lua
// 将登录次数和用户统一存储在一个 sorted set 里面
zadd login:login_time 5 1

// 当用户登录时，对该用户的登录次数自增
ret = r.zincrby("login:login_times",1,uid)

// 那么如何获取登录次数最多的用户呢，逆序排列去的排名前 N 的用户
ret = r.zrevrang("login:login_times",0,N-1)
```

列出前 100 名的高分选手/列出某用户当前的全球排名

对 redis 来说，即使你有几百万个用户，每分钟都会有几百万个新的得分。模式是这样，每次获得新得分时，我们用这样的代码：

```lua
ZADD leaderboard <score> <username>
```

你可以用 userId 来获取 username，这取决于设计。得到前 100 名高分用户很简单：

```lua
ZREVRANGE leaderboard 0 99
```

用户全球排名也是如此：

``` lua
ZRANK leaderboard <username>
```