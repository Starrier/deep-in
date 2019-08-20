---
description: 声明式 Web Service 客户端
---

# Spring Cloud Feign

### 服务之间的调用方式

1. JDK 原生 URLConnection
2. Apache 的 HTTP Client
3. Netty 异步 HTTP Client
4. Spring 的 RestTemplate
5. Spring Cloud Open Feign\(最方便，最优雅\)

Spring Cloud 对 Feign 进行了增强，使 Feign 支持  Spring MVC 注解，并整合了 Ribbon。

### 特性

1. 可插拔的注解支持，包括 Feign 注解和 JAX-RS 注解
2. 支持可插拔的 HTTP 编码器和解码器
3. 支持 Hystrix 及其 fallback
4. 支持 Ribbon 的负载均衡
5. 支持 Http 请求和响应的压缩。

