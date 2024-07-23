---
created: 2022-03-28 17:02
modified: 2024-04-21 16:52
tags: [MySQL]
---

|        | MyISAM | InnoDB             |
| ------ | ------ | ------------------ |
| 事务     | ❌      | ✅                  |
| 行锁     | ❌      | ✅                  |
| 外键     | ❌      | ✅                  |
| 全文搜索   | ✅      | ❌                  |
| 表空间的大小 | 较小     | 较大，约为 MyISAM 的 2 倍 |

### MyISAM

MyISAM 是 MySQL 5.5 版本之前的默认引擎，支持全文检索、压缩、空间函数等，但是不支持事务和行级锁，所以一般用于有大量查询少量插入的场景来使用。而且 MyISAM 不支持外键，索引和数据是分开存储的。

### InnoDB

InnoDB 是 MySQL 5.5 版本之后的默认引擎，基于聚簇索引建立的，支持事务、外键、行级锁，并且通过 MVCC 来支持高并发，索引和数据存储在一起。

### 如何选择

1. 如果需要事务，选择 InnoDB，不需要则选择 MyISAM。

2. 如果大部分表操作都是查询，选择 MyISAM，有写又有读选 InnoDB。

3. 如果系统崩溃导致数据难以恢复，且成本高，不要选择 MyISAM。

> [!link] 参考链接
> [MySQL 查看修改存储引擎总结](https://www.cnblogs.com/kerrycode/p/6571868.html)
> [MySQL 存储引擎 MyISAM 和 InnoDB 的区别](https://juejin.cn/post/7108605020941058085)
