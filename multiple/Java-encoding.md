---
title: Java-encoding
date: 2019-02-17 22:40:29
tags: java
index_img: ../post_img/java-encoding.jpeg
---

## Java Encoding

Java 默认使用 Unioncode 编码，即，不论什么语言，都是一个字符占两个字节，Java 的 class 文件编码为 UTF-8，而虚拟机 JVM 编码为 UTF-16。UFT-8 编码下，一个中文字符占 3 个字节，一个英文占 1 字节。Java 的 char 默认采用 Unioncode 编码，所以 java 的 char 占两个字节。1 字节 = 8 位。

#### 不同基本数据类型占的字节和位

 1. 一个字节等于 8 位
 2. char 占字节 2 个，所以一个 char 类型可以存储一个汉字。
 3. byte 占字节 1 个，8 位， -128-127
 4. short 占字节 2 个，16 位
 5. int 占字节 4 个，32 位
 6. long 占字节 8 个 ，64 位
 7. float 占字节 4 个，32 位
 8. double 占字节 8 个，64 位

 **boolean**：boolean 表现出 0 1 的 “位” 特性，但存储空间的基本计量单位是字节。JVM 中，boolean 变量作为 int 类型处理，也就是 4 字节；boolean 数组当做 byte 数组处理。

### 一个字符为什么占两个字节

```Java
public static void main(String[] args) {
   System.out.printf("The max value of type char is %d.%n",
            (int)Character.MAX_VALUE);// 65535
   System.out.printf("The min value of type char is %d.%n",
            (int)Character.MIN_VALUE);//0
 }
```

说明 char 的范围是 0 ~ 65535，正好是两个字节所能表示的范围（65535 十六进制是 0xFFFF，一个字节能表示 0xFF，两个字节能表示 0xFFFF），所以说一个 char 占两个字节。

``` Java
char c = "你"；
```

Java的char类型，通常以UTF-16 Big Endian的方式保存一个字符。存放的是 unicode 的值。

### UTF-8

