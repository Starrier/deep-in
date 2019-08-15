# 限流

## Nginx

对于 Nginx 接入层限流，可以使用 Nginx 自带的两个模块：

 1. `ngx_http_limit_conn_module` 连接数限流模块
 2. `ngx_http_limit_req_module` 漏桶算法实现的请求限流模块

###  `ngx_http_limit_conn_module`

该模块可以根据定义的键来限制每个键值的连接数，如同一个 IP 来源链接数。并不是所有的链接都会被该模块技术，只有那些正在被处理的请求（这些请求的头信息已被完全读入）所在的链接才会被计数。

在 nginx_conf 的 http{} 中加入以下配置

``` conf
# 限制每个用户的并发连接数，取名 one
limit_conn_zone $binary_remote_addr zone=one:10m;
# 配置记录被限流后的日志级别，默认为 error 级别
limit_conn_log_level error;
# 配置被限流后返回的状态码，默认返回 503
limit_conn_status 503;
```

然后在 server{} 加入

``` conf
# 限制用户并发连接数 1
limit_conn one 1
```

刚才是配置针对单个 IP 的并发限制，也可以针对域名进行并发限制，配置和客户端 IP 类似

```conf
# http{} 配置
limit_conn zone $server_name=perserver:10m;
# server{} 配置
limit_conn perserver 1;
```

###  `ngx_http_limit_req_module`


# （计数器）在容器中配置最大请求数，如果大于该请求，则客户端阻塞。该方法有效阻止了大量的请求访问业务系统，但对于用户不友好

# 使用过滤器，保证一定数量的请求能正常访问系统，多余的请求先跳转到排队页面，由排队页面定时发起。过滤器实现：

```java

```

