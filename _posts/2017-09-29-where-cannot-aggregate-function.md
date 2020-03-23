---
layout: post
title: "MySQL where 子句不能使用聚合函数"
author: "HaoDu"
header-style: text
tags:
  - MySQL
---
##  原理
where 子句是对逐条的行记录进行筛选

而聚合函数是操作结果集的;
> 聚合函数对一组值执行计算，并返回单个值，也被称为组函数。 聚合函数经常与 SELECT 语句的 GROUP BY 子句的HAVING一同使用。

显然如果在未筛选数据时操作是不合理的,逻辑上也是说不通的
所以下边 sql 会报错  **Invalid use of group function**

```sql
delete from Person where Id in 
(select max(Id) from Person group by Email having count(Id) > 1);

```

与where子句不能出现聚集函数正相反的是，我们几乎看不到不使用聚集函数的having子句。为什么？因为在水平方向上根据外部指定条件的筛选（也就是对行的筛选），where子句可以独立完成，剩下的往往都是需要根据结果集自身的统计数据进一步筛选了，这时，几乎都需要通过having子句配合聚集函数来完成。

```sql
delete from Person where Id in (select max(Id) from Person   where count(id) > 1 group by Email );
```



