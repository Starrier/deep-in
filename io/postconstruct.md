---
title: postconstruct
date: 2019-03-12 22:11:59
tags: Annotation
index_img: ../post_img/netty-reactor.jpeg
---

## 构造器注解

JavaEE 5  中，Servlet 增加了两个影响 Servlet 生命周期的注解，`@PostConstruct` 和 `@PreDestroy`，这两个注解被用来修饰一个非静态方法.

```java
@PostConstruct
```

被 `@PostConstruct` 修饰的方法会在服务器加载 Servlet 的时候运行，并且只会被服务器执行一次。`@PostConstruct` 在构造函数之后执行，init() 函数之前执行。`@PreDestroy` 方法会在 destory() 方法之后执行。

执行顺序：

 1. 服务器加载 Servlet
 2. Servlet 构造函数
 3. PostConstruct
 4. Init 方法
 5. destroy
 6. PreDestroy
 7. 服务器卸载 Servlet

Spring 中构造函数的执行顺序性

如果一个类中，有个成员变量 p 被 `@Autowired` 注解，那么 `@Authowired` 注入是发生在 A 的构造方法执行完成后。

如果想在生成对象时完成某些初始化操作，而偏偏这些初始化操作又依赖于注入，那么就无法在构造函数中实现。此时，我们就可以使用 `@PostConstruct` 注解一个方法来完成初始化，`@PostConstruct` 注解的方法就会在依赖注入完成后被自动调用。

**`Constructor` -> `@Autowired` -> `@PostConstruct`**

`@PreDestroy`

通常我们想要在关闭 Spring 之后，是方法一些资源，有两种做法：

 1. 在方法上加上 `@PreDestroy` 注解
 2. 实现 DisposableBean 接口，实际上是实现它的 destory 方法

