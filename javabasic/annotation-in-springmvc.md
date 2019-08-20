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

