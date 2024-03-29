---
layout: post
title: "MySQL 回表"
author: "HaoDu"
subtitle: 'Table access by index rowId'
header-style: text
date:       2019-08-08
tags:
  - MySQL

---
丁奇老师在 《MySQL 实战 45 讲》中说到回表
### 什么是回表？

简单来说就是数据库根据索引（非主键）找到了指定的记录所在行后，还需要根据索引上保存的主键 ID 再次到数据块里获取数据。
#### table-access-by-index-rowid
「回表」一般就是指执行计划里显示的 「TABLE ACCESS BY INDEX ROWID」。
再例如，虽然只查询索引里的列，但是需要回表过滤掉其他行。

在 InnoDB 里，索引 B+ Tree 的叶子节点存储了整行数据的是主键索引，也被称之为聚簇索引。而索引 B+ Tree 的叶子节点存储了主键的值的是非主键索引，也被称之为非聚簇索引。

### 如何避免回表

将需要的字段放在索引中去，查询的时候就能避免回表。
#### 科普时间——覆盖索引
覆盖索引（covering index）指一个查询语句的执行只用从索引中就能够取得，不必从数据表中读取。也可以称之为实现了索引覆盖。当一条查询语句符合覆盖索引条件时，MySQL 只需要通过索引就可以返回查询所需要的数据，这样避免了查到索引后再返回表操作，减少 I/O 提高效率。例如：

表 `covering_index_sample` 中有一个联合索引 `idx_key1_key2(key1,key2)`

当我们通过SQL语句：
```
select key2 from covering_index_sample where key1 = ‘keytest’;
```
的时候，就可以通过覆盖索引查询，无需回表。


