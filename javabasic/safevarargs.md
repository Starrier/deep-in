# @SafeVarargs

在Java SE 5中，引入了可在方法参数列表中使用 可变长度的参数。

```java
public void test(User ... user)
```

当遇到可变参数的方法时，编译器会将可变参数形式的形式参数转化为一个数组。但是，Java编程语言并不允许创建泛型数组。

但是如果参数类型是 **List&lt;String&gt;** 或者 **ArrayList&lt;String&gt;** 相关容器类的话，都属于**非具体化类型\(non-reifiable types\)**。非具体化类型是指在运行时\(runtime\)并不完整的类型。在编译时，非具体化类型经过了一个名为「类型擦除」的过程，编译器删除了与类型参数相关的信息。这将保证Java运行库与那些诞生在Java泛型之前的应用程序之间的二进制兼容性。由于在编译时，类型擦除操作删除了来自参数化类型的相关信息，因此它们是不完整的。

一个参数化类型的变量引用一个非参数化类型的对象，将会导致堆污染。这种情况只会在程序执行了一些在编译时会出现未经检查的警告\(unchecked warning\)的操作时发生。不管是在编译时\(符合编译时类型检查规则的约束\)，还是在运行时，如果无法验证一个表示参数化类型的操作\(例如：类型强转、方法调用\)是否正确，就会产生一个未经检查的警告。

```java
List list = new ArrayList<Integer>();
List<String> lists = list;       // unchecked warning
list.add(0, new Integer(1)); // another unchecked warning
String str = lists.get(0);    
```

 在类型擦除的时候，类型  **ArrayList&lt;Integer&gt;** 和 **List&lt;String&gt;** 将会分别变为 **ArrayList** 和 **List**。变量 **list** 具有参数化类型 **List&lt;String&gt;,**编译器编译时将会产生一个未经检查的警告；编译器无法确定，而且我们也知道 **JVM** 在运行时无法确定变量 **list** 是否引用了类型 **List&lt;String&gt;**，于是堆污染就产生了。

因此，在编译时，编译器在 add 语句处生成了又一个未经检查的警告。编译器无法确定变量 l 引用的类型是 **List&lt;String&gt;** 还是  **List&lt;Integer&gt;** \(或者其他可能产生堆污染的情况\)。

 不过，编译器不会在get语句处生成一个警告或错误。这个语句是有效的；程序将调用  **List&lt;String&gt;.get** 方法并返回一个字符串对象。不过，在运行时，get语句将会抛出一个类型转换异常java.lang.ClassCastException。 

你声明了一个具有参数化类型的可变参数方法。你应该确保方法的主题部分不会抛出类型转换异常（ClassCastException），或由于对可变参数操作不当而导致的其他类似的异常，你可以通过下列选项来忽略编译器产生的警告信息：

在静态的非构造方法的声明上添加注解 @SafeVarargs 和注解 @SuppressWarning 不同的是，注解 @SafeVarargs 是方法约定的一部分，这个注解断言在方法的视线中会正确地处理可变参数形式的形式参数。

如果开发者确信某个使用了可变长度参数的方法，在与泛型类一起使用时不会出现类型安全问题，就可以用这个注解声明。

@SafeVarargs 注解只能用在参数长度可变的方法或构造方法上，且方法必须声明为 static 和 final，否则会出现编译错误。一个方法使用 @SafeVarargs 注解的前提是，必须确保这个方法的实现中对泛型类型参数的处理不会引发类型安全问题。

