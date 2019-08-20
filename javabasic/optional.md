# Optional

## Optional Class

```java
public final class Optional<T>
```

### 基于值:

1. final 类型和不可变（可能会包含可变对象的引用）
2. 有 equals、hasCode、toString 方法的实现，它是通过实例的状态计算出来的，而不是通过其他的对象或变量去计算。
3. 不会使用身份敏感的操作，比如在两个实例之间引用相等性、hasCode 或者内在的锁。
4. 判断两个值相等仅仅通过 equal\(\)，而不会使用 == 判断。
5. 它不提供构造方法，它通过工厂方法创建它的实例，这不保证返回实例的一致性。
6. 当它们相等时，它是可以自由替换，如果 x 和 y 调用 equal\(\) 方法返回 true，那么将 x 和 y 任意交换，它的结果不会产生任何变化。

### orElse\(\)

接受一个参数，如果存在，返回这个值本身，否则返回这个参数。（同时还会新建一个）

### orElseGet\(\)

接受一个 Supplier,如果存在，返回这个值本身，否则返回 Supplier 对象。

### empty\(\)

返回一个空的 Optional 对象。

### of\(\)

接受一个 T 参数，T 必须是 NotNull，返回一个 Optional 对象

### ofNullable（）

接收一个 T 参数，如果 T 为 null，它会调用 empty\(\) 方法，如果不为 null，则调用 of\(\)。

### isPresent\(\)

如果这个对象的值不为 null，返回 true，否则返回 false。

### get\(\)

如果这个值存在，则返回这个值，如果这个值为 null，则抛出异常。

### ifPresent\(\)

检查是否有值的另一个选择是 ifPresent\(\)，该方法除了执行检查，还接受一个 Consumer（消费者）参数，如果对象不是空的，就对执行传入的 Lambda。

```java
opt.ifPresent(u-> assertEquals(user.getEnamil(),u.getEmail());
```

### orElseThrow\(\)

它会在对象为空的时候抛出异常，而不是返回备选的值

```java
Optional.ofNullable(user)
       .orElseThrow(() -> new IllegalArgumentException());
```

