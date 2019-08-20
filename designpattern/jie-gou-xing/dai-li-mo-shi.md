# 代理模式

## 定义

由于某些原因需要给某对象提供一个代理以控制对该对象的访问。这时，访问对象不适合或不能直接引用目标对象。

## 特点

### 优点

1. 代理模式在客户端与目标对象之间起到一个中介作用和保护目标对象的作用。
2. 代理对象可以扩展目标对象的功能
3. 代理模式能将客户端与目标对象分离，在一定程度上降低了系统的耦合度。

### 缺点

1. 在客户端和目标对象之间增加一个代理对象，会造成请求处理速度变慢
2. 增加了系统的复杂度。

## 结构

### 抽象主题（Subject）

通过接口或抽象类声明真实主题和代理对象实现的业务方法。

### 真实主题（Read Subject）

实现了抽象主题中的具体业务，是代理对象所代表的真实对象，是最终要引用的对象。

### 代理类（Proxy）

提供了与真实主题相同的接口，其内部含有对真实主题的引用，它可以访问，控制或扩展真实主题的功能。

## 应用场景

1. 远程代理，这种方式通常是为了隐藏目标对象存在与不同地址空间的事实，方便客户端访问。例如，用户申请某些网盘空间时，会在用户的文件系统中建立一个虚拟的硬盘，用户访问虚拟硬盘时实际访问的是网盘空间。
2. 虚拟代理，这种方式通常用于要创建的目标对象开销很大时。例如，下载一幅很大的图像需要很长时间，因某种计算比较复杂而短时间无法完成，这时可以先用小比例的虚拟代理替换真实的对象，消除用户对服务器慢的感觉。
3. 安全代理，这种方式通常用于控制不同种类客户对真实对象的访问权限。
4. 智能指引，主要用于调用目标对象时，这样当该对象没有被引用时，就可以自动释放它。
5. 延迟加载，为了提高系统性能，延迟对目标的加载。例如，Hibernate 就存在对属性的延迟加载和关联表的延时加载。

### 扩展

代理类中包含对真实主题的引用，这种方式存在两个缺点。

1. 真实主题与代理主题一一对应，增加真实主题也要增加代理。
2. 设计代理以前真实主题必须事先存在，不太灵活。采用动态代理模式可以解决以上问题。如 Spring AOP。

## 代码实现

### 接口类

```java
public interface Delivery {
    void accept();
}
```

### 被代理对象

```java
public class Husband implements Delivery{
    @Override
    public void accept() {
        System.out.print("accept delivery");
    }
}
```

### 代理类

```java
public class WifeProxy implements Delivery{
    private Husband husband;
    public WifeProxy(Husband husband) {
        this.husband = husband;
    }
    @Override
    public void accept() {
        husband.accept();
    }
}
```

### 测试

```java
public class Client {
    public static void main(String[] args) {
        Husband husband = new Husband();
        WifeProxy wifeProxy = new WifeProxy(husband);
        wifeProxy.accept();
    }
}
```

### 运行结果

```java
accept delivery
```

