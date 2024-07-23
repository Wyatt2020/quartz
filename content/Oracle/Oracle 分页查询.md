---
created: 2021-06-24 23:20
modified: 2024-07-21 23:24
tags: [Oracle]
---

分页规律总结：每页显示 m 条数据，查询第 n 页数据：

```sql
select * from 
(
	select rownum r,e.* 
	from 要分页的表 e 
	where rownum<=m*n
) t 
where r>m*n-m;
```

要分页的表既可以是真实的表，也可以是一个查询语句

分页查询员工信息按照工资排序：

```sql
select * from 
(
	select rownum r,t.* 
	from (select * from emp order by sal) t 
	where rownum<=10 
) where r>5
```
