# Interface

接口，英文称作interface，在软件工程中，接口泛指供别人调用的方法或者函数。从这里，我们可以体会到Java语言设计者的初衷，它是对行为的抽象。在Java中，定一个接口的形式如下：

```java
public interface InterfaceName {

}
```

　　接口中可以含有 变量和方法。但是要注意，接口中的变量会被隐式地指定为 public static final 变量（并且只能是 public static final 变量，用 private 修饰会报编译错误），而方法会被隐式地指定为 public abstract 方法且只能是 public abstract 方法（用其他关键字，比如 private、protected、static、 final 等修饰会报编译错误），并且接口中所有的方法不能有具体的实现，也就是说，接口中的方法必须都是抽象方法。从这里可以隐约看出接口和抽象类的区别，接口是一种极度抽象的类型，它比抽象类更加“抽象”，并且一般情况下不在接口中定义变量。

　　要让一个类遵循某组特地的接口需要使用 implements 关键字，具体格式如下：

```text
class ClassName implements Interface1,Interface2,[....]{
}
```

　　可以看出，允许一个类遵循多个特定的接口。如果一个非抽象类遵循了某个接口，就必须实现该接口中的所有方法。对于遵循某个接口的抽象类，可以不实现该接口中的抽象方法。

