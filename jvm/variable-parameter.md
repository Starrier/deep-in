---
title: variable parameter
date: 2019-02-13 15:40:02
tags: Java
index_img: ../post_img/variable-parameter.jpeg
---

## 可变参数

Java 要求实参和形参的数量和类型必须一致，而形参的数目是在方法定义时就已经确定了。重载允许我们扩展参数的类型和数目，但如果需求不同的内容过多，就会产生过多的重载方法。

在这种情况下，我们可以用数组来包裹多个参数，然后将其作为参数进行传递。

在不确定方法需要处理的对象的数量时可以使用可变长参数，会使得方法调用更简单，无需手动创建数组 new T[]{…}。与“数组包裹”的方法相比，真正的实参个数可变的方法，在调用时更为简单，含义也更清楚，但存在自身的限制。

### 函数签名

```text
public void test(Integer... args)
```

 只要在一个形参的“类型”与“参数名”之间加上三个连续的“.”（即“...”，英文里的句中省略号），就可以让它和不确定个实参相匹配。而一个带有这样的形参的方法，就是一个实参个数可变的方法。

#### Tips

1. 不定项参数必须放在参数列表最后一个
2. 不定项参数只能有一个（多个，必然有一个不是最后一个）。
3. 重载时，运行优先级问题。\(系统优先匹配确定参数，然后匹配不确定参数\)
4. **不定参数会被 Java 转型成数组，所以本质上是一个二维数组**
5. 与数组包裹方法相比，存在泛型不可用。
6. 自动装箱和自动拆箱问题

```java
3.
public void method(String ... args);
public void method(String s1);
public void method(String s1,String s2);

public static void main(String[] args){
    method("test1");              // invoke method(String s1)
    method("test1","test2");      // invoke method(String s1,String s2);
}
```

```java
4.
public void method(int...args);
public void method(int[] args); 
```

因为可变参数最后会被编译成数组，所以不能同时存在。可变参数兼容数组，反之不成立。

```java
5.
public static void test(T... args){}// compile failure

public static void test(T [] args){
     List<Integer> integers = Arrays.asList(new Integer(1), new Integer(2));
         integers.stream()
                 .filter(integer -> integer < 10)
                 .collect(Collectors.toList())
                 .forEach(integer -> System.out.println(integer));
}// compile successfully
```

6.

```java
public staitc void test(int ... args){}
public static void test(INteger ... args){}

//Invoke
test(1,2);
test(new Integer(1),new Integer(2))
// Above of all,compile failure
```

7. 可变参数不可以与泛型一起使用，会导致 “generic array creation” 错误。

8. 避免带有可变长度的方法重写。

9. 重写带有可变参数的方法。

``` Java
Class Base{
    public void print(String... args){
        System.out.println("Base----Test");
    }
}

Class Sub extends Base{
    @Override
    public void print(String [] args){
        System.out.println("Sub-----Test");
    }
}
class variableParameters(){

     public static void main(String [] args){
         Base base = new Sub();
         base.println("hello"); //pass

         Sub sub = new Sub();
         sub.println("hello"); //error
     }
 }

```

base 对象将子类对象做了向上转型，形参列表是由父类决定的，所以可以通过。而 sub 调用自身复写的方法，参数类型不匹配也不会父类去匹配，所以导致类型不匹配错误。

10. Object... 参数问题

``` Java
public void test(Object... args){
    System.out.println(args.length);
}

test(new String[]{"1","2","3"});//3
test(100,new String[]{"1","2"});//2
test(new Integer[]{1,2,3})//3
test(100,new Integer[]{1,2})//2
test(1,2,3);//3
test(new int[](1,2,3));//1

```

int[] 无法转型为 Object[] ,只会被当成一个数组对象。Integer[] 可以转型为 Object[],可以成为一个对象数组。

11. 反射方法调用时的注意事项

``` Java
public class Test(){
    public static void test(String... args){
        System.out.println(args.length);
    }

    public static void main(String [] args){
        String [] varArgs =new String[]{"a","b"};
        try{
            Method method = Test.class.getmethod("test",String[].class);
            // Object invoke(Object obj,Object...args);
            method.invoke(null,varArgs);// varArgs.length =2 error
            method.invoke(null,(Object[])varArgs);// String[]  转换为 Object[],varArgs.length =2.error.
            method.invoke(null,(Object) varArgs);// 将整个 String [] 转换为 Object，varArgs.length= 1，right.
            method.invoke(null,new Object[]{varArgs});//Object[] 长度为 1，right。等价于第三个方法调用。
        }catch(Exception e){
            e.printStackTrace();
        }
    }
}
```

11. 调用的参数也可以是 0(等价于空数组)

``` Java
test();
```