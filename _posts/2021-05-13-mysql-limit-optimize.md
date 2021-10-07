---
layout: post
title: "MySQL 分页优化"
subtitle: "MySQL limit offset optimize"
author: "HaoDu"
catalog: true
tags:
  - MySQL

---
## limit offset 原理
```sql
 select * from tab1 limit 10000,10;
```
执行过程

1.全表扫描,取出 10000+10 行

2.舍弃掉前 10000 行(offset),留下 10 行

##优化
###### 条件筛选(强制走索引)

###### 子句

###### 延迟关联

