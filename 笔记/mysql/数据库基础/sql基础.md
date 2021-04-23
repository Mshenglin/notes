### SQL语法

#### 一、基础

##### 1.1 SQL的注释

##### 1.2 数据库表的建立与使用

#### 二、创建表

#### 三、修改表

#### 四、插入

```
insert into mytable(c1,c2) values(v1,v2);
```

#### 五、更新

```
UPDATE  mytable set col=val where id=1;
```

#### 六、删除

#### 七、查询

DISTINCT

相同值只会出现一次。作用于所有的列，也就是所有的列都相同才相同。

```mysql
SELECT DISTINCT col1, col2
FROM mytable;
```

LIMIT

限制返回的行数。可以有两个参数，第一个表示从那一条记录开始，第二条为返回的总行数。

```mysql
SELECT *
FROM mytable
LIMIT 2, 3;-- 返回3-5行
```

补充：WHERE语句在GROUP BY语句之前，SQL会在分组之前计算WHERE语句。HAVING语句在GROUP BY语句之后，SQL会在分组之后计算HAVING语句。

#### 八、排序

- ASC:升序（默认）
- DESC:降序

```mysql
select * from employees order by hire_date desc limit 2,1;
```



#### 九、过滤

#### 十、通配符

#### 十一、计算字段

#### 十二、函数

#### 十三、分组

#### 十四、子查询

#### 十五、连接

