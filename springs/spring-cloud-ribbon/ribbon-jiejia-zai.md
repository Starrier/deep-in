# Ribbon 饥饿加载

Ribbon 在进行客户端负载均衡时，并不是在启动时就加载上下文，而是在实际请求时才去创建，因此这个特性往往会让第一调用出问题，严重时，会导致调用超时。我们可以指定 Ribbon 具体的客户端名称来开启饥饿加载，即在启动时就加载所有配置项的应用程序上下文。

```java
ribbon:
   eager-load:
       enabled: true
       clients: client-a,client-b,client-c
```

