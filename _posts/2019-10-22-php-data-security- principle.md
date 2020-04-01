---
layout: post
title: "MySQL Window Function"
author: "HaoDu"
tags:
  - MySQL
---
MySQL 8.0 窗口函数
---

窗口函数在其他数据库中早已经实现，但是 MySQL 直到 8.0 才加入；

## 数据表
```sql
CREATE TABLE `Employee` (
  `Id` int DEFAULT NULL,
  `Name` varchar(255) DEFAULT NULL,
  `Salary` int DEFAULT NULL,
  `DepartmentId` int DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
## Window Function
### ROW_NUMBER()
ROW_NUMBER 返回其分区内当前行的编号。行数范围从1到分区行数。

ORDER BY影响行的编号顺序。没有ORDER BY，行编号是不确定的。

ROW_NUMBER()为同级分配不同的行号。要为同级分配相同的值，请使用 RANK()或 DENSE_RANK()。
```sql
SELECT
       `name`,
       departmentid as dept,
       `salary`,
       row_number() over (PARTITION BY departmentid ORDER BY salary DESC ) AS salary_rank
FROM
       employee
```
结果
```
+-------+------+--------+-------------+
| name  | dept | salary | salary_rank |
+-------+------+--------+-------------+
| Max   |    1 |  90000 |           1 |
| Joe   |    1 |  85000 |           2 |
| Randy |    1 |  85000 |           3 |
| Will  |    1 |  70000 |           4 |
| Janet |    1 |  69000 |           5 |
| Henry |    2 |  80000 |           1 |
| Sam   |    2 |  60000 |           2 |
+-------+------+--------+-------------+
```
### rank(); 
> 　　类似于 row_number()，也是排序功能，但是rank()有什么不一样？<br>
> 新的事物的出现必然是为了解决潜在的问题。
　　如果再往测试表中写入一条数据：insert into order_info values (11,'u0002',800,'2018-1-22');
　　对于测试表中的U002用户来说，有两条create_date完全一样的数据（假设有这样的数据），那么在row_number()编号的时候，这两条数据却被编了两个不同的号
　　理论上讲，这两条的数据的排名是并列最新的。因此rank()就是为了解决这个问题的，也即：排序条件一样的情况下，其编号也一样。
```sql
SELECT
       `name`,
       departmentid as dept,
       `salary`,
       rank() over (PARTITION BY departmentid ORDER BY salary DESC ) AS salary_rank
FROM
       employee
```
结果


```
+-------+------+--------+-------------+
| name  | dept | salary | salary_rank |
+-------+------+--------+-------------+
| Max   |    1 |  90000 |           1 |
| Joe   |    1 |  85000 |           2 |
| Randy |    1 |  85000 |           2 |
| Will  |    1 |  70000 |           4 |
| Janet |    1 |  69000 |           5 |
| Henry |    2 |  80000 |           1 |
| Sam   |    2 |  60000 |           2 |
+-------+------+--------+-------------+
```
### dense_rank(); 
> dense_rank()的出现是为了解决rank()编号存在的问题的，
　　rank()编号的时候存在跳号的问题，如果有两个并列第1，那么下一个名次的编号就是3，结果就是没有编号为2的数据。
　　如果不想跳号，可以使用dense_rank()替代。
```sql
SELECT
       `name`,
       departmentid as dept,
       `salary`,
       dense_rank() over (PARTITION BY departmentid ORDER BY salary DESC ) AS salary_rank
FROM
       employee
```
结果


```
+-------+------+--------+-------------+
| name  | dept | salary | salary_rank |
+-------+------+--------+-------------+
| Max   |    1 |  90000 |           1 |
| Joe   |    1 |  85000 |           2 |
| Randy |    1 |  85000 |           2 |
| Will  |    1 |  70000 |           3 |
| Janet |    1 |  69000 |           5 |
| Henry |    2 |  80000 |           1 |
| Sam   |    2 |  60000 |           2 |
+-------+------+--------+-------------+
```