# Hystrix 异常机制及其处理

Hystrix 的 fallback 触发的 5 中异常

1. FAULURE ： 执行失败，抛出异常
2. TIMEOUT： 执行超时
3. SHORT\_CIRCUITED 断路器打开
4. THREAD\_POOL\_REJECTED 线程池拒绝
5. SEMAPHORE\_REJECTED 信号量拒绝

BAD\_REQUEST 异常是会触发 fallback 的，也不会被计数入熔断，而是会抛出异常 HystrixBadRequestException，这种异常一般是由于非法参数或者一些非系统异常引起的。可以对这类异常进行自定义异常封装处理。

