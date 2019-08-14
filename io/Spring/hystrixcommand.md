---
title: hystrixcommand
date: 2019-03-03 00:21:31
tags: spring-cloud
index_img: ../post_img/hystrix-comman.jpeg
---

## @HystrixCommand

在需要熔断的方法上，添加该注解：`@HystrixCommand`。

Hystrix 支持注解和 Java 继承的方式来实现自定义的 HystrixCommand，而且还支持同步请求和异步请求。

创建

### Spring Cloud Hystrix 中与缓存有关的注解：

 1. `@CacheResult` : 用来标记其结果需要缓存，必须与 @HystrixCommand 注解一起使用
 2. @CacheRemove 该注解用来让请求命令的缓存失效，失效的缓存根据定义的 key 决定。
 3. @CacheKey 该注解用来在请求命令的参数上标记，是其作为缓存的 key 值，如果没有标注则会使用所有的参数。如果同时使用了 @CacheResult 和 @CacheRemove 注解的 cacheKyeMethod 方法指定缓存 key 生成，那么该注解将不会起作用。

```java

```