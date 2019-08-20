# ACID

## ACID

### 原子性

 一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

### 一致性

 在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。

### 隔离性

 数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。

### 持久性

 事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

{% hint style="warning" %}
 在 MySQL 命令行的默认设置下，事务都是自动提交的，即执行 SQL 语句后就会马上执行 COMMIT 操作。因此要显式地开启一个事务务须使用命令 BEGIN 或 START TRANSACTION，或者执行命令 SET AUTOCOMMIT=0，用来禁止使用当前会话的自动提交。
{% endhint %}

## 事务控制语句

####  BEGIN 或 START TRANSACTION

 显式地开启一个事务；

####  COMMIT 或 COMMIT WORK

 COMMIT会提交事务，并使已对数据库进行的所有修改成为永久性的

####  ROLLBACK 或 ROLLBACK WORK

 回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；

####  SAVEPOINT identifier

 AVEPOINT允许在事务中创建一个保存点，一个事务中可以有多个SAVEPOINT

####  RELEASE SAVEPOINT identifier

 删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常

####  ROLLBACK TO identifier

 把事务回滚到标记点；

####  SET TRANSACTION

 用来设置事务的隔离级别。InnoDB存储引擎提供事务的隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ和SERIALIZABLE

## MySQL 事务处理的方法

####  1、用 BEGIN, ROLLBACK, COMMIT来实现

* BEGIN 开始一个事务
* ROLLBACK 事务回滚
* COMMIT 事务确认

####  2、直接用 SET 来改变 MySQL 的自动提交模式:

* SET AUTOCOMMIT =0 禁止自动提交
* SET AUTOCOMMIT =1 开启自动提交

autocommit

默认情况下，autocommit 的状态是 on，这意味着所有的单独的语句一旦被执行就会被提交，除非该语句在 BEGIN ...  COMMIT 块中。如果 autocommit 的状态是 OFF，则需要明确发出 COMMIT 语句来提交事务。要禁用 autocommit，请使用 

```sql
mysql> SET autocommit = 0
```

DDL 语句，如数据库的 CREATE 或 DROP 语句，以及表或存储例程的 CREATE DROP 或 ALTER 语句，都是无法回滚的。

{% hint style="info" %}
DDL 语句，LOAD DATA INFILE 、 ANALYZE TABLE 以及与 replication 相关的语句等，会导致隐式 COMMIT。
{% endhint %}

