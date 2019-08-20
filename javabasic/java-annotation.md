# Java Annotation

1. @Override 表示当前的方法定义将覆盖父类中的方法。
2. @Deprecated 使用注解，为它的元素编译器发出警告，因为在 @Deprecated 中，是不赞成使用代码的，被弃用的代码。
3. @SuppreWarning 关闭当前不当的编译器警告

## @Target：表示注解可以使用的范围

1. ElementType.CONSTRUCTOR 构造器声明
2. ElementType.FIELD 域声明（包括 enum 实例）
3. ElementType.LOCAL\_VARIABLE 局部变量声明
4. ElementType.METHOD 方法声明
5. ElementType.PACKAGE 参数声明
6. ElementType.PARAMETER 参数声明
7. ElementType.Type 类、接口（包括注解类型）或 enum 声明



```text
指示注释类型所适用的程序元素的种类。如果注释类型声明中不存在 Target 元注释，则声明的类型可以用在任一程序元素上。如果存在这样的元注释，则编译器强制实施指定的使用限制。 例如，此元注释指示该声明类型是其自身，即元注释类型。它只能用在注释类型声明上：
```

```text
@Target（ElementType。）
```

## @Retention： 表示需要什么几倍保存该注解信息，可选的 RetentionPolicy

1. SOURCE 注解将被编译器丢弃
2. CLASS 注解在 Class 文件中可用，但会被 VM 丢弃
3. RUNTIME VM 将在运行期间保留注解，因此可以通过反射机制读取注解信息。

## @Document ：将注解包含在 Javadoc 中

## @Inherited ： 允许子类继承父类中的注解。

Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性作用。包含在 java.lang.annotation 中。

Java 自定义注解和运行时靠反射注解获取







