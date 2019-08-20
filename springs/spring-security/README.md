# Spring Security

@Secured

支持单一角色或者多个角色之间的任何一个角色，不支持 Spring EL 表达式

@PreAuthorize

注解适合进入方法之前的权限验证，@PreAuthorize 可以将登录用户的 role/permissions 参数传到方法中

@PreAuthorize\("hasRole\('ADMIN'\)"\) 拥有 ADMIn 权限的用户才可以访问

@PreAuthroize\("hasRole\('ADMIN'\) AND hasRole\('DBA'\)"\) 拥有 ADMIN 和 DBA 权限的用户才可以访问

@PreAuthorize\("hasAnyRole\('ADMIN'，‘DBA'\)"\)拥有 ADMIN 或者 DBA 权限的用户才可以访问

@PostAuthorize 在方法执行后再进行权限验证

@PreAuthorize/@@PostAuthorize 注解更适合方法级别的安全，也支持 Spring EL ，提供了基于表达式的访问控制。

