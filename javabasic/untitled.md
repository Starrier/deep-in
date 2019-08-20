# 可变参数

### 函数签名

```text
public void test(Integer... args)
```

 只要在一个形参的“类型”与“参数名”之间加上三个连续的“.”（即“...”，英文里的句中省略号），就可以让它和不确定个实参相匹配。而一个带有这样的形参的方法，就是一个实参个数可变的方法。

#### Tips

1. 不定项参数必须放在参数列表最后一个
2. 不定项参数只能有一个（多个，必然有一个不是最后一个）。
3. 重载时，运行优先级问题。\(系统优先匹配确定参数，然后匹配不确定参数\)
4. 本质上是一个二维数组
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

```java
6.
public staitc void test(int ... args){}
public static void test(INteger ... args){}

//Invoke
test(1,2);
test(new Integer(1),new Integer(2))
// Above of all,compile failure
```

{% file src="../../.gitbook/assets/mutilvariable.md" %}

