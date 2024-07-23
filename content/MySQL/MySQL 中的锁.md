---
created: 2022-03-28 16:44
modified: 2023-08-29 16:18
tags:
  - MySQL
---

## 锁的分类

1. 根据性能划分：乐观锁和悲观锁
	- **悲观锁**：悲观锁认为自己在使用数据的时候一定有别的线程来修改数据，因此在获取数据的时候会先加锁，确保数据不会被别的线程修改。（行锁，表锁，读锁，写锁，`select...lock in share mode`，`select…for update`）
	- **乐观锁**：乐观锁认为自己在使用数据时不会有别的线程修改数据，所以不会添加锁，只是在更新数据的时候去判断之前有没有别的线程更新了这个数据。（版本号机制或 `CAS`）
2. 根据数据的操作粒度划分：表锁和行锁
	- **表锁**：SQL 没有命中索引，锁住整张表。开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突概率高，并发度最低。
	- **行锁**： SQL 命中了索引，锁住的就是命中条件的数据行。开销大，加锁慢；会出现死锁；锁定粒度小，发生锁冲突的概率低，并发度高。
	- `InnoDB` 有行锁和表锁，`MyIsam` 只有表锁。
3. 根据数据库的操作类型划分：读锁和写锁
	- 这两种锁都属于悲观锁
	- **读锁**(共享锁) ：多个事务对于同一数据可以共享一把锁，都能访问到数据，但是只能读不能修改
	- **写锁**(排他锁) ：一个事务给一行数据加了写锁之后，在释放写锁之前，其他事务不能对这行数据进行读和修改

## 表锁

对整张表进行上锁。`MyISAM` 存储引擎是天然支持表锁的，也就是说在 `MyISAM` 的存储引擎的表中如果出现并发的情况，将会出现表锁的效果。`MyISAM` 不支持事务。

在 `InnoDB` 中使用表锁：

```sql
# 对一张表上读锁/写锁格式 
lock table 表名 read/write; 
# 例子 
lock table tb_book read; 
# 查看当前会话对所有表的上锁情况 
show open tables; 
# 释放当前会话的所有锁 
unlock tables;
```

读锁：其他事务可以进行读，但是不能进行写
写锁：其他事务不能进行读和写

## 行锁

`MyISAM` 只支持表锁，不支持行锁，`InnoDB` 可以支持行锁。在并发事务里，每个事务的增删改的操作相当于是上了行锁。

```sql
//关闭自动提交模式 
set autocommit=0; 
//开始事务 
begin; 
//查询出商品id为1的库存表数据 
select * from db_stock where goods_id = 1 for update; 
//减库存 
update db_stock set stock_num = stock_num - 1 where goods_id = 1 ; 
//提交事务 
commit;
```

## 间隙锁

行锁只能对某一行上锁，如果想对某一个范围上锁，可以使用间隙锁。间隙锁给的条件 `where id>13 and id<19`，会对 13 和 19 所处的间隙进行上锁。

> [!info]  参考链接
> 
> - [悲观锁和乐观锁的那些事儿](https://mp.weixin.qq.com/s/2tUkOFBGdIqKUvfdQ1L_JQ)
> - [什么是间隙锁](https://blog.csdn.net/qq_21729419/article/details/113643359#commentBox)
> - [为什么开发人员必须要了解数据库锁](https://github.com/javagrowing/JGrowing/blob/master/%E8%AE%A1%E7%AE%97%E6%9C%BA%E5%9F%BA%E7%A1%80/%E6%95%B0%E6%8D%AE%E5%BA%93/mysql/%E4%B8%BA%E4%BB%80%E4%B9%88%E5%BC%80%E5%8F%91%E4%BA%BA%E5%91%98%E5%BF%85%E9%A1%BB%E8%A6%81%E4%BA%86%E8%A7%A3%E6%95%B0%E6%8D%AE%E5%BA%93%E9%94%81%EF%BC%9F.md)
> - [MySQL 中间隙锁的加锁规则](https://www.modb.pro/db/74024)
