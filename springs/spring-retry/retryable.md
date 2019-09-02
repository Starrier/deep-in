# @Retryable

#### value

指定发生的异常进行重试 

#### include

和 value 一样，默认空，当 exclude 也为空时，所有异常都重试

#### exclude

指定异常不重试，默认空，当 include 也为空，所有异常都重试

#### maxAttemps

重试次数，默认为 3

## @Recover 

当重试到达指定次数时，被注解的方法将被回调，可以在该方法中进行日志处理。需要注意的是，发生的异常和入参类型一致时才会回调。

## @Backoff

delay 指定延迟后重试
multiplier 指定延迟的倍数，比如 delay = 5000l，multiplier = 2时，第一次重试为 5 秒，大二次是 10 秒，第三次为 20 秒。

#### backoff

代表了延迟，默认是没有延迟的，就是失败后立即重试，也可以加上延迟时间 delay = 3000L，默认延迟 1000ms

`@Retryable(value={Exception.class},maxAtemps=3,backoff=@Backoff(delay=1000L`))

# RetryTemplate

RetryTemplate 是 RetryOperations 的实现类，实现了重试和熔断，RetryOperations 的 API 

```java
public interface RetryOperations {

    /**
     * Execute the supplied {@link RetryCallback} with the configured retry
     * semantics. See implementations for configuration details.
     * 
     * @return the value returned by the {@link RetryCallback} upon successful
     * invocation.
     * @throws E any {@link Exception} raised by the
     * {@link RetryCallback} upon unsuccessful retry.
     * @throws E the exception thrown
     * @param <T> the return value
     * @param retryCallback the {@link RetryCallback}
     * @param <E> the exception to throw
     */
    <T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback) throws E;

    /**
     * Execute the supplied {@link RetryCallback} with a fallback on exhausted
     * retry to the {@link RecoveryCallback}. See implementations for
     * configuration details.
     * 
     * @return the value returned by the {@link RetryCallback} upon successful
     * invocation, and that returned by the {@link RecoveryCallback} otherwise.
     * @throws E any {@link Exception} raised by the
     * @param <T> the type to return
     * @param <E> the type of the exception
     * @param recoveryCallback the {@link RecoveryCallback}
     * @param retryCallback the {@link RetryCallback}
     * {@link RecoveryCallback} upon unsuccessful retry.
     */
    <T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback, RecoveryCallback<T> recoveryCallback) throws E;

    /**
     * A simple stateful retry. Execute the supplied {@link RetryCallback} with
     * a target object for the attempt identified by the {@link DefaultRetryState}.
     * Exceptions thrown by the callback are always propagated immediately so
     * the state is required to be able to identify the previous attempt, if
     * there is one - hence the state is required. Normal patterns would see
     * this method being used inside a transaction, where the callback might
     * invalidate the transaction if it fails.
     * 
     * See implementations for configuration details.
     *
     * @param retryCallback the {@link RetryCallback}
     * @param retryState the {@link RetryState}
     * @return the value returned by the {@link RetryCallback} upon successful
     * invocation, and that returned by the {@link RecoveryCallback} otherwise.
     * @throws E any {@link Exception} raised by the {@link RecoveryCallback}.
     * @throws ExhaustedRetryException if the last attempt for this state has
     * already been reached
     * @param <T> the type of the return value
     * @param <E> the type of the exception to return
     */
    <T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback, RetryState retryState) throws E, ExhaustedRetryException;

    /**
     * A stateful retry with a recovery path. Execute the supplied
     * {@link RetryCallback} with a fallback on exhausted retry to the
     * {@link RecoveryCallback} and a target object for the retry attempt
     * identified by the {@link DefaultRetryState}.
     * @param recoveryCallback the {@link RecoveryCallback}
     * @param retryState the {@link RetryState}
     * @param retryCallback the {@link RetryCallback}
     * @see #execute(RetryCallback, RetryState)
     * @param <T> the return value type
     * @param <E> the exception type
     * @return     the value returned by the {@link RetryCallback} upon successful
     *             invocation, and that returned by the {@link RecoveryCallback} otherwise.
     * @throws E any {@link Exception} raised by the {@link RecoveryCallback} upon unsuccessful retry.
     */
    <T, E extends Throwable> T execute(RetryCallback<T, E> retryCallback, RecoveryCallback<T> recoveryCallback, RetryState retryState)
            throws E;

}
```

全是 execute 方法。每个方法都有一个 RetryCallback 参数。

RetryCallback 定义了重试的回调操作，定义好重试的操作后，就是怎么触发了，重试策略就需要使用 RetryPolicy 了。

```java
public interface RetryPolicy extends Serializable{
    
    // 在每次重试的时候判断，是否需要继续
    boolean canRetry(RetryContext context);

    // 重试开始前调用，保存上下文
    RetryContext open(RetryContext parent);
    
    void close(RetryContext context);
    
    // 重试的时候调用
    void registerThrowable(RetryContext context,Throwable throwable);
}
```

##  重试策略

- AlwaysRetyPolicy 总是尝试，直到成功为止
- CircuitBreakerRetryPolicy 熔断器策略
- CompositeRetryPolicy 组合重试策略
- ExceptionClassifierRetryPolicy 不同异常策略
- NeverRetryPolicy 只允许 callback 一次的策略
- SimpleRetryPolicy 简单重试策略，默认重试最大次数为 3（默认策略）
- TimeoutRetryPolicy 重试重试蹙额了，默认超时时间为 1


## 重试回退策略

1. ExponentialBackOffPolicy：指数回退策略，线程安全的，适合并发操作。需要设置Sleeper。其中InitialInterval（设置初始睡眠间隔值。默认值是100毫秒。不能设置为小于1的值）、MaxInterval（最大回退周期的Setter。默认是30000(30秒)。如果调用此方法的值小于1，则重置为1。设置这个值以避免无限等待如果后退了大量的次数(或者避免如果乘数被设置太高）、Multiplier（设置乘数值默认是2.0，如果小于等于1.0，则是1.0）.如果不设置则使用默认值。

2. NoBackOffPolicy：没有回退策略，每次立即执行
3. FixedBackOffPolicy：固定时间回退策略，默认回退1000ms。
4. UniformRandomBackOffPolicy：随机回退策略，默认最小时间是500ms，最大时间是1500ms。
5. ExponentialRandomBackOffPolicy：随机指数退避策略

有状态重试 OR 无状态  重试

所谓无状态重试是指重试在一个线程上下文中完成的重试，反之不在一个线程上下文完成重试的就是有状态重试。之前的SimpleRetryPolicy就属于无状态重试，因为重试是在一个循环中完成的。那么什么会后会出现或者说需要有状态重试呢？通常有两种情况：事务回滚和熔断。

**数据库操作异常 DataAccessException，不能执行重试，而如果抛出其他异常可以重试**。

熔断的意思不在当前循环中处理重试，而是全局重试模式（不是线程上下文）。熔断会跳出循环，那么必然会丢失线程上下文的堆栈信息。那么肯定需要一种“全局模式”保存这种信息，目前的实现放在一个cache（map实现的）中，下次从缓存中获取就能继续重试了。