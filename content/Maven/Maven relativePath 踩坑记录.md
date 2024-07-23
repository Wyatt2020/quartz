---
created: 2022-06-02 17:57
modified: 2023-11-18 14:53
tags: [Maven]
---

点击 install 时，报错 `Non-resolvable parent POM for…`

![](Maven%20踩坑记录.png)

检查 pom 文件

![](Maven%20踩坑记录-1.png)

`relativePath` 默认从仓库里找 parent，不从本地找。

查找顺序：`relativePath` 节点的地址 –> 本地仓库 –> 远程仓库

> 解决办法
> -  指定父 pom 的路径
> -  删除此标签
