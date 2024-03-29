[nowcoder](https://www.nowcoder.com/ta/sql)

### 1.最晚入职员工

#### 题目描述

```sql
查找最晚入职员工的所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天(sqlite里面的注释为--,mysql为comment)
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,  -- '员工编号'
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

#### 解题思路

将记录按照降序排练，取出第一条记录，局yang'fenyangfen

```mysql
select * from employees
order by hire_date-- 按照hire_date降序排序
desc
limit 0,1;-- 从第0+1条开始选取，选取一条
```

### 2.查找入职员工时间排名倒数第三的员工所有信息

#### 题目描述

查找最晚入职员工的所有信息，为了减轻入门难度，目前所有的数据里员工入职的日期都不是同一天(sqlite里面的注释为--,mysql为comment)

```java
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,  -- '员工编号'
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

#### 解题思路

将数据库中的数据按照入职日期，降序排列，取出第三条信息。

```mysql
select * from employees
order by hire_date
desc limit 2,1;
```

### 3.查找各个部门当前领导当前薪水详情以及其对应部门编号dept_no

#### 题目表述

查找各个部门当前(dept_manager.to_date='9999-01-01')领导当前(salaries.to_date='9999-01-01')薪水详情以及其对应部门编号dept_no
(注:输出结果以salaries.emp_no升序排序，并且请注意输出结果里面dept_no列是最后一列)

```mysql

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL, -- '员工编号',
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL, -- '部门编号'
`emp_no` int(11) NOT NULL, --  '员工编号'
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));

```

#### 解题思路

查看两张表，找到他们连接的字段。使用连接查询，将题目中的条件带入。

```mysql
select s1.emp_no,s1.salary,s1.from_date,s1.to_date,s2.dept_no-- 要查找的字段
from salaries as s1 left join dept_manager as s2 -- 要被查询的表
on  s1.emp_no=s2.emp_no -- 两个表连接的条件
where s1.to_date='9999-01-01' and  s2.to_date='9999-01-01'-- 限制条件
order by s1.emp_no;
```

### 4.查找所有已经分配部门的员工的last_name和first_name

#### 题目描述

查找所有员工的last_name和first_name以及对应部门编号dept_no，**也包括暂时没有分配具体部门的员工**(请注意输出描述里各个列的前后顺序)

```mysql
CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

#### 解题思路

由于可能在拥有姓名但是没有部门的情况，所以需要使用外连接查询

```mysql
select s1.last_name,s1.first_name,s2.dept_no  -- 查询的列
from employees  s1
left join dept_emp  s2 -- 查询的主表和从表
on s1.emp_no=s2.emp_no; -- 限定条件
```



### 5.查找所有员工入职时候的薪水情况

#### 题目描述

查找所有员工入职时候的薪水情况，给出emp_no以及salary， 并按照emp_no进行逆序(请注意，一个员工可能有多次涨薪的情况)

```mysql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

#### 解题思路

根据题目的提示，一个员工可能拥有多次涨薪的机会，说明在salaries中存在一个员工的多条记录，所以只要查找入职的那天的薪资。

```mysql
select 
  e.emp_no,s.salary -- 被筛选的列 
 from employees as e
 inner join  salaries as s
 on e.emp_no=s.emp_no and e.hire_date = s.from_date -- 两表连接的条件
 order by 
 s.emp_no DESC;-- 排序
```

### 6.查找薪水涨幅超过15次的员工号emp_no以及其对应的涨幅次数t

#### 题目描述



#### 解题思路

### 1

#### 题目描述

#### 解题思路

### 1

#### 题目描述

#### 解题思路

### 1

#### 题目描述

#### 解题思路