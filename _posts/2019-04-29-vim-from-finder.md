---
layout: post
title: "mysql explain"
subtitle: 'Open file with terminal Vim from the macOS Finder'
author: "Hux"
hidden: true
header-style: text
tags:
  - Vim
---
id: SELECT 查询的标识符. 每个 SELECT 都会自动分配一个唯一的标识符.

select_type: SELECT 查询的类型.

table: 查询的是哪个表

partitions: 匹配的分区

type: join 类型

possible_keys: 此次查询中可能选用的索引

key: 此次查询中确切使用到的索引.

ref: 哪个字段或常数与 key 一起被使用

rows: 显示此查询一共扫描了多少行. 这个是一个估计值.

filtered: 表示此查询条件所过滤的数据的百分比

extra: 额外的信息

type 类型的性能比较

ALL < index < range ~ index_merge < ref < eq_ref < const < system

最左前缀原则： 2.当b+树的数据项是复合的数据结构，比如(name,age,sex)的时候，b+数是按照从左到右的顺序来建立搜索树的，比如当(张三,20,F)这样的数据来检索的时候，b+树会优先比较name来确定下一步的所搜方向，如果name相同再依次比较age和sex，最后得到检索的数据；但当(20,F)这样的没有name的数据来的时候，b+树就不知道下一步该查哪个节点，因为建立搜索树的时候name就是第一个比较因子，必须要先根据name来搜索才能知道下一步去哪里查询。比如当(张三,F)这样的数据来检索时，b+树可以用name来指定搜索方向，但下一个字段age的缺失，所以只能把名字等于张三的数据都找到，然后再匹配性别是F的数据了， 这个是非常重要的性质，即索引的最左匹配特性。