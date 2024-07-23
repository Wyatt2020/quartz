---
created: 2022-03-28 17:08
status: doing
tags:
  - MySQL
---

MySQL 在读和写的操作中，对读的性能做了并发性的保障，让所有的读都是快照读，对于写的时候，进行版本控制。如果真实数据的版本比快照版本新，那么写之前就要进行版本(快照)更新，这样就可以既能够提高读的并发性，又能够保证写的数据安全。
![](202203281710708.png)


> [!info]  参考链接
> - [MySQL 的 MVCC 是什么？](https://blog.nowcoder.net/n/c84c5a30965a4a36a6d1ccac2a844885)
> - [多版本并发控制：什么是MVCC、快照读与当前读、MVCC实现原理之ReadView](https://icode.best/i/86846746058741)
> - [MySQL的多版本并发控制(MVCC)是什么？](https://segmentfault.com/a/1190000037557620)


