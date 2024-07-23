---
created: 2021-07-08 23:19
modified: 2024-07-21 23:20
tags: [Oracle]
---

```sql
SELECT TRUNC(months_between(sysdate, to_date('2000-06-11','yyyy-mm-dd'))/12) AS age from dual
```

年龄，小于 1 岁，计算天数

```sql
select decode(TRUNC(months_between(sysdate, to_date('2001-06-11','yyyy-mm-dd'))/12),0,trunc(sysdate-to_date('2001-06-11','yyyy-mm-dd')),TRUNC(months_between(sysdate, to_date('2001-06-11','yyyy-mm-dd'))/12)) from dual
```
