---
layout: post
title: "MySQL 不常用操作"
subtitle: "MySQL not common operate"
author: "HaoDu"
catalog: true
tags:
  - DataStructure
---
### 多字段 `in`
```sql
SELECT
	department.NAME AS Department,
	employee.name as Employee,
	Salary
FROM
	employee
	JOIN department ON departmentId = department.id 
WHERE
	 (salary, departmentId)  IN (
	SELECT
	max(salary) salary ,
		departmentId
		
	FROM
		employee 
	GROUP BY
	departmentId 
	)
```
### `any` `all` 关键字
```sql
select * from Weather where Temperature > all(select Temperature from Weather where id <3)
select * from Weather where Temperature > any(select Temperature from Weather where id <3)
```

