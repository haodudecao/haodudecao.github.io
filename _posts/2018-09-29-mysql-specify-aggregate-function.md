---
layout: post
title: "You can't specify target table for update in FROM clause"
author: "HaoDu"
header-style: text
tags:
  - MySQL
---
## 问题
```sql
update message set content='Hello World' where id in
( select min(id) as min_id from message)
```

mysql出现

**You can’t specify target table for update in FROM clause**
 
 这个错误的意思是不能在同一个sql语句中，先select同一个表的某些值，然后再update这个表。

## 解决方法：
### 1 join

也许您可以将表自己连接起来
 如果逻辑足够简单以重新构造查询，则丢掉子查询并使用适当的选择条件将表连接到自身。这将导致MySQL将表视为两种不同的事物，从而允许进行破坏性的更改。
 ```sql
 UPDATE tbl AS a
 INNER JOIN tbl AS b ON ....
 SET a.col = b.col
```

### 2 select的结果再通过一个中间表select多一次，使之成为临时表,可以避免这个问题

FROM子句中的嵌套子查询创建一个隐式临时表，因此它不算作您要更新的表。


```sql
update message set content='Hello World' where id in
( select min_id from --多套一层 select 
  ( select min(id) as min_id from message) g
)
```
****但请注意，从MySQL 5.7.6开始，优化器可能会优化子查询并仍然给您错误，除非您SET optimizer_switch = 'derived_merge=off';：***

