# @FunctonalInterface

Single Abstract Method interfaces

接口中只有一个抽象方法

```text
// 正确的函数式接口
@FunctionalInterface
public interface TestInterface {
    // 抽象方法
    public void sub();
    // java.lang.Object中的public方法
    public boolean equals(Object var1);
    // 默认方法
    public default void defaultMethod(){}
    // 静态方法
    public static void staticMethod(){}
}

// 错误的函数式接口(有多个抽象方法)
@FunctionalInterface
public interface TestInterface2 {
    void add();
    void sub();
}
```

特点：

1. 接口有且只有一个抽象方法
2. 允许定义静态方法
3. 允许定义默认方法
4. 允许 java。lang.Object  中的 public 方法
5. 该注解不是必须的，如果一个接口符合“函数式接口”定义，那么有无注解都可以。



