---
description: 必须先得到代表字节码的 Class（即，被加载），得到 Class 对象后，反向获取 类对象属性等信息。
---

# 反射

## Class

#### Reflection  / Introspection

 1. Introspection\(内省\)

在运行时检查一个对象类型或者属性，最常见的就是运行时通过 a Instanceof A 来判断 a 的对象类型。

2. Reflection\(反射\)

用来在运行时检查或者修改一个对象的信息，可以用来实现看似不可能的操作，比如访问私有方法，动态创建对象。

### 获取 Class 对象的三种方式

1. Object -&gt; getClass\(\)
2. 任何数据类型（包括基本数据类型）都有一个静态的 class 属性
3. 通过 Class 类的静态方法：forName\(String className\)\(常用\)

```java
// 1.
Test test =new Test();
Class testClass = test.getClass();

//2.
Class test2 = Test.class;

//3.
try{
   // forNama() 只能用于引用类型
   Class test3 =Class.forName("org.starrier.test.Test");
}catch(ClassNotFoundException e){
   e.printStackTrace();
   }
```



## Field

1.  Field getType\(\)    返回这个变量的类型
2.  Field getGenericType\(\) 如果当前属性有签名属性类型就返回，否则就返回 Field.getType\(\)

#### Tips 

1.在使用反射获取或者修改一个变量的值时，编译器不会进行自动的拆箱装箱。使用 Field.set\(Object obj，Object vlalue\)。

2. 使用 Class.getField\(\) 或者 Class.getField\(\) 获取非 public 变量，编译器会 java.lang.NotSuchFieldException。

3. 修改 final 类型的变量导致 IllagelAccessException。因为 Field 继承自 Accessible，我们可以使用 AccessibleObject.setAccessible\(\) 方法告诉安全机制，这个变量可以用。

{% hint style="warning" %}
1. 使用反射会造成一定的性能开销，因为在反射的时候需要引起许多额外操作，比如验证访问权限。
2. 反射也会使一些运行时计算优化失败。
3. setAccessible\(true\) 方法使用不当，也会导致意想不到的结果。比如，运行时虽然你通过了反射修改了变量 a 的值，但其他部分可能还在使用原来的值。
{% endhint %}

## Method

继承的方法（包括重载、重写和隐藏的）会被编译器强制执行，这些方法都无法反射。因此，反射一个类的方法时不考虑父类的方法，只考虑当前类的方法。每个方法都由修饰符、返回值、参数、注解和抛出异常组成。

1. 泛型檫除导致的 NoSuchMethodException，只要传入的参数修改位 Object 就行。
2. 访问不可见的方法导致 IllegalAccessException ，给方法设置 setAccessible\(true\)
3. 反射调用方法时传入错误参数导致 IllegalArgumentException。
4. 声明一个可变参数方法 foo\(Object ... o\)，编译器会使用一个 Object 数组将所有参数传过去。也就是说 foo\(Object ... o\) 相当于 foo\(Object\[\] o\)。





