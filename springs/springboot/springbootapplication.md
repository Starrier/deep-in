# @SpringBootApplication

## @SpringBootApplication

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {
    @Filter(type = FilterType.CUSTOM,classes = {TypeExcludeFilter.class}), 
    @Filter(type = FilterType.CUSTOM,classes = {AutoConfigurationExcludeFilter.class}
)})
public @interface SpringBootApplication {
    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    Class<?>[] exclude() default {};

    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    String[] excludeName() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};
}

```

> @SpringBootApplication 可以替换为

* @SpringBootConfiguration
* @EnableAutoConfiguration
* @ComponentScan

## @SpringBootConfiguration

```java
Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
}
```

@SpringBootConfiguration 来源于 @Configuration，二者功能都是将当前类标注为配置类，并将当前类里以 @Bean 注解标记的方法实例注入到 Spring 容器，实例名即为方法名。

## @EnableAutoConfiguration

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";

    Class<?>[] exclude() default {};

    String[] excludeName() default {};
}
```

1. @EnableAutoConfiguration 借助 AutoConfigurationImportSelector ，后者通过实现 selectImports\(\) 方法来导出 Configuration。
2. AutoConfigurationImportSelector 类的 selectImports\(\) 方法里面通过调用 Spring.Core 包里 SpringFactoriesLoader 类的 loadFactoryName\(\) 
3. 最终通过 SpringFactoriesLoader.loadFactoryNames\(\) 读取 ClassPath 下的 META-INF/spring.factories 文件来获取所有导出类

即， 从 Classpath 下扫描所有的 META-INF/spring.factories配置文件，并将 spring.factories 文件中的 EnableAutoConfiguration 对应的配置项通过反射机制实例化为对应标注了 @Configuration 的形式的 IoC 容器配置类，然后注入 IoC 容器。

## @ComponentScan

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Repeatable(ComponentScans.class)
public @interface ComponentScan {
    @AliasFor("basePackages")
    String[] value() default {};

    @AliasFor("value")
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};

    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    Class<? extends ScopeMetadataResolver> scopeResolver() default AnnotationScopeMetadataResolver.class;

    ScopedProxyMode scopedProxy() default ScopedProxyMode.DEFAULT;

    String resourcePattern() default "**/*.class";

    boolean useDefaultFilters() default true;

    ComponentScan.Filter[] includeFilters() default {};

    ComponentScan.Filter[] excludeFilters() default {};

    boolean lazyInit() default false;

    @Retention(RetentionPolicy.RUNTIME)
    @Target({})
    public @interface Filter {
        FilterType type() default FilterType.ANNOTATION;

        @AliasFor("classes")
        Class<?>[] value() default {};

        @AliasFor("value")
        Class<?>[] classes() default {};

        String[] pattern() default {};
    }
}

```

@ComponentScan对应于 XML 配置形式中的，用于将一些标注了特定注解的 bean 定义批量采集注册到 Spring 容器中，这些特定的注解大致包括：

* @Controller
* @Entity
* @Component
* @Service
* @Repository



