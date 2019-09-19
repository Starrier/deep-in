# RocketMQ 如何支持分布式事务消息

A(存在 DB 操作)，B(存在 DB 操作) 需要保证分布式事务一致性，通过引入中间层 MQ，A 和 MQ 保证事务一致性（异常情况下，通过 MQ 反查 A 接口实现 check） B 和 MQ 保证事务一致性（通过重试），从而达到最终的事务一致性。

原理：大事务 = 小事务 + 异步

MQ 消息，DB 操作一致性方案：

1. 发送消息到 MQ 服务器，此时消息为 SEND_OK。此消息为 consumer 不可见。
2. 执行 DB 操作，DB 执行成功 commit db 操作，DB 失败，则 Rollback DB。
3. 如果 DB 执行成功，回复 MQ 服务器，将状态为 COMMIT_MESSAGE；如果 DB 执行失败，回复 MQ 服务器，将状态改为 ROLLBACK_MESSAGE。（**此过程可能失败**）。
4. MQ 内部提供一个名为“事务状态服务”的服务，此服务会检查事务消息的状态，如果发现消息未 commit，则通过 Produce 启动时注册的 Transcation CheckListener 来回调业务系统，业务系统在 checkLocalTransactionState 方法中检查 DB 事务状态，如果成功，则回复 COMMIT_MESSAGE，否则回复 ROLLBACK_MESSAGE。

**Note**

以上 SEND_OK，COMMIt_MESSAGE,ROLLBACK_MESSAGE 均是 client jar 提供的状态，在 MQ 服务器内部是一个数字。

TransactionCheckListener 是在消息 commit 或 rollback 消息丢失的情况下才会回到。这种消息丢失只存在于断网或 rocketmq 集群挂了的情况下，当 rocketmq 集群挂了，如果采用异步刷盘，存在 1s 内数据丢失的风险，异步刷盘场景下保障事务没有意义。所以如果要核心业务用 RocketMQ 解决分布式事务问题，建议使用 同步刷盘模式。
