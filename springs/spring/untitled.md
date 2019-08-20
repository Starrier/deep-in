# Spring bean 之间循环依赖

```text
public class TestA {
    private TestB testB;

    public TestA(TestB testB) {
        this.testB = testB;
    }
    public TestA() {

    }

    public TestB getTestB() {
        return testB;
    }

    public void setTestB(TestB testB) {
        this.testB = testB;
    }
}
```

```text
public class TestB {
    private TestC testC;

    public TestB(TestC testC) {
        this.testC = testC;
    }

    public TestB() {

    }

    public TestC getTestC() {
        return testC;
    }

    public void setTestC(TestC testC) {
        this.testC = testC;
    }
}
```

```text
public class TestC {
    private TestA testA;

    public TestC(TestA testA) {
        this.testA = testA;
    }
    public TestC() {

    }
    public TestA getTestA() {
        return testA;
    }

    public void setTestA(TestA testA) {
        this.testA = testA;
    }
}
```

#### 测试类

```java
public class Test {
    public static void main(String[] args) {
        ApplicationContext context =
         new ClassPathXmlApplicationContext
         ("com/bean/test/applicationContext.xml");
        System.out.println(context.getBean("a", TestA.class));
    }
}
```

![](../../.gitbook/assets/beansdependencycircuit.png)

## 1. 构造器循环依赖

表示通过构造器注入构成的循环依赖，这种依赖是无法解决的，只能抛出BeanCreationException异常表示循环依赖。程序在创建TestA时，构造器需要TestB，去创建TestB，TestB中的构造器又需要TestC，又去创建TestC，TestC的构造器又需要TestA，形成一个环，没有办法创建。

#### 创建配置文件

```java
 <bean id="a" class="com.bean.test.TestA" scope="singleton">
        <property name="testB" ref="b"></property>
    </bean>
    <bean id="b" class="com.bean.test.TestB" scope="singleton">
        <property name="testC" ref="c"></property>
    </bean>
    <bean id="c" class="com.bean.test.TestC" scope="singleton">
        <property name="testA" ref="a"></property>
    </bean>
```

#### 测试程序的执行结果：

```java
Exception in thread "main" org.springframework.beans.factory.BeanCreationException:……
```

分析构造器循环依赖。Spring容器将每一个正在创建的bean标识符放在一个“当前创建bean池”，bean标识符在创建过程中将一直保持在这个池子中，因此在创建bean的时候发现自己已经在这个池子中（创建TestA时，发现需要TestB，TestB又需要TestC，TestC又需要TestA，需要注意的是，这时候TestC需要的TestA并没有创建完成，TestA还在等着后面需要的bean的成功创建他才可以成功创建）

发现这种情况时程序就会抛出 BeanCreationException 异常，注意池子中装的是正在创建的bean，对于创建完毕后的bean就会从这个池子中移除。

## 2. setter 循环依赖

#### 修改配置文件：

```java
 <bean id="a" class="com.bean.test.TestA" scope="singleton">
        <property name="testB" ref="b"></property>
  </bean>
    <bean id="b" class="com.bean.test.TestB" scope="singleton">
        <property name="testC" ref="c"></property>
    </bean>
    <bean id="c" class="com.bean.test.TestC" scope="singleton">
        <property name="testA" ref="a"></property>
    </bean>
```

#### 测试程序的执行结果：

```java
com.bean.test.TestA@37760a63
```

setter方法也是Spring中的默认解决方法，可以解决循环依赖的问题。具体实现原理是：setter注入造成的依赖是通过Spring容器提前暴露刚完成构造器注入但并未完成其他步骤的bean完成。

Spring是先将Bean对象实例化之后再设置对象属性的

Spring先是用构造实例化Bean对象，此时Spring会将这个实例化结束的对象放到一个Map中，并且Spring提供了获取这个未设置属性的实例化对象引用的方法。 结合我们的实例来看，当Spring实例化了TestA、TestB、TestC后，紧接着会去设置对象的属性，此时TestA依赖TestB，就会去Map中取出存在里面的单例TestB对象，以此类推

## 3. prototype 范围的依赖处理

#### 修改配置文件：

```java
 <bean id="a" class="com.bean.test.TestA" scope="prototype">
        <property name="testB" ref="b"></property>
    </bean>
    <bean id="b" class="com.bean.test.TestB" scope="prototype">
        <property name="testC" ref="c"></property>
    </bean>
    <bean id="c" class="com.bean.test.TestC" scope="prototype">
        <property name="testA" ref="a"></property>
    </bean>
```

执行测试类，程序还是抛出了`BeanCreationException`异常

**对于“prototype”作用域bean，Spring容器无法完成依赖注入，因为“prototype”作用域的bean，Spring容器不进行缓存，因此无法提前暴露一个创建中的Bean。**

