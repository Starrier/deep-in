---
title: static
date: 2019-02-14 13:24:25
tags: Java
index_img: ../post_img/static.jpeg
---

## static

### 1. 修饰成员变量

### 2. 修饰成员方法

### 3. 静态块

### 4. 静态导包

``` Java
 // PrintTest.java
 package org.starrier.test;
 public class PrintTest{
     public static void print(Object object){
         System.out.println(object);
     }
 }

 // Test.java
 import static org.starrier.test;

 public class Test{
     public static void main(String [] args){
         print("Hello World");
     }
 }
```

## 总结

 1. 用来修饰成员变量，该变量就会成为类的成员，从而实现所有对象对该成员变量的共享。
 2. 用来修饰成员方法，将其变为类方法，可以直接使用**类名.方法名**的方式调用，常用于工具类。
 3. 静态块，将多个类成员放在一起初始化，是程序更加规整。
 4. 静态导包，将类的方法直接导入到当前类，从而直接使用**方法名**即可实现类方法的调用。

当 Java 类中包含 static 修饰的静态字段，或者有使用 static｛｝块包裹的代码时，编译后便会在字节码文件中包含一个名为&lt;clinit&gt;\(\) 的方法，JVM 在吃刷阶段便会调用该方法。注意，该方法仅可以被 Java 编译器生成把那个由 JVM 调用，程序开发者无法自定义一个同名方法，更无法直接在 Java 程序中调用。

Java 的初始化顺序：

父类的静态构造成员初始化 ——》 父类的静态代码块 ——》 子类的静态成员初始化 ——》 子类的静态代码块 ——》 父类的代码块 ——》 父类的构造方法 ——》 子类的代码块 ——》 子类的构造方法