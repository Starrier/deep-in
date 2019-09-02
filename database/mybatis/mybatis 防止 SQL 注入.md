# MyBatis 防止 SQL注入

## SQL 注入起因

> SQL注入是一种常见的攻击方式，攻击者或者误操作者通过表单信息或者URL输入一些异常的参数，传入服务端进行SQL处理，可能会出现这样的情况delete from app_poi where poi_id = (输入参数)：

输入参数：10 or 1 = 1

SQL拼接：delete from app_poi where poi_id = (10 or 1 = 1)

执行结果：app_poi中的所有数据都会被delete

这种问题出现的原因可能是攻击者故意为之，也可能是误操作，那么服务端该如何处理，避免这样的问题出现呢？

## MyBatis

MyBatis是一种半自动化持久化框架，所有SQL操作都需要我们通过配置文件或者注解的方式手动编辑，它提供了一种类函数的功能，用户提供输入，MyBatis根据输入类型和输入参数进行验证，如果参数没有问题，那么MyBatis就针对合法的输入提供输出，即

用户输入参数+用户输入类型 ---> MyBatis检测 ---> 返回输出

接下来通过配置文件和注解两种方式来进行说明

1. 配置文件：

```sql
<delete id="deletePoiById" parameterType="java.lang.Integer">
    DELETE
    FROM app_poi
    where poi_id = #{poiId}
</delete>
```

2. 基于注解

```java
@delete("delete from app_poi where poi_id = #{poiId}")
public void deletePoiById(@Param("poiId") Integer poiId);
```

MyBatis对用户输入的数据会检测其类型是否和parameterType标识的类型一致，如果一致，则进行后续拼接处理，否则抛出异常，SQL就不会执行

对于 `10 or 1 = 1` 这种输入，和 java.lang.Integer 类型不同，就会 abort 并抛出异常

### 原理

MyBatis 使用 #{} 防止 SQL 注入，其实使用的是 PreparedStatement，PreparedStatement 会对执行 SQL 进行预编译，这个过程是发生在数据库服务端，也就是说，对于 PreparedStatement 的 SQL 需要两次网络请求，首次是获取 PreparedStatement，第二次才是发起 SQL 执行；

PreparedStatement 会对 SQL 中输入的参数进行检测，并在 SQL 都是用问号来设置占位符，即只允许传入一个参数，像 `（10 or 1 = 1）` 这种明显不会被占位符所接受

#### String类型SQL注入

对于数字类型参数 #{} 可以很好的解决 SQL 注入，其原因不难理解，那么对于 string 类型的呢？Mybatis 只是使用了一个占位符并不能解决该问题

```sql
<delete id="deletePoiById" parameterType="java.lang.String">
    DELETE
    FROM app_poi
    where content = #{content}
</delete>
```

如果 content 的内容是 “美食 or 1 = 1”，那么数据将会被全部清除

解决该问题，还是结合传统方式，使用正则表达式匹配，总结常见的需要摒弃的 string，替换为空即可，例如 string 包含

and|exec|insert|select|delete|update|count|*|%|chr|mid|master|truncate|char|declare|; |or|-|+| 等