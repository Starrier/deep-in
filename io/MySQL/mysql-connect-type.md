---
title: mysql-connect-type
date: 2019-02-18 01:49:37
tags: mysql
index_img: ../post_img/mysql-index.jpeg
---

## MySql 表连接方式

**数据库准备**:

```sql
---A---
create table A(
    PK int(10) not null primary key,
    value varchar(25) null
)
---B---
create table B(
    PK int(10) not null primary key,
    value varchar(25) null
)
```

![mysql-index-bTree](../_posts/mysql-connect-type/A.png)

![mysql-index-bTree](../_posts/mysql-connect-type/B.png)

### 1. 内连接（INNER JOIN）

内连接是一种一一映射的关系，就是两张表都有的才能显示出来,用 VN 图，即两个集合的交集：

![mysql-index-bTree](../_posts/mysql-connect-type/inner-join.png)

实现代码：

```sql
select A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
from table A inner join table B
ON A.PK=B.PK
```

![mysql-index-bTree](../_posts/mysql-connect-type/inner-join-result.png)

### 2. 左连接（left join）

左连接是左边表的所有数据都显示出来，右边表数据只显示共同的那部分，没有对应的部分用空白显示，所谓的左边表，就是指放在 left join 左边的表：

![mysql-index-bTree](../_posts/mysql-connect-type/left-join.png)

实现代码：

```sql
SELECT A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
FROM table A left join table B
ON A.PK = B.PK
```

![mysql-index-bTree](../_posts/mysql-connect-type/left-join-result.png)

### 3. 右连接（right join）

右连接是和左连接相反的，这里的右边也是相对于 right join 来说的，在这个右边的表就是右表：

![mysql-index-bTree](../_posts/mysql-connect-type/right-join.png)

代码实现：

```sql
SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
FROM table_a A
RIGHT JOIN   table_b B
ON A.PK = B.PK;
```

![mysql-index-bTree](../_posts/mysql-connect-type/right-join-result.png)

### 外连接/全连接（outer join）

查询出左右两表的所有数据：

![mysql-index-bTree](../_posts/mysql-connect-type/out-join.png)

代码实现：

```sql
SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
FROM table_a A
FULL  JOIN table_b B
ON A.PK = B.PK;
```

**注意** 以上代码在 MySQL 中是报错的，因为 MySQL 不支持全连接，只能用以下代码实现（左连接+右连接+去重=全连接）

```sql
SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
from table A
left join B
on A.PK=B.PK
UNION
SELECT  A.PK AS A_PK,A.Value AS A_Value,B.PK AS B_PK,B.Value AS B_Value
FROM table_a A
RIGHT JOIN  table_b B
ON A.PK = B.PK;
```

![mysql-index-bTree](../_posts/mysql-connect-type/out-join-result.png)

### 5. 左连接不包括内连接（left join exclude inner join）

只查询左边表有的数据，共有的不查出来：

![mysql-index-bTree](../_posts/mysql-connect-type/left-join-exclude-inner-join.png)

代码实现：

```sql
SELECT A.PK AS A_PK, A.Value AS A_Value,B.Value AS B_Value, B.PK AS B_PK
FROM table A
LEFT JOIN table B
ON A.PK=B.PK
WHERE B.PK IS NULL
```

![mysql-index-bTree](../_posts/mysql-connect-type/left-join-exclude-inner-join-result.png)

### 6. 右连接不包括内连接（right join exclude inner join）

只查询右边表有的数据，共有的也不会查出来：

![mysql-index-bTree](../_posts/mysql-connect-type/right-join-exclude-inner-join.png)

代码实现：

```sql
SELECT A.PK AS A_PK, A.Value AS A_Value, B.PK AS B_PK,
B.Value AS B_Value
FROM Table_A A
RIGHT JOIN Table_B B
ON A.PK = B.PK
WHERE A.PK IS NULL
```

![mysql-index-bTree](../_posts/mysql-connect-type/right-join-exclude-inner-join-result.png)

### 7. 外连接不包括内连接（outer join exclude inner join）

查询左右表各自拥有的书籍部分

![mysql-index-bTree](../_posts/mysql-connect-type/outer-join-exclude-inner-join.png)

代码实现：

```sql
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.PK = B.PK
WHERE A.PK IS NULL
OR B.PK IS NULL
```

**注意** mysql 不支持 full join，所以可用以下方式：

```sql
SELECT  A.PK AS A_PK, A.Value AS A_Value, B.PK AS B_PK,
B.Value AS B_Value
FROM table_a A
LEFT JOIN table_b B
ON A.PK = B.PK
WHERE B.PK IS NULL
UNION ALL
SELECT *
FROM table_a A
RIGHT JOIN table_b B
ON A.PK = B.PK
WHERE A.PK IS NULL;
```

![mysql-index-bTree](../_posts/mysql-connect-type/outer-join-exclude-inner-join-result.png)