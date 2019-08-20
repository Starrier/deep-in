# Java Bean 为什么需要一个无参构造函数



JAVAEE中，javaBean是一个很重要的概念  
而JavaBean还有以下的使用习惯  
![clipboard.png](../../.gitbook/assets/javabeannoargsconstructor.png)

首先我们知道，有两种方法为java对象中的元素赋值，

```text
1. 通过类的带参数构造函数的方法
2. 通过类的set方法
```

在javaBean里，一般参数在4个以上的，极不推荐使用带参数构造函数赋值：

```text
 1.  对于一个实体类，对于多个参数，降低可读性
 2. 需要记住构造参数的顺序
 3. 一个参数无效就会造成整个构造函数失效
 4. 当我们要添加新的属性时，不仅要传参数，还要修改构造函数，同时之前所有的构造都要修改。
 5. 必须在对象创建之前给参数赋值，而不可以在对象创建后赋值
```

使用set方法的优势：

```text
 1.  我们可以显式的看到赋值元素的名称
 2. 可以以任何顺序赋值
 3. 可以忽略一些元素的初始化
 4. 添加新属性时，不需要修改类
 5. 可以在对象创建后赋值
```

所以对于javaBean，最好的方法就是只定义一个无参数构造函数，  
然后用set方法来赋值

最后复习一下类的构造器概念：

```text
//constructor with parameters
public Bicycle(int startCadence, int startSpeed, int startGear) {
    gear = startGear;
    cadence = startCadence;
    speed = startSpeed;
}
Bicycle myBike = new Bicycle(30, 0, 8);
//constructor without parameters
public Bicycle() {
    gear = 1;
    cadence = 10;
    speed = 0;
}
Bicycle yourBike = new Bicycle();
```

一个类可以重载多个构造函数，只要他们的参数不同  
当没有申明构造函数时，java将自动创建构造函数  
若已申明带参数构造函数，必须申明无参数构造函数  
否则当你调用无参数构造函数时，将会编译错误  
对于一个普通的没有父类的类：

> _TO a class that has not a explicit super class_  
> then it has an implicit superclass of **Object**,   
> which does have a no-argument constructor.

