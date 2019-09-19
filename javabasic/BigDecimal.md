# BigDecimal 常用类

## 简介

Java 在 java.math 包中提供的 API 类 BigDecimal，用来对超过 16 位有效位的数进行精确的运算。双精度浮点型变量 double 可以处理 16 位有效数。在实际应用中，需要对更大或者更小的数进行运算和处理。float 和 double 只能用来做科学计算或者是工程计算，在商业计算中要用 java.math.BigDecimal。BigDecimal 所创建的是对象，我们不能使用传统的 +、-、*、/ 等算术运算符直接对其对象进行数学运算，而必须调用其相对应的方法。方法中的参数也必须是 BigDecimal 的对象。构造器是类的特殊方法，专门用来创建对象，特别是带有参数的对象。

## 构造器

```
BigDecimal(int)       创建一个具有参数所指定整数值的对象。
BigDecimal(double) 创建一个具有参数所指定双精度值的对象。
BigDecimal(long)    创建一个具有参数所指定长整数值的对象。
BigDecimal(String) 创建一个具有参数所指定以字符串表示的数值的对象。
```

这几个都是常用的构造器，他们返回的对象都是BigDecimal对象。换而言之， 将各个类型的值转换为BigDecimal对象，就是通过构造器。

反过来说，将 BigDecimal 对象转换为其他类型的对象，我们通过以下几种：

```
toString()                将BigDecimal对象的数值转换成字符串。
doubleValue()          将BigDecimal对象中的值以双精度数返回。
floatValue()             将BigDecimal对象中的值以单精度数返回。
longValue()             将BigDecimal对象中的值以长整数返回。
intValue()               将BigDecimal对象中的值以整数返回。
```

常用方法

```java
BigDecimal b1 = new BigDecimal("20");
BigDecimal b2 = new BigDecimal("30");
```

```
b1.add(b2) ：加法，求两个BigDecimal类型数据的和。
b1.subtract(b2)：减法，求两个BigDecimal类型数据的差。
b1.multiply(b2)：乘法，求两个BigDecimal类型数据的积。
b1.remainder(b2)：求余数，求b1除以b2的余数。
b1.max(b2) : 最大数，求两个BigDecimal类型数据的最大值
b1.min(b2) : 最小数，求两个BigDecimal类型数据的最小值。
bi.abs()：绝对值，求BigDecimal类型数据的绝对值。
b1.negate()：相反数，求BigDecimal类型数据的相反数。
```
这里把除法单独拉出来

```java
BigDecimal divide(BigDecimal divisor, int scale, int roundingMode)
```

除法 divide 有三个参数的方法，第一参数表示除数，第二个参数表示小数点后保留位数，第三个参数表示取舍规则。只有在作除法运算或四舍五入时才用到取舍规则。 因为 BigDecimal 除法可能出现不能整除的情况，比如 4.5/1.3，这时会报错 java.lang.ArithmeticException: Non-terminating decimal expansion; no exact representable decimal result。所以当我们用三参数的除法方法时，规定了保留几位小数以及你的保留方式，就可以避免异常。

几个取舍规则：

ROUND_CEILING //向正无穷方向舍入
ROUND_DOWN //向零方向舍入
ROUND_FLOOR //向负无穷方向舍入
ROUND_HALF_DOWN  //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，向下舍入, 例如1.55 保留一位小数结果为1.5
ROUND_HALF_EVEN  //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，如果保留位数是奇数，使用ROUND_HALF_UP，如果是偶数，使用ROUND_HALF_DOWN
ROUND_HALF_UP  //向（距离）最近的一边舍入，除非两边（的距离）是相等,如果是这样，向上舍入, 1.55保留一位小数结果为1.6
ROUND_UNNECESSARY //计算结果是精确的，不需要舍入模式
ROUND_UP //向远离0的方向舍入

我们最常用的四舍五入应该是 ROUND_HALF_UP

1
Note that this is the rounding mode that most of us were taught in grade school.
源码中的注释也解释了这一点。

这里说下除法里的第三个参数，

 a.divide(b,2,RoundingMode.HALF_UP)
这里RoundingMode其实是个枚举类，点进去源码可以看到其实他就是匹配到几种取舍规则

UP(BigDecimal.ROUND_UP),
DOWN(BigDecimal.ROUND_DOWN),
CEILING(BigDecimal.ROUND_CEILING),
FLOOR(BigDecimal.ROUND_FLOOR),
HALF_UP(BigDecimal.ROUND_HALF_UP),
HALF_DOWN(BigDecimal.ROUND_HALF_DOWN),
HALF_EVEN(BigDecimal.ROUND_HALF_EVEN),
UNNECESSARY(BigDecimal.ROUND_UNNECESSARY);

而最常用的就是 HALF_UP 也就是四舍五入。

那如果我们在乘法或者加法减法中也要保留几位或者四舍五入，该怎么操作呢？

四舍五入

BigDecimal中有一个setScale()方法

第一个参数就是你要保留几位，第二个可填的参数就是取舍规则，图中的两种殊途同归。

如果你第二个参数不加，仅仅想保留几位，他在源码中会自动帮你选择默认的规则。

```java
 public BigDecimal setScale(int newScale) {
        return setScale(newScale, ROUND_UNNECESSARY);
    }

/**
     * Rounding mode to assert that the requested operation has an exact
     * result, hence no rounding is necessary.  If this rounding mode is
     * specified on an operation that yields an inexact result, an
     * {@code ArithmeticException} is thrown.
     */
    public final static int ROUND_UNNECESSARY =  7;
```

因为我看了下这个语句，主要是 marketName 和 cityCode 不一样。所以我的想法是这样的 
1. 将需要查询的 city 封装到一个 List 中，里面放属性包含cityName 和 cityCode 的类。
2. 然后在 mybatis 中 foreach List，返回需要的值，并放入封装的类，以 List 类型返回。


https://oapi.dingtalk.com/robot/send?access_token=e4b6256f13fd78fc28d3d0ea85a99c4cc49b0ed319b7b0b36de976f72de52e22

```sql
本月城市累计销量：
SELECT
  t1.marketName 城市名称,
  t1.OrderCount 订单数量（单）,
  format(t1.actual_paid_fee,2)  GMV值（元）
FROM 
(
    SELECT 
  '上海市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='310100'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
 
 SELECT 
  '杭州市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='330100'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
    
 SELECT 
  '南京市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='320100'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
 
 SELECT 
  '苏州市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='320500'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
    
 SELECT 
  '常州市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='320400'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
 
 SELECT 
  '绍兴市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='330600'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
    
 SELECT 
  '青岛市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='370200'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
 
 SELECT 
  '南通市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='320600'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
    
 SELECT 
  '无锡市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='320200'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
 
 SELECT 
  '长沙市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='430100'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
    
 SELECT 
  '广州市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='440100'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
 
 SELECT 
  '武汉市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='420100'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
 UNION ALL
    
 SELECT 
  '威海市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='371000'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
  UNION ALL
    
 SELECT 
  '宁波市' as marketName,
  sum(sb.`actual_paid_fee`) / 100 as actual_paid_fee,
  COUNT(sb.`order_id`) as OrderCount
FROM `txp_suborder` sb
where sb.`seller_id`= 3963690401
 AND sb.`parent_status` in (2,6)
 AND sb.`city_code` ='330200'
 AND sb.`pay_time` >='2019-09-01'
 AND sb.`pay_time` <curdate()+1
)t1
order by t1.actual_paid_fee desc
```