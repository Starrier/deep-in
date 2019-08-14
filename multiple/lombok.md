---
title: lombok
date: 2019-03-12 22:57:00
tags: Utils

---

# lombok

## 常用注解

### `@Data`

注解在类上，会为类的所有属性自动生成 setter/getter、equals、canEqual、hashCode、toString 方法，如果字段属性被声明为 final，则不会为该属性生成个 setter 方法。

### `@Getter`/`@Setter`

`@Data` 包含了多种特性，可以单独在属性上注解，来获取 getter/setter 方法。

### `@NonNull`

该注解在属性或构造器上，Lombok 会生成一个非空的声明，可用于参数校验，可以避免空指针。

`@NotNull` （Bean 的校验框架）注解用在指明一个参数、字段或者方法的返回值不可以为 null，是标准化的。
`@NonNull` （缺陷检查框架）变量被其修饰时，IDE 会警告可能有崩溃的风险。这个是一个静态分析的方法，运行时不报任何警告。目前只有 IDEA 支持。

### `@Cleanup`

可以帮我们自动调用 close() 方法。

```java
@Cleanup InputStream in = new FileInputStream(args[0]);
@Cleanup OutStream out = new FileOutputStream(args[1]);
byte [] b = new byte[1000];
while(true){
    int r = in.read(b);
    if(r==-1){
        break;
    }else{
        out.write(b,0,r);
    }

}

// 在函数签名中添加抛出的异常类型 IOException
InputStream in = new FileInputStream(args[0]);
    try {
      OutputStream out = new FileOutputStream(args[1]);
      try {
        byte[] b = new byte[10000];
        while (true) {
          int r = in.read(b);
          if (r == -1) break;
          out.write(b, 0, r);
        }
      } finally {
        if (out != null) {
          out.close();
        }
      }
    } finally {
      if (in != null) {
        in.close();
      }
    }
```

### `@EqualsAndHashCode`

默认情况下，会使用所有的非静态和非瞬态的属性来生成 equals 和 hashCode，也可以通过 exclude 注解来排除一些属性。

### `@toString`

注解在类上，Lombok 会为类生成一个 toString() 方法，默认情况下，会输出类名，所有属性（按照定义的属性顺序），用 `,` 隔开。

**待添加**：对内部属性的使用场景。

### `@NoArgsConstructor`/`@RequiredArgsConstructor` and `@AllArgsConstructor`

无参构造器，部分参数构造器，全参构造器。Lombok 无法实现多种参数构造器的重载。

## Lombok 的运行原理

对注解解析的方式：

1. **运行时解析**：必须将 @Retention 设置为 RUNTIME，这样就可以通过反射拿到该注解，java.lang.reflect 反射包提供了一个接口
2. **编译时解析**：编译时有两种，一种是 Annotation Processing Tool，另一种是 Pluggable Annotation API

Lombok 使用的是编译时解析的第二种。

- javac对源代码进行分析，生成了一棵抽象语法树（AST）
- 运行过程中调用实现了“JSR 269 API”的Lombok程序
- 此时Lombok就对第一步骤得到的AST进行处理，找到@Data注解所在类对应的语法树（AST），然后修改该语法树（AST），增加getter和setter方法定义的相应树节点
- javac使用修改后的抽象语法树（AST）生成字节码文件，即给class增加新的节点（代码块）

## Lombok 的优缺点

### 优点：

 1. 能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
 2. 让代码变得简洁，不用过多的去关注相应的方法
 3. 属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

### 缺点：

1. 不支持多种参数构造器的重载
2. 虽然省去了手动创建getter/setter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度。