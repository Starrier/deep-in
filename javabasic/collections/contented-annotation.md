---
title: contented-annotation
date: 2019-03-04 09:34:52
tags: Java8
index_img: ../post_img/contened.jpeg
---

## @Contenned 和伪共享

### 缓存行

CPU

### 伪共享（False Sharing）

### 填充（Padding）

JVM 可能会清除无用字段或重排无用字段的位置，这样的话，可能又会不经意间引入伪共享。为了避免无用字段被消除，我们通常会使用 Volatile 修饰。

**Note**：

应该只需为处于激烈竞争状态的类进行填充处理，而且一般只有通过对其性能分析才能发现性能上的不同。通常在性能分析的时候，最好在对其迭代访问 10000 次之后再去采样，这样可以消除 JVM 本身运行时优化策略带来的影响。

### @Contended

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.TYPE})
public @interface Contended {
    String value() default "";
}
```

除了对字段进行填充之外，我们还可以对需要避免陷入伪共享的字段进行注解，这个注解暗示 JVM 应当讲字段放入不同的缓存行。

```java
public class Point{
    int x;
    @Contended
    int y;
}
```

上面的代码会将 x 和 y 置于不同的缓存行，`@Contended` 注解将 y 移动到远离对象头部的地方（以避免和 x 一起被加载到同一个缓存中）。