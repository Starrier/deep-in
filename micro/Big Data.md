Big Data Tec.

HDFS:

是 Hadoop 应用程序中主要的分布式存储系统，HDFS 集群包含了一个 NameNode（主节点），这个节点负责管理所有文件系统的元数据及存储了真实数据的 DataNode（数据节点可以有很多）。HDFS 针对海量数据所设计，所以相比传统文件系统在 大批量上的优化，HDFS 优化的则是对小批量大型文件的访问和存储。

MapReduce

是一个软件框架，用以轻松编写处理海量（TB）数据的并行应用程序，以可靠和容错的方式连接大型集群中上万个节点。

Hive

Apache Hive 是 Hadoop 的一个数据仓库系统，促进了数据的综述（将结构化的数据文件映射为一张数据库表），即席查询以及存储在 Hadoop 兼容系统中的大型数据集分析。Hive 提供完整的 SQL 查询功能 —— HiveQL 语言，同时当使用这个语言表达一个逻辑变得低效和繁琐时，HiveQL 还允许传统的 MapReduce 程序员私用自己定制的 Mapper 和 Reducer。Hive 类似 CloudBase，基于 Hadoop 分布式计算平台上的提供的 data warehouse 的 sql 功能的一套软件。使得存储在 Hadoop 里面的海量数据的汇总，即席查询简单化。

Pig

Apache Pig 是一个用于大型数据集分析的平台，它包含了一个用于数据分析应用的高级程序以及评估这些应用的基础设施。Pig 应用的

Cloudera 系列：

1. Cloudera Manager

 - 管理
 - 监控 
 - 诊断 
 - 集成

2. 