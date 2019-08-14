---
title: spring-transcation
date: 2019-02-18 01:00:51
tags: Spring
index_img: ../post_img/spring-transcation.jpeg
---

## Spring-Transcation

### 事务特性（ACID）

1. **原子性**（atomicity）

一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

 2. **一致性**(consistency)

在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。

 3. **隔离性**(isolation)

数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括：

   * . 读未提交（Read uncommitted）
   * . 读提交（read committed）
   * . 可重复读（repeatable read）
   * . 串行化（Serializable）。

4. **持久性**(durability)

事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失

### 事务隔离级别

更新丢失（Lost Update）：两个事务都企图去更新一行数据，导致事务抛出异常退出，两个事务的更新都白费了。

脏数据（Dirty Read）：如果第二个应用程序使用了第一个应用程序修改过的数据，而这个数据处于未提交状态，这时就会发生脏读。第一个应用程序随后可能会请求回滚被修改的数据，从而导致第二个事务使用的数据被损坏，即所谓的“变脏”。

不可重读（Unrepeatable Read）：一个事务两次读同一行数据，可是这两次读到的数据不一样，就叫不可重读。如果一个事务在提交数据之前，另一个事务可以修改和删除这些数据，就会发生不可重读。

幻读（Phantom Read）：一个事务执行了两次查询，发现第二次查询结果比第一次查询多出了一行，这可能是因为另一个事务在这两次查询之间插入了新行。针对由事务的不完全隔离所引起的上述问题，提出了一些隔离级别，用来防范这些问题。

读操作未提交（Read Uncommitted）：读取未提交的数据是允许的。说明一个事务在提交前，其变化对于其他事务来说是可见的。这样脏读、不可重读和幻读都是允许的。当一个事务已经写入一行数据但未提交，其他事务都不能再写入此行数据；但是，任何事务都可以读任何数据。这个隔离级别使用排写锁实现。

读操作已提交（Read Committed）：读取未提交的数据是不允许的，它使用临时的共读锁和排写锁实现。这种隔离级别不允许脏读，但不可重读和幻读是允许的。

可重读（Repeatable Read）：说明事务保证能够再次读取相同的数据而不会失败。此隔离级别不允许脏读和不可重读，但幻读会出现。

可串行化（Serializable）：提供最严格的事务隔离。这个隔离级别不允许事务并行执行，只允许串行执行。这样，脏读、不可重读或幻读都不可发生。

事务隔离与隔离级别的关系

|隔离级别|脏读（Dirty Read）|不可重读（Unrepeatable read）|幻读（Phantom Read）|
|---|---|---|---|
|读操作未提交（Read Uncommitted）|可能|可能|可能|
|读操作已提交（Read Committed）|不可能|可能|可能|
|可重读（Repeatable Read）|不可能|不可能|可能|
|可串行化（Serializable）|不可能|不可能|不可能|


### 事务的传播

|事务传播行为类型|说明|
|---|---|
|PROPAGATION_REQUIRED|如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。|
|PROPAGATION_SUPPORTS|支持当前事务，如果当前没有事务，就以非事务方式执行|
|PROPAGATION_MANDATORY|使用当前的事务，如果当前没有事务，就抛出异常|
|PROPAGATION_REQUIRES_NEW|新建事务，如果当前存在事务，把当前事务挂起|
|PROPAGATION_NOT_SUPPORTED|以非事务方式执行操作，如果当前存在事务，就把当前事务挂起|
|PROPAGATION_NEVER|以非事务方式执行，如果当前存在事务，则抛出异常|
|PROPAGATION_NESTED|如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与 PROPAGATION_REQUIRED 类似的操作|

**readOnly**:

事务属性中的 `readOnly`标志表示对应的事务应该被最优化为只读事务。这是一个最优化提示 。在一些情况下，一些事务策略能够起到显著的最优化效果，例如在使用 Object/Relational 映射工具 （如：Hibernate或TopLink）时避免dirty checking（试图“刷新”）。

**Timeout**:

在事务属性中还有定义“`timeout`”值的选项，指定事务超时为几秒。在 JTA 中，这将被简单地传递到 J2EE 服务器的事务协调程序，并据此得到相应的解释。

 1. **PROPAGATION_REQUIRED**

加入当前正要执行的事务不在另外一个事务里，那么就起一个新的事务

例如：
 ServiceB.methodB的事务级别定义为PROPAGATION_REQUIRED
ServiceA.methodA已经起了事务，这时调用ServiceB.methodB，ServiceB.methodB就加入ServiceA.methodA的事务内部，就不再起新的事务。ServiceA.methodA没有在事务中，这时调用ServiceB.methodB，

ServiceB.methodB就会为自己分配一个事务。

在ServiceA.methodA或者在ServiceB.methodB内的任何地方出现异常，事务都会被回滚。即使ServiceB.methodB的事务已经被提交，但是ServiceA.methodA在接下来fail要回滚，ServiceB.methodB也要回滚

 2. **PROPAGATION_SUPPORTS**

如果当前在事务中，即以事务的形式运行，如果当前不再一个事务中，那么就以非事务的形式运行

 3. **PROPAGATION_MANDATORY**

必须在一个事务中运行。也就是说，他只能被一个父事务调用。否则，他就要抛出异常

 4. **PROPAGATION_REQUIRES_NEW**

例如：

        ServiceA.methodA的事务级别为PROPAGATION_REQUIRED，ServiceB.methodB的事务级别为PROPAGATION_REQUIRES_NEW，

        当调用ServiceB.methodB的时候，ServiceA.methodA所在的事务就会挂起，ServiceB.methodB会起一个新的事务，等待ServiceB.methodB的事务完成以后，他才继续执行。

        PROPAGATION_REQUIRES_NEW与PROPAGATION_REQUIRED 的事务区别在于事务的回滚程度：

                因为ServiceB.methodB和ServiceA.methodA两个不同的事务。如果ServiceB.methodB已经提交，那么ServiceA.methodA失败回滚，ServiceB.methodB是不会回滚的。如果ServiceB.methodB失败回滚，

                如果他抛出的异常被ServiceA.methodA捕获，ServiceA.methodA事务仍然可能提交。

 5. **PROPAGATION_NOT_SUPPORTED**

当前不支持事务。

例如:

　　ServiceA.methodA的事务级别是PROPAGATION_REQUIRED ，而ServiceB.methodB的事务级别是PROPAGATION_NOT_SUPPORTED ，

　　调用ServiceB.methodB时，ServiceA.methodA的事务挂起，而以非事务的状态运行完，再继续ServiceA.methodA的事务。

 6. **PROPAGATION_NEVER**

不能在事务中运行。

假设ServiceA.methodA的事务级别是PROPAGATION_REQUIRED，  而ServiceB.methodB的事务级别是PROPAGATION_NEVER ，

那么ServiceB.methodB就要抛出异常了。

 7. **PROPAGATION_NESTED**

理解Nested的关键是savepoint。他与PROPAGATION_REQUIRES_NEW的区别是，PROPAGATION_REQUIRES_NEW另起一个事务，将会与他的父事务相互独立，

而Nested的事务和他的父事务是相依的，他的提交是要等和他的父事务一块提交的。也就是说，如果父事务最后回滚，他也要回滚的。

而Nested事务的好处是他有一个savepoint。

### Spring 事务管理

Spring 配置文件中关于事务配置总是由 3 部分组成，分别是：

 1. DataSource
 2. TranscationManager
 3. 代理机制

一般变化的只有代理机制。DataSource 和 TranscationManager 只是会根据数据访问方式有所变化，比如 Hibernate 进行数据访问时，DataSource 实际为 SessionFactory，TranscationManager 实际为 HibernateTranscationManager。

![mysql-index-bTree](../_posts/spring-transcation/spring-config.png)

#### 处理方式：

 1. 每个 Bean 都有一个代理
 2. 所有 Bean 共享一个代理
 3. 使用拦截器
 4. 使用 tx 标签配置的拦截器
 5. 全注解（此时，需要在 DAO 上加上 @Transcation 注解）

### Spring 事务处理原理

![mysql-index-bTree](../_posts/spring-transcation/spring-transcation-work.png)

### Spring TranscationDefinition 接口中的五个隔离级别

 1. **ISOLATION_DEFAULT**

这是一个 PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别.另外四个与JDBC的隔离级别相对应；

 2. **ISOLATION_READ_UNCOMMITTED**

这是事务最低的隔离级别，它充许别外一个事务可以看到这个事务未提交的数据。这种隔离级别会产生脏读，不可重复读和幻像读。

 3. **ISOLATION_READ_COMMITTED**

保证一个事务修改的数据提交后才能被另外一个事务读取。另外一个事务不能读取该事务未提交的数据。这种事务隔离级别可以避免脏读出现，但是可能会出现不可重复读和幻像读。

 4. **ISOLATION_REPEATABLE_READ**
 
这种事务隔离级别可以防止脏读，不可重复读。但是可能出现幻像读。它除了保证一个事务不能读取另一个事务未提交的数据外，还保证了避免下面的情况产生(不可重复读)。

 5. **ISOLATION_SERIALIZABLE**

这是花费最高代价但是最可靠的事务隔离级别。事务被处理为顺序执行。除了防止脏读，不可重复读外，还避免了幻像读。
