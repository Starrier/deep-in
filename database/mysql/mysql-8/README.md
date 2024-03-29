# MySQL 8

## 性能

 MySQL 8.0 的速度要比 MySQL 5.7 快 2 倍。MySQL 8.0 在以下方面带来了更好的性能：**读/写工作负载、IO 密集型工作负载、以及高竞争（"hot spot"热点竞争问题）工作负载**。

## NoSQL

 MySQL 从 5.7 版本开始提供 NoSQL 存储功能，目前在 8.0 版本中这部分功能也得到了更大的改进。该项功能消除了对独立的 NoSQL 文档数据库的需求，而 MySQL 文档存储也为 schema-less 模式的 JSON 文档提供了多文档事务支持和完整的 ACID 合规性。

![](../../../.gitbook/assets/mysql8nosql.png)

## 窗口函数

 从 MySQL 8.0 开始，新增了一个叫窗口函数的概念，它可以用来实现若干新的查询方式。窗口函数与 SUM\(\)、COUNT\(\) 这种集合函数类似，但它不会将多行查询结果合并为一行，而是将结果放回多行当中。即窗口函数不需要 GROUP BY。

## 隐藏索引

 在 MySQL 8.0 中，索引可以被“隐藏”和“显示”。当对索引进行隐藏时，它不会被查询优化器所使用。我们可以使用这个特性用于性能调试，例如我们先隐藏一个索引，然后观察其对数据库的影响。如果数据库性能有所下降，说明这个索引是有用的，然后将其“恢复显示”即可；如果数据库性能看不出变化，说明这个索引是多余的，可以考虑删掉。

## 降序索引

 MySQL 8.0 为索引提供按降序方式进行排序的支持，在这种索引中的值也会按降序的方式进行排序。

## 通用表表达式

 在复杂的查询中使用嵌入式表时，使用 CTE 使得查询语句更清晰。

## UTF- 8 编码

 从 MySQL 8 开始，使用 utf8mb4 作为 MySQL 的默认字符集。

## JSON

 MySQL 8 大幅改进了对 JSON 的支持，添加了基于路径查询参数从 JSON 字段中抽取数据的 JSON\_EXTRACT\(\) 函数，以及用于将数据分别组合到 JSON 数组和对象中的 JSON\_ARRAYAGG\(\) 和 JSON\_OBJECTAGG\(\) 聚合函数。

## 可靠性

 InnoDB 现在支持表 DDL 的原子性，也就是 InnoDB 表上的 DDL 也可以实现事务完整性，要么失败回滚，要么成功提交，不至于出现 DDL 时部分成功的问题，此外还支持 crash-safe 特性**，**元数据存储在单个事务数据字典中。

## 高可用性

 InnoDB 集群为您的数据库提供集成的原生 HA 解决方案。

## 安全性

 对 OpenSSL 的改进、新的默认身份验证、SQL 角色、密码强度、授权。

