---
title: FunctionalInterface
date: 2019-02-19 20:24:06
tags: Java8
index_img: ../static/functionalinterface.jpeg
---

## FunctionalInterface

### 函数式接口和普通接口的区别：

* Java 普通接口：指的是一些具有相同属性和行为的类抽象。
* 函数式接口：也是同样的理解方式，它是对一些相似的方法的抽象。

如何定义一个函数式接口：

 1. 添加 `@FunctionalInterface`
 2. 有且只有一个抽象方法，可以有多个 default 方法和 static 方法，但是这些方法都必须实现。

其中，`@FunctionalInterface` 注解也可以不加，完全是可以通过编译的，但前提是这个接口满足上面的第二个注意点。加上注解，可以检查出编译错误，当接口不符合函数式接口定义时，编译器会报错。

接口里允许定义 java.lang.Object 里的 public 方法。因为任何一个函数式接口的实现，默认都继承了 Object 类，包含了来自 java.lang.Object 里对这些抽象方法的实现。

比如：

```java
@FunctionalInterface
interface test{
    void test1(String message);

    @Override
    boolean equals(Object object);
}
```

定义一个泛型类型的函数式接口：

```java
@FunctionalInterface
public interface test<T,R>{
    R caculate(T t);
}
```

定义一个具体的函数式接口：

```java
@FunctionalInterface
public interface test{
    void print(Integer i);
}
```

Java 的接口可以当做函数的参数，那么函数式接口自然也可以，这就叫**行为参数化**。

### Java8 中定义好的函数式接口：

* jva.lang.Runable
* java.util.Comparator
* java.util.function.Predicate
* java.util.function.Consumer