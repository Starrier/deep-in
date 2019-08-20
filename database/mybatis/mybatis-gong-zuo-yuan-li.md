# MyBatis 工作原理

![MyBatis](../../.gitbook/assets/mybatis.png)

MyBatis 工作原理

* 通过SqlSessionFactoryBuilder从mybatis-config.xml配置文件中构建出SqlSessionFactory。
* SqlSessionFactory开启一个SqlSession，通过SqlSession实例获得Mapper对象并且运行Mapper映射的Sql语句。
* 完成数据库的CRUD操作和事务提交，关闭SqlSession。

