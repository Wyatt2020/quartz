---
created: 2024-04-03 13:53
modified: 2024-04-03 13:55
tags: [docker]
---

找到已安装的 docker 模块

方式一

```shell
rpm -qa | grep docker
```

方式二

```shell
yum list installed | grep docker
```

删除模块

```shell
yum -y remove 模块名
```
