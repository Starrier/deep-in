# @FeignClients

### name 

指定 FeignClient 的名称，如果项目使用了 Ribbon，那么属性会作为微服务的名称，用于服务发现。

### url

url 一般用于调试，可以手动指定 @FeignClient 的调用地址

### decode404

当发生 404 错误时，如果该字段为 true，会调用 decode 进行解码，否则抛出 FeignException

### configuration

Feign 配置类，可以自定义 Feign 的 Encoder 、Decoder、LogLevel、Contract。

### fallback

定义容错的处理类，当调用远程接口失败或超时，会调用对应接口的容错逻辑，fallback 指定的类必须实现 @FeignClient 标记的接口。

### fallbackFactory

工厂类，用于生成 fallback 类示例，通过这个属性我们可以实现每个接口通用的容错逻辑，减少重复代码。

### path 

定义当前 Feign Client 的统一前缀。 

