---
created: 2022-05-30 23:43
tags:
  - Git
draft: true
---

1. 生成 SSH Key

```shell
ssh-keygen -t rsa -C "mySshKey"
```

![[Pasted image 20240720181952.png]]

2. 在 `gitee/github/gitlab` 添加 SSH 公钥

	![[Pasted image 20240720182009.png]]

3. 在终端输入代码，添加主机到本机 SSH 可信列表，以 gitee 为例（此过程需要输入 yes 确认）

```shell
ssh -T git@gitee.com
```

![](202205302354934.png)
