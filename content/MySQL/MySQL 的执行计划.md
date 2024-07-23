---
created: 2022-03-28 17:32
modified: 2023-08-29 16:17
tags: MySQL
---

# Explain（执行计划）

```sql
EXPLAIN select (select 1 from tb_author where id=1) from (select * from tb_ book where id=1) der;
```

## id

在多个 select 中，id 越大越先执行；如果 id 相同，上面的先执行。

## select_type

- derived
第一条执行的 sql 是 from 后面的子查询，该子查询只要在 from 后面，就会生成一张衍生表， 因此 der 的查询类型是 derived

- subquery
在 select 之后 from 之前的子查询

- primary
最外部的 select

- simple
不包含子查询的简单查询

- union
使用 union 进行的联合查询

## table

当前正在查询的表名

## type

type 列可以直观的判断出当前的 sql 语句的性能，type 里的取值和性能的优劣顺序如下：

```
null > system > const > eq_ref > ref > range > index > all
```

对于 SQL 优化来说，要尽量保证 type 列的值是属于 range 及以上级别。

- null

性能最好的，一般在使用了聚合函数操作索引列，结果直接从索引树获取即可，因此是性能最好。

- system

很少见，直接和一条记录进行匹配。

- const

使用主键索引或唯一索引和常量进行比较，这种性能非常好

- eq_ref

在进行多表连接查询时，如果查询条件使用了主键进行比较，那么当前查询类型是 eq_ref

```sql
EXPLAIN select * from tb_book_author left join tb_book on tb_book_author.book id = tb_book.id
```

- ref

简单查询：查询条件是普通列索引

```sql
EXPLAIN select * from tb_book where name='book1'
```

复杂查询：查询条件是普通列索引

```sql
EXPLAIN select book.id from tb_book left join tb_book_author on tb_book.id = tb_book_author.book_id
```

- range

使用索引进行范围查找

- index

查询没有条件判断，但是所有的数据都可以直接从索引树上获取

- all

没有使用索引，进行了全表扫描

## possible keys

此次查询可能会用到的索引。MySQL 内部优化器会进行判断，如果查询走索引的性能比全表扫描的性能要差，那么内部优化器就让此次查询进行全表扫描（这样的判断依据我们可以通过 trace 工具来查看）

## key

sql 语句实际使用的索引

## rows

sql 语句可能要查询的数据条数

## key_len

键的长度，通过这一列可以知道，当前查询命中了联合索引中的哪几列

## extra

extra 列提供了额外的信息，够帮助我们判断当前 sql 是否使用了覆盖索引、文件排序、使用了索引进行查询条件等等的信息。

- Using index

使用了覆盖索引

- Using where

where 的条件使用了普通索引

- Using index condition

查询的列没有完全被索引覆盖，并且 where 条件中使用普通索引

- Using temporary

在没有索引的列上执行去重操作，需要临时表来实现；这种情况可以通过给列加索引进行优化

- Using filesort

MySQL 对数据进行排序，都会使用磁盘或借助内存来完成，涉及到两个概念：单路排序、双路排序

- Select tables optimized away

当直接在索引列上使用聚合函数，意味着不需要操作表

# trace

在执行计划中，我们发现有的 sql 会走索引，有的 sql 即使明确使用了索引也不会走索引。这是因为 mysql 的内部优化器任务走索引的性能比全表扫描的性能要差，因此 mysql 内部优化器选择了使用全表扫描。依据来自于 trace 工具的结论。

```sql
set sess ion optimizer_trace="enabled=on", end_ markers_ in_ json=on; -- 开启 trace 
select * from employees where name > 'a' order by position; --执行查询 
select * from information_schema.OPTIMIZER_TRACE; --获得 trace 的分析结果
```
