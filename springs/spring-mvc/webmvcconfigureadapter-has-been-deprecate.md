# WebMvcConfigureAdapter has been Deprecate

### WebMvcConfigureAdapter  在 SpringBoot 2.0+ 以及 Spring 5.0 + 中被弃用

代替方案：

#### 1. 直接实现 WebMvcConfigurer（官方推荐）

```java
@Configuration
public class WebMvcConfg implements WebMvcConfigurer {
    //todo
}
```

#### 2. 直接继承 WebMvcConfigurationSupport

```java
@Configuration
public class WebMvcConfg extends WebMvcConfigurationSupport {
        //todo
}
```

as of 5.0 WebMvcConfigurer has default methods \(made possible by a Java 8 baseline\) and can be implemented directly without the need for this adapter

An implementation of WebMvcConfigurer with empty methods allowing subclasses to override only the methods they’re interested in.

