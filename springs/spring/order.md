---
description: '@Order 注解主要是用来控制配置类的加载顺序'
---

# @Order

@Order 标记出现在 Spring 2.0 中，但在 Spring 4.0 之前，@Order 注解只支持 AspectJ 切面排序。Spring 4.0 对其做了增强，他开始支持对装载在诸如 Lists 和 Arrays 容器中的自动包装（autowired）组件的排序。

在 Spring 内部，对基于 spring.xml 应用，spring 使用 OrderComparator 类来实现排序。对基于注解的应用，Spring 采用 AnnotationAwareOrderComparator 来实现排序。

@Order 标记定义如下

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})
@Documented
public @interface Order {
    int value() default 2147483647;
}
```

这个标记包含一个 value 属性。属性接受整形值。如 1 2，值越小，优先级越高。

### @Order\(number\)

number 越小优先级越高。

