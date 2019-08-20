# Design Pattern in Spring

## 简单工厂

又称为 静态工厂方法，但不属于 23 种设计模式之一。简单工厂的实质是，由一个工厂类根据传入的参数，动态决定应该创建哪一个产品。

Spring 中的 BeanFactory 就是简单工厂模式的体现，根据传入一个唯一的表示来获取 Bean 对象，但是否是在传入参数后创建还是传入参数前创建，需要根据具体情况决定。

## 工厂方法

定义一个用于创建对象的接口，让子类去决定实例化哪一个类。Factory Method 使一个类的实例化延迟到其子类。

![FactoryMethod](../../.gitbook/assets/factorymethod.png)

## 单例（Singleton）

保证一个类只有一个实例，并提供一个访问它的全局访问点。

Spring 中的单例模式完成了后半句，即提供了全局的访问点 BeanFactroy。但没有从构造器级别去控制单例，这是因为 Spring 管理的是任意的 Java 对象。

## 适配器

将一个类的接口转化成客户希望的另外一个接口。Adapter 模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

Spring 中对于 AOP 的处理中有 Adapter 模式的例子，见如下图：

![AdapterMethod](../../.gitbook/assets/adapter.png)

由于 Advisor 链需要的是 MethodInterceptor \(拦截器\) 对象，所以每一个 Advisor 中的 Advice 都要适配成对应的 MethodInterceptor 对象。

## 包装器（Decorator）

动态地给一个对象添加一些额外的职责。就增加功能来说，Decorator 模式相比生成子类更为灵活。

![DecoratorMethod](../../.gitbook/assets/decoratormethod.png)

  
Spring 中用到的包装器模式在类名上有两种表现：一种是类名包含有 Wrapper，另一种是 Decorator。基本上都是动态地给一个对象添加一些额外的职责。

## 代理（Proxy）

为其他对象提供一种代理，来控制对这个对象的访问 。

从结构上来看和 Decorator 模式类似，但 Proxy 是控制，更像是一种对功能的限制，而 Decorator 是增加了职责。

![ProxyMethod](../../.gitbook/assets/proxymethod.png)

Spring 的 Proxy 模式在 AOP 中有体现，比如 JdkDynamicAopProxy 和 Cglib2AopProxy。

## 观察者（Observer）

定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖它的对象都得到通知并被自动更新。

![ObserverMethod](../../.gitbook/assets/observermethod.png)

Spring 中的 Observeer 模式常用的地方是 listener 的实现。如 ApplicationListener。  


## 策略（Strategy）

定义一系列算法，把它们一个一个封装起来，并且使它们可以相互替换。本模式使得算法可独立于使用它的客户而变化。 

Spring 中在实例化对象的时候用到 Strategy 模式

![](../../.gitbook/assets/strategypattern.webp)

## 模版（Template）

定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，Template Method 使得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤。

Template Method 模式一般需要继承。Spring 中的 JdbcTemplate 在使用时，不需要继承，因为这个类方法太多 但是我们还是想用到JdbcTemplate已有的稳定的、公用的数据库连接，那么我们怎么办呢？我们可以把变化的东西抽出来作为一个参数传入JdbcTemplate的方法中。但是变化的东西是一段代码，而且这段代码会用到JdbcTemplate中的变量。怎么办？那我们就用回调对象吧。在这个回调对象中定义一个操纵JdbcTemplate中变量的方法，我们去实现这个方法，就把变化的东西集中到这里了。然后我们再传入这个回调对象到JdbcTemplate，从而完成了调用。这可能是Template Method不需要继承的另一种实现方式吧。





















