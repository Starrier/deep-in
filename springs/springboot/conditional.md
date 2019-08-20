# @Conditional\*\*

### @Conditional\(Test.class\)

注解标注在类上，表示该类下所有 @Bean 都会启用配置，也可以标注在方法上，只对被标注的类启用配置。

### @ConditionalOnBean

仅在当前上下文中存在某个对象时，才会实例化一个 Bean

### @ConditionalOnClass

某个 class 位于类路径上，才会实例化一个 Bean

该注解的参数对应的类必须存在，否则不解析该注解修饰的配置类。即，如果当前 classpath 下存在指定类，则将当前的配置装载如 Spring 容器。比如，maven 中引用的 velocity，视图就使用 velocity，若引入的是 freemarker，则使用 freemarker。

### @ConditionalOnExpression

当表达式为 True，才会实例化一个 Bean

### @ConditionalOnMissingBean

仅当前上下文中不存在某个对象时，才会实例化一个 Bean

如果存在它修饰的类的 bean，则不需要再创建这个 bean，可以给该注解传入参数。比如，@ConditionalOnMissingBean\(name="example"\) 表示如果 name 为 example 的 bean 存在，则该注解修饰的代码块不执行。

### @ConditionalOnMissingClass

某个 class 类路径上不存在的时候，才会实例化一个 Bean

### @ConditionalOnNotWebApplication

不是 Web 应用























