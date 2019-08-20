# Ribbon 的超时与重试

#### F 版中 默认开启重试机制

```java
client-a:
    ribbon:
        ConnectTimeout: 3000
        ReadTimeout: 30000
        MaxAutoRetries: 1
        MaxAutoRetriesNextServer: 1
        OkToRetryOnAllOperations: true
```

