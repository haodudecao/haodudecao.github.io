---
layout: post
title: "MySQL 不常用操作"
subtitle: "「Data Structure」- Linked List"
author: "HaoDu"
tags:
  - DataStructure
---
多字段 in
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
