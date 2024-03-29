# Annotation In Spring

# Annotation In SpringMVC

### @RequestMapping

* params :  指定 request 中必须包含某些参数值才让改方法处理。
* headers：指定 request 中必须包含某些指定的 header 才让改方法处理。
* value  ： 指定请求的实际地址，指定的地址可以是 URL Template 模式。
* method：指定请求的 method 类型，如 GET/POST/PUT/DELETE
* consumes:指定处理请求的提交类型\(Content-Type\) 如 application/json
* produces: 指定返回的内容类型，仅当 request 请求头中的（Accept）类型中包含该指定类型才返回。

### @RequestParam 

用在方法的参数前面

### @PathVariable 

路径变量

@



# Annotation In Java

@Deprecated

@CallerSensitive

# Annotation In JPA

### @Entity/@Table\(name=""\)

表明这是一个实体类。一般用于 JPA，两个同时使用，但如果表明和实体类名相同，课省略 @Table。

### @MappedSuperClass

用在父类的 entity 上，父类的属性子类可以继承

### @NoRepositoryBean

一般作用于父类的 repository，有这个注解，spring 不会去实例化该 repository

### @Colum

如果字段名与列名相同，则可以省略

### @Id

表示该属性为主键

@GenerationValue

表示主键的生成策略

### @Transient

表示该属性并非一个到数据库表的字段的映射，ORM 框架将忽略该属性。如果一个属性并非数据库表的字段映射，就务必将其标注为@Transient，否则，ORM 框架默认其注解为 @Basic

### @Base\(fetch=FetchType.LAZY\)：

指定实体的加载方式 

### @JsonIgnore

JOSN 序列化时将 Java Bean 中的一些属性忽略掉，序列化和反序列化都受影响。

@JoinColumn\(name="loginId"\)

一对一：本表中指向另一个表的外键。一对多：另一个表指向本表的外键。

### @OneToOne

### @OneToMany

### @ManyToMany



## Spring

### @SpringBootApplication

@ComponentScan

@Configuration

@EnableAutoConfiguration

@RestController

### @Controller



### @Autowired

### @Service

### @Bean

@Value

### @Inject

### @Qualifier

### @Resource

### @Response



### 全局异常处理

@ControllerAdvice

@ExceptionHandler

@JsonIgnore

```text
@JsonIgnore 作用是json序列化时将java bean中的一些属性忽略掉,序列化和反序列化都受影响。
```

@Ordered

控制类的 加载顺序

@DependsOn

控制 Bean 的初始化顺序

