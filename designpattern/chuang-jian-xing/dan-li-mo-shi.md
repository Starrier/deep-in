---
description: 单例对象的类只能允许有一个实例存在。
---

# 单例模式

## 特点

### 优点：

系统内存中该类只存在一个对象，节省了系统资源，对于一些需要频繁创建销毁的对象，使用单例模式可以提高系统的性能。

1. 单例类只有一个实例对象
2. 该单例对象必须由单例类自行创建
3. 单例类对外提供一个访问该单例的全局访问点。

### 缺点：

当想实例化一个单例类时，必须记住使用相应的获取对象的方法，而不是使用 new，可能会给其他开发者造成不必要的困扰，特别是看不到源码的时候。

## 适用场景

1. 需要频繁的进行创建和销毁对象。
2. 创建对象时耗时过多或者耗费资源过多，但又经常用到的对象。
3. 工具类对象。
4. 频繁访问的数据库或文件对象。

### 扩展

1. 单例模式可以扩展为多例模式，这种模式可生成有限个实例并保存在 ArrayList 中。

## 代码实现

### 1.饿汉式（静态变量）\[可用\]

```java
public class Singleton{
     private final static Singelton INSTANCE =new Singleton();
     
     private Singleton(){}
     
     public static Singleton getInstance(){
        return INSTANCE;
     }
}
```

#### 优点：

 这种写法比较简单，就是在类装载的时候就完成实例化，避免了线程同步问题。

#### 缺点：

在类装载的时候就完成实例化，没有达到 Lazy Loading 的效果，如果从始至终都没有使用过这个实例，则会造成内存的浪费。

### 饿汉式（静态代码块）\[可用\]

```java
public class Singleton{
   private static Singleton instance;
   
   static{
      instance=new Singleton()
      }
   private Singleton(){}
   
   public static Singleton getInstance(){
       return instance;
      }
 }
```

### 3. 懒汉式（线程不安全）\[不可用\]

```java
public class Singleton｛
   private static Singleton singleton;
   
   private Singleton(){}
   
   public static Singleton getInstance(){
       if(singelton == null){
          singleton=new Singleton();
       }
       return singleton;
   }
｝
```

### 懒汉式 （线程安全）

只需要对 getSingeltonInstance\(\) 方法加锁，那么在一个时间点只有一个线程能够进入该方法，从而避免被实例化多次。

但是当一个线程进入该方法之后，其他试图进入该方法的线程都必须等待，即使 singelton 已经被实例化了。这会让线程阻塞时间过长，因此存在性能问题，不推荐使用。

```java
public class Single{
   private static Singleton singleton;
   
   pirvate Single(){}
   
   public static synchronized Single getSingleInstance(){
     if (singleton ==null){
       singleton=new Singleton();
       }
       return singleton;
```

### 6.双重检查\[推荐使用\]

```java
public class Singleton{
   
   private static volatile Singleton singleton;
   
   private Singleton(){}
   
   public static Singleton getInstance(){
         if(singleton==null){
               synchronized(Singleton.class){
                   if(singleton==null){
                         singleton=new Singleton();
                         }
                   }
             }
             return singleton;
   }
}
```

### 7. 静态内部类\[推荐使用\]

```java
public class Singleton{
     private Singleton(){}
     
     private static class SingletonInstance{
        private static final Singleton INSTANCE =new Singleton);
     }
     
     public static Singleton getInstance(){
         return SingletonInstance.INSTANCE;
      }         
}   
```

### 8.枚举\[推荐使用\]

```java
public enum Singleton{
  INSTANCE;
  public void whateverMethod(){}
  }
```

