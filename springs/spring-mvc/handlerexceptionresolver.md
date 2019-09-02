# HandlerExceptionResolver

SpringMVC 异常处理器

1. HandlerExceptionResolver(推荐@ExceptionHandler)
2. ResponseStatusExceptionResolver（默认处理 @ResponseStatus）
3. DefaultHandlerExceptionResolver（默认异常处理器）

异常处理的具体实现是在 SpringMVC 核心类的 DispatcherServlet。

`processDispatchResult(processedRequest,response,mappedHandler,mv,dispatchException)`

```java
/**
     * Resolve the exception by iterating over the list of configured exception resolvers.
     * The first one to return a ModelAndView instance wins. Otherwise {@code null} is returned.
     */
    @Override
    public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response,
                                         Object handler,
                                         Exception ex) {
        if (resolvers != null) {
            for (HandlerExceptionResolver handlerExceptionResolver : resolvers) {
                ModelAndView mav = handlerExceptionResolver.resolveException(request, response, handler, ex);
                if (mav != null) {
                    return mav;
                }
            }
        }
        return null;
  }
```
1. 异常处理器只有当返回的 ModeAndView 不是空的时候才会返回最终的异常视图，当异常处理返回的 ModelAndView 如果是空，那么它将会继续去下一个异常处理器。
2. 异常解析器是有执行顺序的，我们在合适的场景可以定义自己的 order 来决定哪个异常解析器优先执行，order 越小，越先执行。

当异常返回的视图 ModelAndView 不是空的时候，DispatchServlet 最终会重定向到执行 View。


这是spring默认实现，也就是说，我们没有重写的话，spring是这样执行的，从命名来瞎说就是。如果出现异常（private List<HandlerExceptionResolver> resolvers;），处理异常解析器就会非空，通过循环异常解析器处理resolvers中的异常，然后处理。最后返回null也就是我们之前所说的不做任何错误页面的那种处理。然后处理异常打印异常信息是在抽象类里面完成的：

springmvc默认使用了ResponseStatusExceptionResolver来处理异常带有@ResponseStatus的异常类，并且返回对应code的视图。而rest在发生错误的时候，友好的形式是返回一个json视图，并且说明错误的信息，这样更加有利于在碰到异常的情况下进行错误的定位，提高解决bug的效率。

我们采用ResponseStatusAndBodyExceptionResolver，是对ResponseStatusExceptionResolver做了进一步处理，并作用在ResponseStatusExceptionResolver之前。ResponseStatusAndBodyExceptionResolver是针对加了@ResponseBody或者控制器加了@RestController的处理程序遇到异常的异常解析器，获得异常结果并且返回json(RestResponse)视图