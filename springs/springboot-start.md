---
title: springboot-start
date: 2019-02-19 16:03:41
tags: SpringBoot
index_img: ../post_img/springboot-start.jpeg
---

## SpringBoot-start

### SpringBoot 源码分析

SpringBoot 启动类：

```Java
@SpringBootApplication
public class Test{
    public static void main(String [] args){
        SpringApplication.run(Test.class,args);
    }
}
```
