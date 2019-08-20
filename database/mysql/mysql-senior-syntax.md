---
title: mysql-senior-syntax
date: 2019-02-18 02:33:22
tags: mysql
index_img: ../post_img/mysql-senior-syntax.jpeg
---

## MySQL 高级用法

#### select 顺序

|子句|说明|是否必须使用|
|---|---|---|
|select|要返回的列或表示式|是|
|form|从中检索数据的表|仅在从表选择数据时使用|
|where|行级过滤|否|
|group by|分组说明|仅在按组计算聚集时使用|
|having|组级过滤|否|
|order by|输出排序顺序|否|
|limit|要检索的行数|否|

### 笛卡尔积

 1. 先确定数据要使用到的表
 2. 将多个表通过笛卡尔积变成一个表
 3. 然后取出不符合逻辑的数据（根据两个表的关系去掉）
 4. 最后当做是一个虚拟表一样来加上条件即可

### MySQL 注入

如果您通过网页获取用户输入的数据并将其插入一个 MySQL 数据库，那么就有可能发生 SQL 注入安全的问题。

所谓 SQL 注入，就是通过把 SQL 命令插入到 Web 表单递交或输入域名或页面请求的查询字符串，最终达到欺骗服务器执行恶意的 SQL 命令。

我们永远不要信任用户的输入，我们必须认定用户输入的数据都是不安全的，我们都需要对用户输入的数据进行过滤处理。

##### 防止 SQL 注入：

 1. 永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和 双"-"进行转换等。
 2. 永远不要使用动态拼装 sql，可以使用参数化的 sql 或者直接使用存储过程进行数据查询存取。
 3. 永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接。
 4. 不要把机密信息直接存放，加密或者 hash 掉密码和敏感的信息。
 5. 应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装
 6. sql 注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用 sql 注入检测工具 jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN等。采用 MDCSOFT-IPS 可以有效的防御 SQL 注入，XSS 攻击等。

##### like 语句中的注入

like查询时，如果用户输入的值有`"_"`和 `"%"`，则会出现这种情况：用户本来只是想查询 `"abcd_"`，查询结果中却有 `"abcd_"`、`"abcde"`、`"abcdf"`等等；用户要查询 `"30%"`（注：百分之三十）时也会出现问题。

### UNION 操作符

MySQL UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中。多个 SELECT 语句会删除重复的数据。

语法：

```sql
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions];
```

参数 ：

 1. expression1, expression2, ... expression_n: 要检索的列。
 2. tables: 要检索的数据表。
 3. WHERE conditions: 可选， 检索条件。
 4. DISTINCT: 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。
 5. ALL: 可选，返回所有结果集，包含重复数据。

 ### like 操作

我们知道在 MySQL 中使用 SQL SELECT 命令来读取数据， 同时我们可以在 SELECT 语句中使用 WHERE 子句来获取指定的记录。

WHERE 子句中可以使用等号 = 来设定获取数据的条件，如 "runoob_author = 'RUNOOB.COM'"。

但是有时候我们需要获取 runoob_author 字段含有 "COM" 字符的所有记录，这时我们就需要在 WHERE 子句中使用 SQL LIKE 子句。

SQL LIKE 子句中使用百分号 %字符来表示任意字符，类似于UNIX或正则表达式中的星号 *。

如果没有使用百分号 %, LIKE 子句与等号 = 的效果是一样的。

语法：

```sql
SELECT field1, field2,...fieldN 
FROM table_name
WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'
```

 1. 你可以在 WHERE 子句中指定任何条件。
 2. 你可以在 WHERE 子句中使用LIKE子句。
 3. 你可以使用 LIKE子 句代替等号 =。
 4. LIKE 通常与 % 一同使用，类似于一个元字符的搜索。
 5. 你可以使用 AND 或者 OR 指定一个或多个条件。
 6. 你可以在 DELETE 或 UPDATE 命令中使用
 7. WHERE...LIKE 子句来指定条件。

```sql
mysql> SELECT * from runoob_tbl  WHERE runoob_author LIKE '%COM';
+-----------+---------------+---------------+-----------------+
| runoob_id | runoob_title  | runoob_author | submission_date |
+-----------+---------------+---------------+-----------------+
| 3         | 学习 Java   | RUNOOB.COM    | 2015-05-01      |
| 4         | 学习 Python | RUNOOB.COM    | 2016-03-06      |
+-----------+---------------+---------------+-----------------+
```

### 排序

如果我们需要对读取的数据进行排序，我们就可以使用 MySQL 的 ORDER BY 子句来设定你想按哪个字段哪种方式来进行排序，再返回搜索结果。

```sql
SELECT field1, field2,...fieldN table_name1, table_name2...
ORDER BY field1, [field2...] [ASC [DESC]]
```

 1. 你可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。
 2. 你可以设定多个字段来排序。
 3. 你可以使用 ASC 或 DESC 关键字来设置查询结果是按升序或降序排列。 默认情况下，它是按升序排列。
 4. 你可以添加 WHERE...LIKE 子句来设置条件。

```sql
mysql> SELECT * from runoob_tbl ORDER BY submission_date ASC;
+-----------+---------------+---------------+-----------------+
| runoob_id | runoob_title  | runoob_author | submission_date |
+-----------+---------------+---------------+-----------------+
| 3         | 学习 Java   | RUNOOB.COM    | 2015-05-01      |
| 4         | 学习 Python | RUNOOB.COM    | 2016-03-06      |
| 1         | 学习 PHP    | 菜鸟教程  | 2017-04-12      |
| 2         | 学习 MySQL  | 菜鸟教程  | 2017-04-12      |
+-----------+---------------+---------------+-----------------+
```

### 分组

GROUP BY 语句根据一个或多个列对结果集进行分组。

在分组的列上我们可以使用 COUNT, SUM, AVG,等函数

语法：

```sql
SELECT column_name, function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;
```

WITH ROLLUP 可以实现在分组统计数据基础上再进行相同的统计（SUM,AVG,COUNT…）。