---
title: mysql-index-search
date: 2019-02-19 22:33:06
tags: SpringBoot
index_img: ../post_img/mysql-index-search.jpeg
---

## MySQL 复合索引

### 命名规则

 1. 需要加索引的字段，要在 where 条件中
 2. 数据量少的字段不需要加索引
 3. 如果 where 条件中是 or 关系，加索引不起作用

联合索引又叫做复合索引。对于复合索引：MySQL 从左到右，使用索引中的字段，一个查询可以只使用索引中的一部分，但只能是左侧部分。例如，索引为 key index(a,b,c)，可以支持 a|a，b|a，b，c 3 种组合进行查找，但不支持 b，c 进行查找，但最左侧字段是常量引用时，索引就十分有效。

两个或两个以上的索引被称为复合索引。利用索引中的附加列，可以缩小搜索的范围。

### 创建索引

在执行 create table 语句时可以创建索引，也可以单独使用 create index 或 alter table 来为表添加索引。

 1. alter table

 alter table 用来创建普通缩影、unique 索引或 primary key 索引

```sql
/*tabel_name：要添加索引的表名
  column_list：指出对那些列进行索引
  index_name：索引名，可选；缺省时，MySQL 将根据第一个索引列赋一个名称。
*/
alter table table_name ADD INDEX index_name(column_list)
alter table table_name add unique(column_list)
alter table table_naem add primary key(column_list)
```

`alter table` 允许在单个语句中更改多个表，因此可以同时创建多个索引。

 2. create index

create index 可以对表增加普通索引或 unique 索引。

```sql
create index index_name on table_name(column_list);
create unique index index_name on table_name(column_list)
```

**注意**：不能用 create index 语句创建 primary key 索引。