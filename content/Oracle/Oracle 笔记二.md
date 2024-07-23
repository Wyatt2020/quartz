---
created: 2020-10-24T23:49:00
modified: 2024-07-21 23:49
tags:
  - Oracle
---

### 视图

视图就是提供一个查询的窗口，所有数据来自于原表。

```sql
---查询语句创建表
create table emp as select * from scott.emp;
---创建视图【必须有dba权限】
create view v_emp as select ename, job from emp;
---查询视图
select * from v_emp;
---修改视图[不推荐修改视图]
update v_emp set job='CLERK' where ename='ALLEN';
commit;
---创建只读视图
create view v_emp1 as select ename, job from emp with read only;
---视图的作用？
---第一：视图可以屏蔽掉一些敏感字段。
---第二：保证总部和分部数据及时统一。
```

### 索引

索引就是在表的列上构建一个二叉树，达到大幅度提高查询效率的目的，但是索引会影响增删改的效率。

#### 单列索引

```sql
---创建单列索引
create index idx_ename on emp(ename);
```

单列索引触发规则，条件必须是索引列中的原始值。

单行函数，模糊查询，都会影响索引的触发。

#### 复合索引

```sql
---创建复合索引
create index idx_enamejob on emp(ename, job);
```

复合索引中第一列为优先检索列；如果要触发复合索引，必须包含有优先检索列中的原始值。

```sql
select * from emp where ename='SCOTT' and job='xx';---触发复合索引
select * from emp where ename='SCOTT' or job='xx';---不触发索引
select * from emp where ename='SCOTT';---触发单列索引。
```
