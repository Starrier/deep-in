---
title: jvm-parameter-command
date: 2019-02-17 20:43:25
tags: JVm
index_img: ../post_img/jvm-parameter-command.jpeg
---

## JVM-Command

### **内存相关**

 1. 表示设置 JVM 启动内存的最小值为 20M，必须以 M 为单位：

```Java
-Xms20M
```

 2. 表示设置 JVM 启动内存的最大值为 20M，必须以 M 为单位。将 -Xmx 和 -Xms 设置为一样可以避免 JVM 内存自动扩展。打的项目 -Xmx 和 -Xms 一般设置为 10G 20G 或者更大。

```Java
-Xmx20M
```

 3. 表示输出虚拟机中 GC 的详细情况：

```Java
-verbose:gc
```

 4. 表示可以设置虚拟机栈的大小为 128 K

```java
-Xss128k
```

 5. 表示设置本地方法栈的大小为 128 k。但 HotSpot 并不区分虚拟机栈和本地方方法栈，因此对于 HotSpot 来说这个参数是无效的：

```Java
-Xoss128k
```

 6. 表示 JVM 初始分配的永久代的最大容量，必须以 M 为单位，大部分情况下这个参数默认为 64 M。

 ```Java
-XX:PermSize=10M
 ```

 7. 表示 JVM 允许分配的永久代的最大容量，必须以 M 为单位，大部分情况下这个参数默认为 64 M

 ```Java
-XX:MaxPermSize=10M
 ```

 ### 垃圾回收管理

 1. 表示关闭 JVM 对类的垃圾回收

 ```Java
-Xnoclassgc
 ```

 2. 表示可以看到每次 GC 前后堆内存布局

```Java
-XX:+PrintHeapAtGC
```

 3. 在控制台打印 GC 信息

```Java
-XX:+PrintGC
```

 4. 表示在控制台打印 GC 的具体细节

``` Java
-XX:PrintGCDetails
```

 5. 表示让 JVM 使用 G1 垃圾收集器

 ```Java
-XX:+UseG1GC
 ```

 6. 表示年轻代的大小

``` Java
-Xmn
```

 7. 设置年轻代（包括 Eden 和两个 Survivor 区）与年老代的比值（除去持久代）。设置为 4，则年轻代与年老代所占比值为 1：4，年轻代占整个堆栈的 1/5

```Java
-XX:NewRatio=4
```

 8. 设置年轻代中 Eden 区与 Survivor 区的大小比值。设置为 4，则两个 Survivor 区与一个 Eden 区的比值为 2:4，则一个 Survivor 区占整个年轻代的1/6

``` Java
-XX:SurvivorRatio=4
```