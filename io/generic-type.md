---
title: generic-type
date: 2019-02-20 21:14:25
tags: Java
index_img: ../post_img/generic-type.jpeg
---

# 泛型

## K ： 键，比如映射的键

### V ： 值，比如 List 和 Set 的内容，或者 Map 中的值。

#### E ： 异常类

#### T ： 泛型

在泛型接口、泛型类和泛型方法的定义过程中，我们常见的如 T、E、K、V 等形式的参数常用于表示泛型形参，由于接收来自外部使用时候传入的类型实参。那么对于不同传入的类型实参，生成的相应对象实例是不是一样的呢？

```java
class Box<T> {
    private T data;
    public Box() { }
    public Box(T data) {
        this.data = data;
    }
    public T getData() {
        return data;
    }
}

```

```java
public class GenericTest {

    public static void main(String[] args) {

        Box<String> name = new Box<String>("corn");

        Box<Integer> integerBox = new Box<>(11);

        System.out.println("name:" + name.getData());

        System.out.println(integerBox.getClass() == name.getClass());
    }

}

```

#### 结果

```text
name:corn
true
```

#### 结论

* 在使用泛型时，虽然传入了不同的泛型实参，但并没有真正意义上生成不同的类型，传入不同泛型实参的泛型类在内存上只有一个，即还是原来的最基本的类型（本示例中为 Box），当然在逻辑上我们还是可以理解成多个不同 泛型类型。
* 原因是，Java 中的泛型这一概念的提出，导致其只是作用域代码编译阶段，在编译过程中，对正确检查泛型结果后，会将泛型的相关信息檫除，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。即，经过编译过后的 class 文件中是不包含任何泛型信息的。泛型信息是不会进入到运行时阶段。
* 泛型类型在逻辑上看成是多个不同的类型，实际上都是相同的基本类型。

### 类型通配符：？（类型形参）

* 类型通配符上限 &lt;? extend 类型&gt; （表示该通配符所代表的类型是 T 类型的子类）
* 类型通配符下限 &lt;? super 类型&gt;  （表示该通配符所代表的类型是 T 类型的父类）

#### 类型通配符上限

在泛型方法中添加上下边界限制时，必须在权限声明与返回值之间的 &lt;T&gt; 上添加上下边界，即在泛型声明的时候添加

```java
public <T extends Number> T showKeyName(Generic<T> container){
    System.out.println("container key :" + container.getKey());
    T test = container.getKey();
    return test;
}
```

### &lt;E&gt;  

&lt;E&gt;  中的 E 表示类型形参，可以接收具体的类型参数，并且此接口定义中，凡是出现 E 的地方均表示相同的接受来自外部的类型实参。

## 泛型的使用

1. 泛型类
2. 泛型接口
3. 泛型方法

### 关于数组

```text
// error
List<String>[] ls =new ArrayList<String>[10];
// OK
List<String>[] ls =new ArrayList<10];
// ok
List<?>[] ls = new ArrayList<?>[10];
```

通过泛型可以定义类型安全的数据结构（类型安全），而无须使用实际的数据类型（可扩展）。这能够显著提高性能并得到更高质量的代码（高性能），因为你可以重用数据处理算法，而无须复制类型特定的代码（可重用），在概念上，泛型类似于 C++ 模版，但实现和功能方面存在明显差异。

基于 Object 的解决方案，存在两个问题：

1. 性能。使用值类型时，必须拆箱装箱，装箱和拆箱都会根据它们自己的类型造成重大的性能损失，同时还会增加堆栈的压力，导致更多的垃圾收集工作，而这对于性能而言，也是不好的。即使是在使用引用类型而不是值类型时，仍然存在性能损失，这是因为必须从 Object 向你要与之交互的实际类型进行强制类型转换，从而造成强制类型转换开销。
2. 基于 Object 的解决方案的第二个问题（通常会更安全）是类型安全，因为编译器允许在任何类型和 Object 之间进行强制类型转换，所以你将丢失编译时类型安全。例如，代码可以正常编译通过，但是在运行时会发生强制类型转换无效的异常。