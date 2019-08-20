# Ribbon 脱离 Eureka 使用

如果 Eureka 是一个供多人使用的公共注册中心，此时极其容易产生服务入侵问题，所以就不要从 Eureka 中获取注册列表，而应该在 Ribbon 客户端自行制定源服务地址，让 Ribbon 脱离 Eureka 使用：

```java
ribbon:
    eureka: 
       enabled: false
client:
    ribbon:
        listOfServers: http://localhost:8080,http://localhost:8081
```

