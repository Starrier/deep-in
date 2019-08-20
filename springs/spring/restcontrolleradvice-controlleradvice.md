---
description: 全局异常处理
---

# @RestControllerAdvice/@ControllerAdvice

### @RestControllerAdvice/@ControllerAdvice

#### 在需要处理的 controller 方法上注解 @ExceptionHandler\(异常类型.class\)，改异常处理方法只在当前的 controller 中起作用

异常的大致三种情况

1. 在进入 Controller 之前，譬如请求一个不存在的地址，404 
2. 在执行 @RequestMapping 时，进入逻辑处理阶段前，如传参类型错误 
3. 以上都正常时，在 Controller 层执行逻辑代码出现异常，如空指针异常等。

@ControllerAdvice 是 controller 的一个辅助类，最常用的就是全局异常处理的切面类。@ControllerAdvice 可以指定扫描范围，他约定了一种可行的返回值，如果直接返回 model 类的话，需要使用 @ResponseBody进行 json 转换 

1. 返回 String，表示跳转到某个 view 
2. 返回 modelAndView 
3. 返回 model + @ResponseBody

同一个异常被局部范围异常处理器和全局异常处理器同时覆盖时，会选择范围较小的局部范围处理器。

同一个异常被小范围的异常类和大范围的异常类同时覆盖，会选择小范围的异常处理器。

  
使用 @ControllerAdvice 处理异常有一定的局限性。只有进入 Controller 层的错误，才会由 @ControllerAdvice 处理。拦截器抛出的错误，以及访问错误地址的情况 @ControllerAdvice 处理不了，由 SpringBoot 默认的异常处理机制处理。

如果对 controller 层进行了 catch 处理，那么 @ControllerAdvice 就不会捕获到 Controller 层的异常\*\*。如果在 controller 层不做异常 catch 处理，那么 serivice 层中抛出的异常（Service 层也不做 catch 处理），那么也是可以在这里捕获到异常的。  
\# \*\*为什么可以捕获到 service 层的异常

{% file src="../../.gitbook/assets/quan-ju-yi-chang-chu-li.md" %}

