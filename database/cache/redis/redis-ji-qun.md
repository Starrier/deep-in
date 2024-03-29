# Redis - 集群



1. edis3以后，节点之间提供了完整的sharding（分片）、replication（主备感知能力）、failover（故障转移）的特性
2. 配置一致性：每个节点（Node）内部都保存了集群的配置信息，存储在clusterState中，通过引入自增的epoch变量来使得集群配置在各个节点间保持一致
3. sharding数据分片 
   * 将所有数据划分为16384个分片（slot），每个节点会对应一部分slot，每个key都会根据分布算法映射到16384个slot中的一个，分布算法为slotId=crc16\(key\)%16384
   * 当一个client访问的key不在对应节点的slots中，redis会返回给client一个moved命令，告知其正确的路由信息从而重新发起请求。client会根据每次请求来缓存本地的路由缓存信息，以便下次请求直接能够路由到正确的节点
   * 分片迁移：分片迁移的触发和过程控制由外部系统完成，redis只提供迁移过程中需要的原语支持。主要包含两种：一种是节点迁移状态设置，即迁移钱标记源、目标节点；另一种是key迁移的原子化命令
4. failover故障转移 
   * 故障发现：节点间两两通过TCP保持连接，周期性进行PING、PONG交互，若对方的PONG相应超时未收到，则将其置为PFAIL状态，并传播给其他节点
   * 故障确认：当集群中有一半以上的节点对某一个PFAIL状态进行了确认，则将起改为FAIL状态，确认其故障
   * slave选举：当有一个master挂掉了，则其slave重新竞选出一个新的master。主要根据各个slave最后一次同步master信息的时间，越新表示slave的数据越新，竞选的优先级越高，就更有可能选中。竞选成功之后将消息传播给其他节点。
5. 集群不可用的情况： 
   * 集群中任意master挂掉，且当前master没有slave。
   * 集群中超过半数以上master挂掉。

