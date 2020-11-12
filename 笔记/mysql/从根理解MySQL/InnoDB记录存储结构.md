### InnoDB页简介

InnoDB与磁盘读写数据的方式：**将数据划分成若干个页，以页作为磁盘和内存之间的交互基本单位，InnDB的页一般是16KB。**

### InnDB行格式

InnDB存储引擎有四种不同的行格式：Compact、Redundant、Dynamic、Compessed。

指定行格式的语法

```mysql
CREATE TABLE 表名(列的名称) ROW_FORMAT=行格式名称
ALTER TABLE 表名 ROW_FORMAT=行格式名称
```

实验

```mysql
mysql> USE xiaohaizi;
Database changed

mysql> CREATE TABLE record_format_demo (
    ->     c1 VARCHAR(10),
    ->     c2 VARCHAR(10) NOT NULL,
    ->     c3 CHAR(10),
    ->     c4 VARCHAR(10)
    -> ) CHARSET=ascii ROW_FORMAT=COMPACT;
Query OK, 0 rows affected (0.03 sec)
mysql> INSERT INTO record_format_demo(c1, c2, c3, c4) VALUES('aaaa', 'bbb', 'cc', 'd'), ('eeee', 'fff', NULL, NULL);
Query OK, 2 rows affected (0.02 sec)
Records: 2  Duplicates: 0  Warnings: 0
mysql> SELECT * FROM record_format_demo;
+------+-----+------+------+
| c1   | c2  | c3   | c4   |
+------+-----+------+------+
| aaaa | bbb | cc   | d    |
| eeee | fff | NULL | NULL |
+------+-----+------+------+
2 rows in set (0.00 sec)
```

#### COMPACT行格式

![img](https://user-gold-cdn.xitu.io/2019/3/12/169710e8fafc21aa?imageslim)

COMPACT分为**记录额外信息**和**记录的真实信息**两大部分。

##### **记录额外信息**

这段信息的作用是向服务器描述这条记录不得不额外添加的信息。记录的额外信息分为 **变长字段长度列表**、**NULL值列表**、**记录头信息**。

###### 变长字段长度列表

变长字段占用的存储空间分为两部分：

1. 真正的数据内容
2. 占用的字节数     

在`Compact`行格式中，把所有变长字段的真实数据占用的字节长度都存放在记录的开头部位，从而形成一个变长字段长度列表，各变长字段数据占用的字节数按照列的顺序**逆序**存放。

![image-20201109190425356](C:\Users\Dell\AppData\Roaming\Typora\typora-user-images\image-20201109190425356.png)