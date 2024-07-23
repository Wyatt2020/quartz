---
created: 2020-10-23 23:25
modified: 2024-07-21 23:50
tags: [Oracle]
---

### 字符函数

**转大写**

`dual` 是虚拟表，用于补全语法

```sql
select upper('yes') from dual;  --YES
```

**转小写**

```sql
select lower('YES') from dual;   --yes
```

### 数值函数

- **四舍五入**，保留两位小数
- `-2` 表示对小数点前面两位四舍五入

```sql
select round(13.14624, 2) from dual; --13.15
```

**直接截取**

```sql
select trunc(13.14624, 2) from dual; --13.14
```

**取余**

```sql
select mod(10, 3) from dual;  --1
```

### 日期函数

`sysdate`：系统时间，可以与数字进行运算

当前时间 +1 天

```sql
select sysdate+1 from dual;
```

某天距离现在几个月

```sql
select mouths_between(sysdate, t.date) from tb_name t;
```

### 转换函数

**日期转字符**

`fm` 月份不加 0 

`24` 24 小时制

```sql
select TO_CHAR(sysdate, 'fm yyyy-mm-dd hh24:mi:ss') from dual;
```

**字符转日期**

```sql
select TO_DATE(sysdate, 'fm yyyy-mm-dd hh24:mi:ss') from dual;
```

### NVL

如果 `t.a` 为 `null`，返回 0，否则返回 `t.a`

```sql
select nvl(t.a, 0) from tb_name t;
```

### 条件表达式

> Oracle、MySQL 通用

**等值选择**

`else` 可省略

```sql
select t.a
    case t.b
        when 'A' then 'a'
        when 'B' then 'b'
        else 'c'
    end
from tb_name t
```

**范围选择**

`else` 可省略

```sql
select e.sal
    case
        when e.sal>5000 then '高收入'
        when e.sal>3000 then '中收入'
        else '低收入'
    end
from emp e
```

### decode

```sql
select t.a,
    decode(t.b,
       'A', 'a',
       'B', 'b',
       'c') b
from tb_name t
```

> [!tip] 
> 这是 Oracle 特有的语法，为了代码可重用，建议用通用语法

### 聚合函数

可以把多行记录变成一个值

`count` 

`sum` 

`max` 

`min` 

`avg`

### 分组查询

分组查询中，出现在 `group by` 后面的原始列，才能出现在 `select` 后面；没有出现在 `group by` 后面的列，想在 `select` 后面，必须加上聚合函数。

```sql
select e.deptno, avg(e.sal)
from emp e
where e.sal>800
group by e.deptno
having avg(e.sal)>2000
```

where 和 having 的区别：

- where 是过滤分组前的数据，having 是过滤分组后的数据

- where 必须在 group by 之前，having 是在 group by 之后

- 不能使用别名做条件

### 连接查询

**等值连接**

```sql
select *
from emp e,dept d
where e.deptno=d.deptno;
```

如果 d 表为空，则结果为空

**内连接**

```sql
select *
from emp e inner join dept d
on e.deptno=d.deptno;
```

> [!tip] 
> 等值连接与内连接的结果是相同的，推荐用等值连接，通俗易懂

**左连接**

```sql
select *
from emp e left join dept d
on e.deptno=d.deptno;
```

emp 的数据全部展示，如果 dept 没有与 emp 匹配的数据，则展示为 `null`

**右连接**

```sql
select *
from emp e right join dept d
on e.deptno=d.deptno;
```

dept 的数据全部展示，如果 emp 没有与 dept 匹配的数据，则展示为 `null`

**自连接**

站在不同角度，把一张表看成多张表

```sql
--查询出员工姓名，员工部门名称，员工领导姓名，员工领导部门名称
select e1.ename, d1.dname, e2.ename, d2.dname
from emp e1,emp e2, dept d1, dept d2
where e1.mgr= e2.empno
and e1.deptno=d1.deptno
and e2.deptno=d2.deptno;
```

#### Oracle 专用语法

**左连接**

```sql
select *
from emp e,dept d
where e.deptno=d.deptno(+);
```

**右连接**

```sql
select *
from emp e,dept d
where e.deptno(+)=d.deptno;
```

> [!tip] 
> 为了代码可重用，建议用通用语法

### 分页查询

`rownum`: 当我们做 `select` 操作的时候，每查询出一行记录，就会在该行上加上一个行号，行号从 1 开始，依次递增，不能跳着走。

排序操作 (`order by`) 会影响 `rownum` 的顺序

```sql
select rownum, e.* from emp e order by e.sal desc;
```

如果涉及到排序，还要使用 `rownum` 的话，我们可以再次嵌套查询。

`r1` 是顺序，`r2` 的顺序被打乱

```sql
select rownum r1, t.* from(
select rownum r2, e.* from emp e order by e.sal desc) t;
```

每页五条记录，查询第二页 (分页查询时，条件不能写 `rownum` 大于一个正数)

```sql
select * from (
    select rownum rn,e.* from (
        select * from emp order by sal desc
    ) е where rownum <= 10 --offset=pageSize*pageNum
) where rn > 5 --pageSize*(pageNum-1)
```

分页模版

```sql
pageSize=5, offset=pageSize*pageNum
```
