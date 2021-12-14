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
 select * from user limit 10000,10;
```
执行过程

1.全表扫描,取出 10000+10 行

2.舍弃掉前 10000 行(offset),留下 10 行

## 优化
#### 条件筛选（强制走索引）
laravel 中的 `chuckById` 就是这么做的
```php
// src/Illuminate/Database/Concerns/BuildsQueries.php
/**
 * Chunk the results of a query by comparing IDs.
 *
 * @param  int  $count
 * @param  callable  $callback
 * @param  string|null  $column
 * @param  string|null  $alias
 * @return bool
 */
public function chunkById($count, callable $callback, $column = null, $alias = null)
{
    $column = $column ?? $this->defaultKeyName();

    $alias = $alias ?? $column;

    $lastId = null;

    $page = 1;

    do {
        $clone = clone $this;

        // We'll execute the query for the given page and get the results. If there are
        // no results we can just break and return from here. When there are results
        // we will call the callback with the current chunk of these results here.
        $results = $clone->forPageAfterId($count, $lastId, $column)->get();

        $countResults = $results->count();

        if ($countResults == 0) {
            break;
        }

        // On each chunk result set, we will pass them to the callback and then let the
        // developer take care of everything within the callback, which allows us to
        // keep the memory low for spinning through large result sets for working.
        if ($callback($results, $page) === false) {
            return false;
        }

        $lastId = $results->last()->{$alias};

        if ($lastId === null) {
            throw new RuntimeException("The chunkById operation was aborted because the [{$alias}] column is not present in the query result.");
        }

        unset($results);

        $page++;
    } while ($countResults == $count);

    return true;
}

// src/Illuminate/Database/Query/Builder.php

/**
 * Constrain the query to the next "page" of results after a given ID.
 *
 * @param  int  $perPage
 * @param  int|null  $lastId
 * @param  string  $column
 * @return $this
 */
public function forPageAfterId($perPage = 15, $lastId = 0, $column = 'id')
{
    $this->orders = $this->removeExistingOrdersFor($column);

    if (! is_null($lastId)) {
        $this->where($column, '>', $lastId);
    }

    return $this->orderBy($column, 'asc')
                ->limit($perPage);
}
```
`chuckById` 每次都会记录本批次最后一个列 `id` 再通过  `forPageAfterId` 方法组成 如下 SQL:
```sql
select * from user where `id` > 9000 limit 1000;
```
`id` 通常设置为自增，我们认为其按顺序排列，那这样可以直接跳过前 9000 行

#### 子句
原查询：
```sql
 select * from user limit 10000,10;
```
改为子句：
```sql
 select * from user a ,(select `id` from user limit 10000,10)  b where a.id = b.id;
```
#### 延迟关联

