---
title: mysql-start
date: 2019-02-18 02:58:28
tags: mysql
index_img: ../post_img/mysql-start.jpeg
---

## MySQL

### select

我们知道从 MySQL 表中使用 SQL SELECT 语句来读取数据。

如需有条件地从表中选取数据，可将 WHERE 子句添加到 SELECT 语句中。

语法：

```sql
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
[WHERE condition1 [AND [OR]] condition2.....
```

查询语句中你可以使用一个或者多个表，表之间使用逗号, 分割，并使用 WHERE 语句来设定查询条件。

 1. 你可以在 WHERE 子句中指定任何条件。
 2. 你可以使用 AND 或者 OR 指定一个或多个条件。
 3. WHERE 子句也可以运用于 SQL 的 DELETE 或者 UPDATE 命令。
 4. WHERE 子句类似于程序语言中的 if 条件，根据 MySQL 表中的字段值来读取指定的数据

以下为操作符列表，可用于 WHERE 子句中。下表中实例假定 A 为 10, B 为 20

|操作符	|描述	|实例|
|---|---|---|
|=	|等号，检测两个值是否相等，如果相等返回true	|(A = B) 返回false|
|<>| !=	不等于，检测两个值是否相等，如果不相等返回true|	(A != B) 返回 true|
|>|	大于号，检测左边的值是否大于右边的值, 如果左边的值大于右边的值返回true|	(A > B) 返回false|
|<|	小于号，检测左边的值是否小于右边的值, 如果左边的值小于右边的值返回true|	(A < B) 返回 true|
|>=|	大于等于号，检测左边的值是否大于或等于右边的值, 如果左边的值大于或等于右边的值返回true|(A >= B) 返回false|
|<=|	小于等于号，检测左边的值是否小于于或等于右边的值, 如果左边的值小于或等于右边的值返回true|	(A <= B) 返回 true|

### Update

语法：

```sql
UPDATE table_name SET field1=new-value1, field2=new-value2
[WHERE Clause]
```

 1. 你可以同时更新一个或多个字段。
 2. 你可以在 WHERE 子句中指定任何条件。
 3. 你可以在一个单独表中同时更新数据。

当我们需要将字段中的特定字符串批量修改为其他字符串时，可已使用以下操作：

``` sql
UPDATE table_name SET field=REPLACE(field, 'old-string', 'new-string') 
[WHERE Clause]
```

实例：

以下实例将更新 runoob_id 为 3 的runoob_title 字段值的 "C++" 替换为 "Python"：

```sql
UPDATE runoob_tbl SET runoob_title = REPLACE(runoob_title, 'C++', 'Python') where 
runoob_id = 3;
```

### Delete

语法：

```sql
DELETE FROM table_name [WHERE Clause]
```

 1. 如果没有指定 WHERE 子句，MySQL 表中的所有记录将被删除。
 2. 你可以在 WHERE 子句中指定任何条件
 3. 您可以在单个表中一次性删除记录。
 4. 当你想删除数据表中指定的记录时 WHERE 子句是非常有用的

delete，drop，truncate 都有删除表的作用，区别在于：

 1. delete 和 truncate 仅仅删除表数据，drop 连表数据和表结构一起删除，打个比方，delete 是单杀，truncate 是团灭，drop 是把电脑摔了。
 2. delete 是 DML 语句，操作完以后如果没有不想提交事务还可以回滚，truncate 和 drop 是 DDL 语句，操作完马上生效，不能回滚，打个比方，delete 是发微信说分手，后悔还可以撤回，truncate 和 drop 是直接扇耳光说滚，不能反悔。
 3. 执行的速度上，drop>truncate>delete，打个比方，drop 是神舟火箭，truncate 是和谐号动车，delete 是自行车。

 ### Alter

当我们需要修改数据表名或者修改数据表字段时，就需要使用到 MySQL ALTER 命令。

```sql
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| i     | int(11) | YES  |     | NULL    |       |
| c     | char(1) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
```

删除，添加或修改表字段.如下命令使用了 ALTER 命令及 DROP 子句来删除以上创建表的 i 字段：

```sql
mysql> ALTER TABLE testalter_tbl  DROP i;
```

如果数据表中只剩余一个字段则无法使用 DROP 来删除字段。

MySQL 中使用 ADD 子句来向数据表中添加列，如下实例在表 testalter_tbl 中添加 i 字段，并定义数据类型:

mysql> ALTER TABLE testalter_tbl ADD i INT;
执行以上命令后，i 字段会自动添加到数据表字段的末尾。

```sql
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c     | char(1) | YES  |     | NULL    |       |
| i     | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
```

如果你需要指定新增字段的位置，可以使用MySQL提供的关键字 FIRST (设定位第一列)， AFTER 字段名（设定位于某个字段之后）。

尝试以下 ALTER TABLE 语句, 在执行成功后，使用 SHOW COLUMNS 查看表结构的变化：

```sql
ALTER TABLE testalter_tbl DROP i;
ALTER TABLE testalter_tbl ADD i INT FIRST;
ALTER TABLE testalter_tbl DROP i;
ALTER TABLE testalter_tbl ADD i INT AFTER c;
```

FIRST 和 AFTER 关键字可用于 ADD 与 MODIFY 子句，所以如果你想重置数据表字段的位置就需要先使用 DROP 删除字段然后使用 ADD 来添加字段并设置位置。

修改字段类型及名称
如果需要修改字段类型及名称, 你可以在ALTER命令中使用 MODIFY 或 CHANGE 子句 。

例如，把字段 c 的类型从 CHAR(1) 改为 CHAR(10)，可以执行以下命令:

```sql
mysql> ALTER TABLE testalter_tbl MODIFY c CHAR(10);
```

使用 CHANGE 子句, 语法有很大的不同。 在 CHANGE 关键字之后，紧跟着的是你要修改的字段名，然后指定新字段名及类型。尝试如下实例：

```sql
mysql> ALTER TABLE testalter_tbl CHANGE i j BIGINT;
mysql> ALTER TABLE testalter_tbl CHANGE j j INT;
```

ALTER TABLE 对 Null 值和默认值的影响
当你修改字段时，你可以指定是否包含值或者是否设置默认值。

以下实例，指定字段 j 为 NOT NULL 且默认值为100 。

```sql
mysql> ALTER TABLE testalter_tbl 
    -> MODIFY j BIGINT NOT NULL DEFAULT 100;
```

如果你不设置默认值，MySQL会自动设置该字段默认为 NULL。

修改字段默认值
你可以使用 ALTER 来修改字段的默认值，尝试以下实例：

```sql
mysql> ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c     | char(1) | YES  |     | NULL    |       |
| i     | int(11) | YES  |     | 1000    |       |
+-------+---------+------+-----+---------+-------+
```

你也可以使用 ALTER 命令及 DROP子句来删除字段的默认值，如下实例：

```sql
mysql> ALTER TABLE testalter_tbl ALTER i DROP DEFAULT;
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c     | char(1) | YES  |     | NULL    |       |
| i     | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
```

Changing a Table Type:
修改数据表类型，可以使用 ALTER 命令及 TYPE 子句来完成。尝试以下实例，我们将表 testalter_tbl 的类型修改为 MYISAM ：

注意：查看数据表类型可以使用 SHOW TABLE STATUS 语句。

```sql
mysql> ALTER TABLE testalter_tbl ENGINE = MYISAM;
mysql>  SHOW TABLE STATUS LIKE 'testalter_tbl'\G
*************************** 1. row ****************
           Name: testalter_tbl
           Type: MyISAM
     Row_format: Fixed
           Rows: 0
 Avg_row_length: 0
    Data_length: 0
Max_data_length: 25769803775
   Index_length: 1024
      Data_free: 0
 Auto_increment: NULL
    Create_time: 2007-06-03 08:04:36
    Update_time: 2007-06-03 08:04:36
     Check_time: NULL
 Create_options:
        Comment:
```

修改表名
如果需要修改数据表的名称，可以在 ALTER TABLE 语句中使用 RENAME 子句来实现。

尝试以下实例将数据表 testalter_tbl 重命名为 alter_tbl：

mysql> ALTER TABLE testalter_tbl RENAME TO alter_tbl;
ALTER 命令还可以用来创建及删除MySQL数据表的索引，该功能我们会在接下来的章节中介绍。