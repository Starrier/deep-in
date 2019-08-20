# @Primary

如果同一个类型有多个实例，但我们需要注入一个时，必须采取措施，否则 Spring 容器会报错

```java
...required a single bean,but 2 were found...
```

@Primary 

在众多相同的 bean 中，优先选择用 @Primay 注解的 bean（该注解加在各个 bean 上）



