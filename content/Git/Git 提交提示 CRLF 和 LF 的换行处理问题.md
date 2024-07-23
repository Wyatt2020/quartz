---
created: 2022-05-31 00:08
tags:
  - Git
---

```shell
git config --global core.autocrlf true
```

`core.autocrlf` 是 git 中负责处理 line-endings 的变量

- 设置为 true，添加文件到 git 仓库时，git 将其视为文本文件，它将把 CRLF 变成 LF（Windows）
- 设置为 false 时，line-endings 将不做转换操作，文本文件保持原来的样子
- 设置为 input 时，添加文件 git 仓库时，git 把 CRLF 变成 LF（Mac & Linux，在 Windows 系统不要使用这个设置）

> [!link]- Reference
> - [Git 提交提示 CRLF 和 LF 的换行处理问题](https://blog.csdn.net/qn20126816/article/details/79309496)

