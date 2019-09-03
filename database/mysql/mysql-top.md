# Select TOP 

SQL SELECT TOP 子句

SELECT TOP 子句用于规定要返回的记录的数目。

MySQL：

```sql
SELECT column_name(s)
FROM table_name
LIMIT number;
```


查询结果：存入虚拟表vt4，为筛选的条件为true的结果集，这里加入一个记忆点，就是，where的筛选删除为永久的，而on的筛选删除为暂时的，因为on筛选过后，有可能会经过outer添加外部行，重新把数据加载回来，而where则不能。 4096 4096     5192