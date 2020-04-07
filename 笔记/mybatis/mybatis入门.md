#### mybatis 的概述

​	mybatis是一个持久层框架，用Java语言编写。封装很多JDBC的细节，使开发者只需要关注sql语句本身，而无需关注注册驱动，创建连接等许多繁杂的过程，使用ORM思想实现了结果集的封装。

ORM：

​			**Object RelationalMappging 对象关系映射**

​				简单的收就是将数据库表和实体类联系起来，让我们可以操作实体类来实现操作数据库表。

#### mybatis入门

mybatis的入门案例

第一步：读取配置文件、

第二步：创建SqlSessionFactory工厂

第三步：创建sqlSession

第四步：创建dao接口的代理对象

第五步：执行Dao中的方法

第六步：释放资源

注意事项：

不要忘记映射配置告知内mybatis要封装到那个实体类中

配置方式：指定实体类的全类名。

