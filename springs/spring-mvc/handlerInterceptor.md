# HandlerInterceptorAdapter


Spring MV C提供的 org.springframework.web.servlet.handler.HandlerInterceptorAdapter 这个适配器，继承此类，可以非常方便的实现自己的拦截器。

```java
//preHandle 在业务处理器处理请求之前被调用。预处理，可以进行编码、安全控制等处理。如果返回 true，则继续调用下一个拦截器，如果返回 false，则中断执行，也就是说，我们想调用的方法不会被执行，但是你可以修改 response 为你想要的响应。
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)throws Exception {    
        return true;    
} 
//postHandle 在业务处理器处理请求执行完成后，生成视图之前执行。后处理（调用了 Service 并返回 ModelAndView，但未进行页面渲染），有机会修改ModelAndView；    
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)throws Exception {    
}    
//afterCompletion 在 DispatcherServlet 完全处理完请求后被调用，也就是说，视图渲染完成或调用方已经拿到响应。可用于清理资源等。返回处理（已经渲染了页面），可以根据ex是否为null判断是否发生了异常，进行日志记录；
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)throws Exception {    
} 
```
## 自定义拦截器

1. 首先配置拦截器

多个拦截器存在时，会按照顺序被执行。

``` java
@Configuration
public class InterceptorConfig extends WebMvcConfigurerAdapter{
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new ManualInterceptor());
    }
}
```

2. 添加拦截器

``` java
public class ManualInterceptor extends HandlerInterceptorAdapter {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return super.preHandle(request, response, handler);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        super.afterCompletion(request, response, handler, ex);
    }
}
```

如果基于 XML 配置使用 Spring MVC，可以利用 SimpleUrlHandlerMapping、BeanNameUrlHandlerMapping 进行 Url 映射（相当于 struts 的 path 映射）和拦截请求（注入 interceptors）。

如果基于注解使用 Spring MVC，可以使用 DefaultAnnotationHandlerMapping 注 入interceptors。

注意无论基于XML还是基于注解，HandlerMapping Bean都是需要在XML中配置的。

```xml
<!--配置拦截器, 多个拦截器,顺序执行 -->  
<mvc:interceptors>    
    <mvc:interceptor>    
        <!-- 匹配的是url路径， 如果不配置或/**,将拦截所有的Controller -->  
        <mvc:mapping path="/" />  
        <mvc:mapping path="/user/**" />  
        <mvc:mapping path="/test/**" />  
        <bean class="com.alibaba.interceptor.CommonInterceptor"></bean>    
    </mvc:interceptor>  
    <!-- 当设置多个拦截器时，先按顺序调用preHandle方法，然后逆序调用每个拦截器的postHandle和afterCompletion方法 -->  
</mvc:interceptors> 
```