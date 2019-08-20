# XML 配置问题

### 1.resultMap 和 resultType 的区别

#### resultType:

1. 对应的是 Java 中的属性，大小写不敏感。
2. 如果放的是 java.lang.Map key 是查询语句的列名，value 是查询语句的值，大小写敏感。
3. 直接表示返回类型。

#### resultMap：

1. 指的是定义好了的 id，是定义好的 resultType 的引用。
2. 对外部 resultMap 的引用。
3. 单表查询：当使用 resultMap 做 SQL 语句返回结果类型处理时，通常需要在 mapper.xml 中定义 resultMap 进行 POJO 和相应表字段的对应。
4. 关联查询（一对一）：resultMap 对于一对一表连接的处理方式通常为在主表的 POJO 中添加嵌套另一个表的 POJO，然后在 mapper.xml 中采用 assoclation 节点元素进行对另一个表的链接处理。
5. 关联查询（一对多）：resultMap 的处理方式在订单表数据的 POJO 中添加一个 list，list 中为订单明细表的属性，例如：

```markup
-- 订单及订单明细的resultMap
    使用extends继承，不用在中配置订单信息和用户信息的映射
     -->
    <resultMap type="cn.itcast.mybatis.po.Orders" id="OrdersAndOrderDetailResultMap" extends="OrdersUserResultMap">
        <!-- 订单信息 -->
        <!-- 用户信息 -->
        <!-- 使用extends继承，不用在中配置订单信息和用户信息的映射 -->
        
        
        <!-- 订单明细信息
        一个订单关联查询出了多条明细，要使用collection进行映射
        collection：对关联查询到多条记录映射到集合对象中
        property：将关联查询到多条记录映射到cn.itcast.mybatis.po.Orders哪个属性
        ofType：指定映射到list集合属性中pojo的类型
         -->
         <collection property="orderdetails" ofType="cn.itcast.mybatis.po.Orderdetail">
             <!-- id：订单明细唯 一标识
             property:要将订单明细的唯 一标识 映射到cn.itcast.mybatis.po.Orderdetail的哪个属性
               -->
             <id column="orderdetail_id" property="id"/>
             <result column="items_id" property="itemsId"/>
             <result column="items_num" property="itemsNum"/>
             <result column="orders_id" property="ordersId"/>
         </collection>
        
    
    </resultMap>
```

