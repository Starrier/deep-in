# Feign 首次请求失败

### 问题

当 Feign 和 Ribbon 整合了 Hystrix 之后，可能会出现首次调用失败的问题。

### 原因

Hystrix 默认的超时时间是 1 秒，如果超过这个时间尚未作出反映，将会进入 fallback 代码，由于 Bean的装配以及懒加载机制，Feign 首次请求都会比较慢。如果这个响应时间大于 1 秒，就会出现请求失败的问题。

### 解决方案

#### 1. 将 Hystrix 的超时时间改为 5 秒：

hystrix.command,default.execution.isolation.thread.timeoutInMilliseconds:5000

#### 2. 禁用 Hystrix 的超时时间，配置如下

hystrix.command.default.execution.timeout.enable:false

#### 3.使用 Feign 的时候直接关闭 Hystrix（不推荐）

feign.hystrix.enable:false

