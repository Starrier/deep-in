# Feign 的 Post 和 Get 多参数传递

Web 开发中，Spring MVC 是支持 GET 方法直接绑定 POJO 的，但 Feign 暂时未覆盖所有 Spring MVC 功能。解决方案

1. 把 POJO 拆分成一个一个单独的属性放在方法参数里。
2. 把方法参数变成 Map 传递。
3. 使用 Get 传递 @RequestBody，但违反 RESTful 规范。



