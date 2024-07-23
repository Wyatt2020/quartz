---
created: 2022-05-31 00:26
modified: 2023-11-22 16:10
tags: [IDEA]
---

> [!tip] Intellij IDEA 出现如下提示
> Reduce the indexing time and CPU load with pre-built JDK shared indexes

IDEA 为了能够更快的使用共享索引，会下载 JDK 和 Maven 库的共享索引，给你的的项目代码构建共享索引。

1. 要关掉的也可以，File –> Settings –> Tools –> Shared Indexes，改成： `Ask before download`， 或者 `Don’t download, use local indexes`
2. 然后： File –> Invalidate Caches –> Clear downloaded shared indexes –> Invalidate and Restart
