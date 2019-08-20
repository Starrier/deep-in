# Java 堆栈溢出

## Java 堆内存溢出

## 一  java.lang.OutOfMemoryError;Java heap space

#### 问题描述

设置的 JVM 内存太小，对象所需内存太大，创建对象时分配空间，就会抛出这个溢出.

```java
public class HeapOutOfError{
        public static void main(String[] args) {
            List<byte[]> list = new ArrayList<>();
            int i=0;
            while (true) {
                list.add(new byte[5 * 1024 * 1024]);
                System.out.println("count is " + (++i));
            }
        }
    }
```

解决方案

1. -Xms
2. -Xmx

##  问题描述

Java 中内存泄露是一些对象不再被应用程序使用，但垃圾收集无法识别的情况。因此，这些未使用的对象仍然在 Java 堆空间中无限期地存在。不停地堆积，最终会触发  java.lang.OutOfMemoryError。





