---
created: 2022-05-26 16:24
modified: 2024-04-24 10:05
tags: [Spring]
aliases:
  - AopContext.currentProxy() 解决同类中调用嵌套方法 AOP 失效
---

在同一个类中，非事务方法 A 调用事务方法 B，会导致事务失效，可以采用 `AopContext.currentProxy().xxxxx()` 来保证事务生效。

> [!link] 参考链接
> [AopContext.currentProxy()解决同类中调用嵌套方法AOP失效](https://www.jianshu.com/p/28a29070f530)
