# @WebFilter

@WebFilter 用于将一个类声明为过滤器，该注解会在部署时被容器处理，容器将根据具体的属性配置将相应的类部署为过滤器。

 以下所有属性均为可选属性，但是 value、urlPatterns、servletNames 三者必需至少包含一个，且 value 和 urlPatterns 不能共存，如果同时指定，通常忽略 value 的取值 

| 属性名 | 类型 | 描述 |
| :--- | :--- | :--- |
| filterName | String | 指定过滤器的 name 属性，等价于&lt;filter-name&gt; |
| value |  |  |

```java
@WebFilter(servletNames = {"SimpleServlet"},filterName="SimpleFilter") 
public class LessThanSixFilter implements Filter{...}
```

等价于 web.xml 中的配置

```java
<filter> 
    <filter-name>SimpleFilter</filter-name> 
    <filter-class>xxx</filter-class> 
</filter> 
<filter-mapping> 
    <filter-name>SimpleFilter</filter-name> 
    <servlet-name>SimpleServlet</servlet-name> 
</filter-mapping>
```

```java
@WebFilter("/CountFilter")
public class CountFilter implements Filter {
    private int count;
    private String param;
    private FilterConfig fConfig;
}
```

Tomcat 根据&lt;filter-mapping&gt; 的顺序初始化 Filter，由于上面的代码包含 @WebFilter，相当于在 web.xml 中对同一过滤器 CountFilter 设置了两次 &lt;filter&gt; 和 &lt;filter-mapping&gt;。因此这个过滤器会被初始化两次，所以当 @WebFilter 设置的过滤器被初始化时， String param =getInitParameter（“count” ）得到的字符串为看那个，调用 Integer.parseInt\(param\) 会导致 NumberFormatException 异常。去掉 @WebFilter\("/CountFilter"\) 一切正常。

```java
@WebFilter(urlPatterns = "/*")
@Order(1)
public class MemberFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.err.println("自定义过滤器MemberFilter->doFilter");
    }

    @Override
    public void destroy() {

    }
}
```

配合 @Order 使用，生命周期排序。

