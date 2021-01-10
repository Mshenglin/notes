[nowcoder](https://www.nowcoder.com/practice/218ae58dfdcd4af195fff264e062138f?tpId=82&&tqId=29753&rp=1&ru=/ta/sql&qru=/ta/sql/question-ranking)

### 最晚入职员工

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

将记录按照降序排练，取出第一条记录

```mysql
select * from employees
order by hire_date-- 按照hire_date降序排序
limit 0,1;-- 从第0+1条开始选取，选取一条
```

