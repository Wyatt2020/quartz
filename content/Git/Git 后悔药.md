---
created: 2022-05-30 23:57
modified: 2024-03-27 13:53
tags:
  - Git
aliases:
  - git reset
draft: true
---

1. `git log` 查看提交记录

	![[Pasted image 20240720182210.png]]

2. `git reset --soft [需要返回的 commitId]`
   比如想 `head` 指向“统一返回数据格式”，则输入：
   ```bash
	git reset --soft 582b6a4635f9c093945b08b69f61a9cefba7a647
	```

> [!tip]
> - **git reset --soft**
> 	- 用于回退到指定的提交，并保留回退后的修改在暂存区。
> - **git reset --mixed**
> 	- **默认**的模式，它会将指定提交之后的修改放入工作目录，但不会放入暂存区。
> - **git reset --hard**
> 	- 将指定提交之后的修改全部丢弃，包括工作目录和暂存区的修改，==慎用==，会永久删除未提交的所有更改。

> [!link]- Reference
> [Git提交代码错了吃后悔药的几种常用办法](https://mp.weixin.qq.com/s/4obQ14qLd7Lp724bP8KiVA)
