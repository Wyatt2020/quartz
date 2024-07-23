---
created: 2024-06-26 23:14
modified: 2024-06-26 23:14
tags:
  - MySQL
---

**A 原子性**：利用 InnoDB 的 **undo log**。undo 日志记录需要回滚的日志信息，当事务回滚时能够撤销所有已经成功执行的 SQL 语句

**C 一致性**：一致性是事务追求的最终目标，**原子性、持久性和隔离性都是为了保证一致性的手段**。另外，程序代码要保证业务上的一致性，比如购买操作只扣除用户的余额，不减库存，肯定无法保证状态的一致。

**I 隔离性**：利用锁和 MVCC 机制。

**D 持久性**：利用 InnoDB 的 **redo log**。当做数据修改的时候，不仅在内存中操作，还会在 redo 日志中记录这次操作。当事务提交的时候，会将 redo 日志进行刷盘 (redo log 一部分在内存中，一部分在磁盘上)。当数据库宕机重启的时候，会将 redo 日志中的内容恢复到数据库中，再根据 undo log 和 binlog 内容决定回滚数据还是提交数据。

> [!link] Reference
> [浅谈 MySQL 如何保证 ACID](https://www.jianshu.com/p/7e5853d7087a)
> [MySQL 如何保证事务的 ACID 特性的](https://simonzgx.github.io/2020/07/29/MYSQL%E4%BA%8B%E5%8A%A1%E5%A6%82%E4%BD%95%E4%BF%9D%E8%AF%81%E5%8E%9F%E5%AD%90%E6%80%A7/)
