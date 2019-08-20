# Ribbon 负载均衡策略

| 策略类 | 命名 | 描述 |
| :--- | :--- | :--- |
| RandomRule	 | 随机策略 | 随机选择 server |
| RoundRobinRule | 轮询策略 | 按顺序循环选择 server |
| RetryRule |  重试策略 | 在一个配置时间段内但选择 server 不成功，则一直尝试选择一个可用的server |
| BestAvailableRule | 最低并发策略 | 逐个考察 server，如果 server 断路器打开，则忽略，在选择其中并发链接最低的那个 |
| AvailableFilteringRule	 | 可用过滤策略 | 过滤掉一直连接失败把那个被标记为 circuit tripped 的 server，过滤掉那些高并发连接的 server（active connections）超时配置的阀值 |
| ResponseTimeWeightRule | 响应时间加权策略 | 根据 server 的响应时间分配权重。响应时间越长，权重越低，被选择到的概率越低；时间越短，权重越高，被选择到的概率就越高。 |
| ZoneAvoidanceRule | 区域权衡策略 | 综合判断 server 所在区域的性能和 server 的可用性轮询选择 server，并且判定一个 AWS Zone 的运行性能是否可用，提出不可用的 Zone 中所有 server |

