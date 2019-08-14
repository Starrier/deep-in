---
title: Class Loader
date: 2019-02-13 22:33:28
tags: JVM
index_img: ../static/static.jpeg
---
# JVM Class Loading Process

```java
public class Singleton {
    private static int x = 0;
    private static int y;
    private static Singleton instance = new Singleton();
    private Singleton(){
        x++;
        y++;
    }
    public static Singleton getInstance() {
        return instance;
    }
    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
        System.out.println(singleton.x);
        System.out.println(singleton.y);
    }
}
```

#### 最后的打印结果：

``` text
x:1
y:1
```

```java
public class Singleton {
    private static Singleton instance = new Singleton();
    private static int y;
    private static int x = 0;
    private Singleton(){
        x++;
        y++;
    }
    public static Singleton getInstance() {
        return instance;
    }
    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
        System.out.println(singleton.x);
        System.out.println(singleton.y);
    }
}
```

#### 最后的打印结果：

```text
x:0
y:1
```

## 类的主动使用和被动使用

### 主动使用：

#### 1. 通过 new 关键字会导致类的初始化

#### 2.访问类的静态变量

#### 3.访问类的静态方法

#### 4.对某个类进行反射操作

#### 5.初始化子类会导致父类的初始化

#### 6.启动类

实例化子类的时候  若此类未被加载过，

首先加载是父类的类对象，然后加载子类的类对象，接着实例化父类，最后实例化子类，

若此类被加载过，不再加载父类和子类的类对象。

接下来是加载顺序，当加载类对象时，首先初始化静态属性，然后执行静态块；当实例化对象时，首先执行构造块（直接写在类中的代码块），然后执行构造方法。至于各静态块和静态属性初始化哪个些执行，是按代码的先后顺序。属性、构造块（也就是上面的实例块）、构造方法之间的执行顺序（但构造块一定会在构造方法前执行），也是按代码的先后顺序。

构造代码块，随着构造方法的执行而执行

```java
public class ClassLoader {

    private static int k;

    static{
        System.out.println("父类的静态方法");
    }
    {
        System.out.println("执行父类的构造代码块");
    }

    public ClassLoader() {
        System.out.println("执行父类的构造方法");
    }

    public static void main(String[] args) {
        System.out.println(ClassLoader.k);

    }
}
```

构造代码块与构造方法对于类的加载没有任何关系

```java
public class ClassLoader {
    static{
        a=100;
        b=200;
    }
    private static int a;
    private static int b=1000;
    public static void main(String[] args) {
        System.out.println(ClassLoader.a);
        System.out.println(ClassLoader.b);
    }
}
```

#### 最后的打印结果

```text
100
1000
```

```java
public class ClassLoader {
    private static int a;
    private static int b=1000;
    static{
        a=100;
        b=200;
    }
    public static void main(String[] args) {
        System.out.println(ClassLoader.a);
        System.out.println(ClassLoader.b);
    }
}

```

#### 最后的打印结果

```text
100
200
```

结论：静态代码块中动作看作赋值

### 代码的加载顺序

1. 初始化父类的静态变量、静态代码块，初始化顺序按照出现顺序。
2. 初始化子类的静态变量、静态代码块，初始化顺序按照出现顺序。
3. 初始化父类的成员变量。
4. 执行父类的构造函数
5. 初始化子类的成员变量
6. 构造代码块创建对象时执行