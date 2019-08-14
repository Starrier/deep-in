---
title: spring-cloud-hystrix-command
date: 2019-03-02 12:04:38
tags: spring-cloud
index_img: ../post_img/hystrix-comman.jpeg
---

# Spring Cloud Hystrix

## `@EnableCircuitBreaker`

`@EnableHystrix`:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@EnableCircuitBreaker
public @interface EnableHystrix {}
```

`@EnableCircuitBreaker` :

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(EnableCircuitBreakerImportSelector.class)
public @interface EnableCircuitBreaker {}
```

`@EnableHystrix` 是对 `@EnableCircuitBreaker` 的增强。

HystrixCommandAspect 通过 AOP 拦截所有的 `@HystrixCommand` 注解的方法，从而使得 @HystrixCommand 能够集成到 SpringBoot 中。

HystrixCommandAspect：

 1. hystrixCommandAnnotationPointcut() 定义拦截 HystrixCommand
 2. hystrixCollapserAnnotationPointcut() 定义拦截 HystrixCollapser
 3. 方法 methodsAnnotatedWithHystrixCommand(…) 通过 @Around(…) 拦截所有 HystrixCommand 和 HystrixCollapser 注解的方法。

```java
@Aspect
public class HystrixCommandAspect {

    @Pointcut("@annotation(com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand)")
    public void hystrixCommandAnnotationPointcut() {
    }

    @Pointcut("@annotation(com.netflix.hystrix.contrib.javanica.annotation.HystrixCollapser)")
    public void hystrixCollapserAnnotationPointcut() {
    }

  @Around("hystrixCommandAnnotationPointcut() || hystrixCollapserAnnotationPointcut()")
    public Object methodsAnnotatedWithHystrixCommand(final ProceedingJoinPoint joinPoint) throws Throwable {
        // 获取拦截的Method
        Method method = getMethodFromTarget(joinPoint);
        Validate.notNull(method, "failed to get method from joinPoint: %s", joinPoint);
        // 只有被HystrixCommand和HystrixCollapser注解的方法才执行后续操作
        if (method.isAnnotationPresent(HystrixCommand.class) && method.isAnnotationPresent(HystrixCollapser.class)) {
            throw new IllegalStateException("method cannot be annotated with HystrixCommand and HystrixCollapser " +
                    "annotations at the same time");
        }
        // 根据拦截方法的注解HystrixCommand或HystrixCollapser分别获取CommandMetaHolderFactory或者CollapserMetaHolderFactory类
        MetaHolderFactory metaHolderFactory = META_HOLDER_FACTORY_MAP.get(HystrixPointcutType.of(method));
        // 将拦截方法封装到MetaHolder中
        MetaHolder metaHolder = metaHolderFactory.create(joinPoint);
        // 根据metaHolder生成相应的HystrixCommand，包含生成hystrix执行时需要的配置信息，这些配置信息来自默认配置或我们自定义的属性
        HystrixInvokable invokable = HystrixCommandFactory.getInstance().create(metaHolder);
        ExecutionType executionType = metaHolder.isCollapserAnnotationPresent() ?
                metaHolder.getCollapserExecutionType() : metaHolder.getExecutionType();

        Object result;
        try {
           // 根据是否是Observable执行CommandExecutor.execute()方法，executeObservable最后也会执行CommandExecutor.execute()方法
            if (!metaHolder.isObservable()) {
                result = CommandExecutor.execute(invokable, executionType, metaHolder);
            } else {
                result = executeObservable(invokable, executionType, metaHolder);
            }
        } catch (HystrixBadRequestException e) {
            throw e.getCause() != null ? e.getCause() : e;
        } catch (HystrixRuntimeException e) {
            throw hystrixRuntimeExceptionToThrowable(metaHolder, e);
        }
        return result;
    }

….
}
```

@EnableCircuitBreaker 和 @EnableCircuitBreakerImportSelctor

HystrixCircuitBreakerConfiguration 的执行：

```xml
org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker=\
org.springframework.cloud.netflix.hystrix.HystrixCircuitBreakerConfiguration
```

通过 @Import 来初始化 EnableCircuitBreakerImportSelector 类，可以参见 @EnableCircuitBreaker.

EnableCircuitBreakerImportSelector 是 SpringFactoryImportSelector 子类。此类在初始化后，会执行 selectImports(AnnotationMetadata metadata) 的方法。此方法会根据注解启动的注解（这里指 `@EnableCircuitBreaker`）从spring.factories 文件中获取其配置需要初始化 `@Configuration` 类（这里是 org.springframework.cloud.netflix.hystrix.HystrixCircuitBreakerConfiguration），从而最终初始化 HystrixCommandAspect 类，从而实现拦截 HystrixCommand` 的功能

以上就是通过 `@EnableCircuitBreake` 可以开启 Hystrix 的原理。