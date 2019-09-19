# MySQL

 concat

返回结果为连接参数产生的字符串。如有任何一个参数为NULL ，则返回值为 NULL。MySQL的concat函数在连接字符串的时候，只要其中一个是NULL,那么将返回NULL

contcat_ws(separator,str1,str2,...)

contcat_ws() 代表 CONCAT With Separator ，是CONCAT()的特殊形式。第一个参数是其它参数的分隔符。分隔符的位置放在要连接的两个字符串之间。分隔符可以是一个字符串，也可以是其它参数。

**注意**：

如果分隔符为 NULL，则结果为 NULL。函数会忽略任何分隔符参数后的 NULL 值。和MySQL中concat函数不同的是, concat_ws函数在执行的时候,不会因为NULL值而返回NULL

repeat()函数

 

用来复制字符串,如下'ab'表示要复制的字符串，2表示复制的份数

```sql
mysql> select repeat('ab',2);
+----------------+
| repeat('ab',2) |
+----------------+
| abab           |
+----------------+
```

group_concat函数


完整的语法如下：
group_concat([DISTINCT] 要连接的字段 [Order BY ASC/DESC 排序字段] [Separator '分隔符'])

基本查询

```sql
mysql> select * from aa;
+------+------+
| id| name |
+------+------+
|1 | 10|
|1 | 20|
|1 | 20|
|2 | 20|
|3 | 200   |
|3 | 500   |
+------+------+
6 rows in set (0.00 sec)
```

以id分组，把name字段的值打印在一行，逗号分隔(默认)

```sql
mysql> select id,group_concat(name) from aa group by id;
+------+--------------------+
| id| group_concat(name) |
+------+--------------------+
|1 | 10,20,20|
|2 | 20 |
|3 | 200,500|
+------+--------------------+
3 rows in set (0.00 sec)
```

以id分组，把name字段的值打印在一行，分号分隔

```sql
mysql> select id,group_concat(name separator ';') from aa group by id;
+------+----------------------------------+
| id| group_concat(name separator ';') |
+------+----------------------------------+
|1 | 10;20;20 |
|2 | 20|
|3 | 200;500   |
+------+----------------------------------+
3 rows in set (0.00 sec)
```

以id分组，把去冗余的name字段的值打印在一行，

逗号分隔

```sql
mysql> select id,group_concat(distinct name) from aa group by id;
+------+-----------------------------+
| id| group_concat(distinct name) |
+------+-----------------------------+
|1 | 10,20|
|2 | 20   |
|3 | 200,500 |
+------+-----------------------------+
3 rows in set (0.00 sec)
```

以id分组，把name字段的值打印在一行，逗号分隔，以name排倒序

```sql
mysql> select id,group_concat(name order by name desc) from aa group by id;
+------+---------------------------------------+
| id| group_concat(name order by name desc) |
+------+---------------------------------------+
|1 | 20,20,10   |
|2 | 20|
|3 | 500,200|
+------+---------------------------------------+
3 rows in set (0.00 sec)
```
