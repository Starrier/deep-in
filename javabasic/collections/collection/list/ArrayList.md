---
title: ArrayList
date: 2019-02-15 10:28:09
tags: DataStruct
index_img: ../post_img/arraylist.jpeg
---

## ArrayList

函数签名：

``` Java
 public class ArrayList<E> extends AbstractList<E>
         implements List<E>,RandomAccess,Cloneable,java.io.Serializable
```

**AbastrctList**: 抽象父类，提供了 List 的方法。添加、删除、修改、遍历等。

**RandomAccess**： 可随机访问。由 Lis 实现，为 List 提供快速访问功能。在 ArrayList 中，我们可以通过元素的序号快速获取元素对象；这就是快速随机访问。

**Clonable**：可拷贝

**Serializable** 可序列化

#### 构造函数

``` Java
ArrayList()

ArrayList(int capacity)

ArrayList(Collection<? extends E> Collection) 
```
