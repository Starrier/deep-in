---
title: springcloud-greenwich
date: 2019-02-23 21:36:09
tags: SpringCloud-Greenwich
index_img: ../post_img/spring-cloud-greenwich.jpeg
---

# SpringCLoud Greenwich

Greenwich 只支持 Spring Boot 2.1 x 分支，主要是适配 Java 11。如果是 2.0 x，请使用 Finchley。

## pring Cloud Gateway

 1. 添加了重写响应头的过滤器。
 2. 在各自的路由 predicates 中添加了对多路径和多主机的支持，并且自定义在某些情况下返回 HTTP 状态码。
 3. 支持整合 OAuth2
 4. 新增重写响应头过滤器：

 ```yml
 spring:
   cloud:
     gateway:
       routes:
        - id: rewriteresponseheader_route
         uri: http://example.org
         filters:
           = RewriteResponseHeader=X-Response-Foo,,password=[^&]+,password=***
 ```

## Spring Cloud OpenFeign

 1. 添加了对 `@QueryMap` 注解的支持。
 2. 更新 OpenFeign 至 10.1.0 版本。

## Spring Cloud Fegin

 1. `@SpringQueryMap` 对 Get 进行了增强。不用直接使用 OpenFeign 新增的 `@QueryMap`，由于缺少 value 属性，QueryMap 注释与 Spring 不兼容。

 ```java
 @FeignClient("demo")
 public class DemoTemplate{
     @GetMapping(path="/demo")
     String demoEndpoint(@SpringQueryMap Params params);
 }
 ```

## Spring Cloud Config

 1. 添加了 `EnvironmentRepository` 来支持 CredHub 后端。
 2. 除了支持 Git、File、JDBC 新版本提供在 Cloud Foundry 的 CredHub 存储功能。

``` yml
spring:
  profiles:
    active: credhub
  cloud:
    config:
      server:
        credhub:
          url: http://credhub:8844
  
```

## Spring Cloud Security

 1. 增加了 Spring Cloud Gateway 过滤器来支持 OAuth2.

## 