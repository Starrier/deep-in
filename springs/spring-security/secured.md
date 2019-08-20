# @Secured

区别在于使用@Secured，而@Secured对应的角色必须要有ROLE\_前缀。

通常在项目里面会实现UserDetails写user类，关键在于getAuthoritie\(\)方法里面生成的role有没有前缀ROLE\_。  
使用@PreAuthorize是可以随意设置的

 @PostAuthorize

@PreAuthorize

 为了使使用Spring的方法级别安全，我们需要用注释一个 @EnableGlobalMethodSecurity类在@Configuration



需要注意的是@EnableGlobalMethodSecurity需要几个参数，如下所示：

* prePostEnabled :确定 Spring Security 前置注释 \[@PreAuthorize,@PostAuthorize,..\] 是否应该启用；
* secureEnabled : 确定 Spring Security 安全注释 \[@Secured\] 是否应该启用；
* jsr250Enabled : 确定 [JSR-250注释](https://en.wikipedia.org/wiki/JSR_250) \[@RolesAllowed..\] 是否应该启用；

@PreAuthorize适合进入方法之前验证授权。 @PreAuthorize可以兼顾，角色/登录用户权限，参数传递给方法等等。@PostAuthorize 虽然不经常使用，检查授权方法之后才被执行，所以它适合用在对返回的值作验证授权。Spring EL提供可在表达式语言来访问并从方法返回 returnObject 对象来反映实际的对象。请参见常见[内置表达式](http://docs.spring.io/spring-security/site/docs/4.0.1.RELEASE/reference/htmlsingle/#el-common-built-in)了解支持表达式的完整列表。让我们回到之前的例子，这一次使用 @PreAuthorize/@PostAuthorize 。

