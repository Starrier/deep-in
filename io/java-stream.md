---
title: java-stream
date: 2019-03-12 23:16:19
tags: java8
index_img: ../post_img/netty-reactor.jpeg
---

## Stream

Stream 专注于对容器对象进行各种非常便利，高效的**聚合操作**，或者**大批量数据操作**。它提供串行化和并行两种模式进行汇聚操作，并发模式可以充分利用多核优势，使用 fork/join 并行方式来拆分任务和加速处理过程。

### Stream 特点：

1. **无存储**： Stream 不是一种数据结构，它只是某种数据源的一个视图，数据源可以是一个数组，Java 容器，IO，channel 等。
2. **函数式**： 对 Stream 的任何修改都不会修改背后的数据源，比如对 Stream 执行过滤操作，并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的 Stream。
3. **惰式执行**： Stream 上的操作并不会立即执行，只有等到用户真正需要结果的时候才会执行。
4. **可消费性**： Stream 只能被“消费”一次，一旦遍历就会失效，就像容器那样，想要再次遍历必须重新生成。

对于 Stream 的处理，主要有三种关键性操作，分别是

- Stream 的创建，
- 中间件操作
- 最终操作

### 创建 Stream

 1. 通过已有集合来创建流

 ```java
List<String> listStream = Arrays.asList("A","B","C");
Stream<String> stream = listStream.stream();
 ```

 2. 通过 Stream 创建

 ```java
Stream<String> streams = Stream.of("A","B");
 ```

3. 从 Collection 和数组中获取

  - Collection.stream()
  - Collection.parallelStream()
  - Arrays.stream(T array) / Stream.of()

4. 从 BufferedReader 中获取

 - java.io.BufferedReader.lines()

5. 静态共产

- java.util.stream.IntStream.range()
- java.nio.file.Files.walk()

6. 自己构建

- java.util.Spliterator

7. 其他

- Random.ints()
- BitSet。stream()
- 


### 中间操作

Stream 有很多中间操作，多个中间操作可以连接起来形成一个流水线，每一个中间操作就像是线上的一个工人，每个工人都可以对流进行加工，加工后得到的还是一个流。

#### filter

filter 方法用于通过设置的条件过滤出元素。

#### map

map 方法用于映射每个元素到对应的结果。（可以参考 《Java 8 函数式编程》（薄的那本）：map 用于将一种流映射成另一种流）

#### limit/skip

limit 会返回 Stream 的前面 n 个元素；skip 则是扔掉前 n 个元素。

#### sorted

sorted 方法用于对流进行排序。

#### distinct

distinct 主要用来去重。

### Stream 的最终操作

Stream 的中间件操作，得到的结果仍然是一个 Stream，想要获取到结果，我们就需要计算出流中元素的个数，将元素的个数，将流转换成集合等。这就需要最终操作。

最终操作会消耗流，会产生一个结果。即，想要再次对其操作时，会产生异常，

 ```java
java.lang.IllegalStateException:stream has already been operated upon or closed
 ```

 #### forEach

 用于迭代 Stream 中的每个数据。

 #### count

 用于统计 Stream 中的元素个数。

 #### collect

 collect 是一个规约操作，可以接受各种做法作为参数，将流中的元素基类成一个汇总结果。

 ## 总结

 1. Stream 不是数据结构
 2. 他没有
