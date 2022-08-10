---
layout: post
title: "MySQL 慢查询优化之-降序索引"
subtitle: "MySQL Slow Query Optimization - Descending Index"
author: "HaoDu"
catalog: true
tags:

- MySQL

---

## 起因

查看 php-fpm 慢日志时发现有个方法频繁超时（默认超时时间为 10s)，通过本地及生产访问，猜测是 MySQL 慢查询问题。
找到阿里云后台
![](/img/img.png)
平均执行 10+s，最长执行时长 50s……，阿里云这个建议点了之后一直转圈😓

### 开始优化

先来看一下表结构

```sql
create table notifications
(
    id              char(36)        not null
        primary key,
    type            varchar(255)    not null,
    notifiable_type varchar(255)    not null,
    notifiable_id   bigint unsigned not null,
    data            text            not null,
    read_at         timestamp       null,
    created_at      timestamp       null,
    updated_at      timestamp       null
)
    collate = utf8mb4_unicode_ci;

create index notifications_notifiable_type_notifiable_id_index
    on notifications (notifiable_type, notifiable_id);
```
下面是慢查询语句

```sql
   select *
   from `notifications`
   where `notifications`.`notifiable_type` = '巴拉巴拉'
     and `notifications`.`notifiable_id` = 2
   order by `created_at` desc,
            `read_at`
   limit 15 offset 0;
 ```

这个 notifications 是 Laravel 自带的消息通知系统。

explain 分析一下，Extra 格外引人注目，`filesort` 可不是个好东西，覆盖索引可破之，于是加上覆盖索引![](/img/img_1.png)
notifiable_type, notifiable_id,created_at,read_at ，结果还是不走这个索引，而且我发现去掉 read_at 的排序条件后，就走这个索引了，Extra 列显示 `Backward index scan`

于是 Google “两个字段排序对索引的影响”，发现两个字段如果排序方向不一致是不会走索引的，但是 MySQL8.0 新增了 **降序索引** 可破，这是个好东西。
于是加上这个索引
```sql
create index idx_created_at_read_at
on notifications (notifiable_type, notifiable_id, created_at desc, read_at);
```
![](/img/img_2.png)
终于走索引了，没有额外的排序，试了下查询时间，从50s 降到  300ms，收益几百倍，爽。
### 总结
避免额外排序可以使用覆盖索引。

降序索引可以不用排序，直接扫描二级索引树就得到了排好的数据。





