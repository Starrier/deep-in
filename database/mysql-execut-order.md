

# MySQL 语句的执行顺序

``` sql
 from -> where -> group by -> having -> order by -> select;
```

## having

``` sql
select product, sum(price) from orders group by product having sum(price)>100;
```

`group by` 字句和 `where` 一起使用时，`where` 在前， `group by` 在后。

`having` 只能在 `group by` 之后，对分组后的结果进行筛选（即，使用 `having` 的前提条件是分组）

`where` 肯定在 `group by` 之前

`where` 后的条件表达式里不允许使用聚合函数，而 `having` 可以

## 执行顺序和编写顺序

``` sql
select product,sum(price) from orders GROUP BY product HAVING sum(price)>100 ORDER BY sum(price);
```

1. 执行 Where xx 对全表数据做筛选，返回第一个结果集
2. 针对 1 的结果集使用 group by 分组，返回第二个结果集
3. 针对 2 的结果集中的每一组数据执行 select xx，有几组就执行几次，返回第三个结果集
4. 针对 3 的结果集执行 having xx 进行筛选，返回第四个结果集
5. 针对 4 的结果集排序



1. `to_char` 优化成 `to_date` ,把 `=`左边函数移到右边

优化前:
``` sql
select sum(POINT) as point , to_char(p.EXPIRE_DATE,'yyyy/mm/dd') as strParam
     from MSTB_CUSTOMER p
     where p.STATUS = 1 
     and to_char(p.EXPIRE_DATE,'yyyymmdd')>=to_char(sysdate,'yyyymmdd') 
```

(需要优化的功能: 时间比较 , 忽略时分秒 , 只精确到日 )

优化后:

(from前面的 to_char(p.EXPIRE_DATE,'yyyy/mm/dd') 不影响效率)
``` sql
select sum(POINT) as point , to_char(p.EXPIRE_DATE,'yyyy/mm/dd') as strParam
     from MSTB_CUSTOMER p
     where p.STATUS = 1 
      and EXPIRE_DATE>=to_date(to_char(sysdate,'yyyymmdd'),'yyyymmdd')
```

优化前:

``` sql
select sum(POINT) as point , to_char(p.EXPIRE_DATE,'yyyy/mm/dd') as strParam
     from MSTB_CUSTOMER p
     where p.STATUS = 1 
     and to_char(p.EXPIRE_DATE,'yyyy-mm-dd') = '2018-12-04'
```
(需要优化的功能: 时间比较 , 忽略数据库时间字段的时分秒 , 精确到日)

优化后:
``` sql
select sum(POINT) as point , to_char(p.EXPIRE_DATE,'yyyy/mm/dd') as strParam
     from MSTB_CUSTOMER p
     where p.STATUS = 1 
     and  p.EXPIRE_DATE >=to_date( '2018-12-04' || '00:00:00' , 'yyyy-mm-dd  hh24:mi:ss')
     and  p.EXPIRE_DATE <=to_date( '2018-12-04' || '23:59:59' , 'yyyy-mm-dd  hh24:mi:ss')
```