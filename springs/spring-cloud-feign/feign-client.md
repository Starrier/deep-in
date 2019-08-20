# Feign Client

Feign Client 

默认使用 JDK 原生 URLConnection 发送 Http 请求。

### Apache Client

### OKHttp

#### 功能以及特性

1. 支持 SPDY，可以合并多个到同一个主机的请求。
2. 使用连接池技术减少请求的延迟（如果 SPDY 是可用的话）。
3. 使用 GZIP 压缩减少传输的数据量
4. 缓存响应避免重复的网络请求。

