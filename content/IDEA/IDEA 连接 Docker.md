---
created: 2022-05-31 00:27
tags:
  - IDEA
  - docker
---

> [!danger] 警告
> 这个功能慎用！原因是我开启了 Docker 远程访问以后，当天晚上服务器就被黑了。因为 Docker 完全暴露在了公网上，无需密码就可以操作 Docker，黑客就可以在你的 Docker 上部署病毒。所以建议配置好防火墙。

1.  开启 Docker 的远程访问

```shell
# 修改 Docker 服务文件，需要先切换到 root 用户
vim /lib/systemd/system/docker.service

# 注释掉"ExecStart"这一行，并添加下面这一行信息
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375
```

2.  重启 Docker

```shell
#重新加载配置文件
systemctl daemon-reload
#重启服务
systemctl restart docker.service
#查看配置的端口号（2375）是否开启（非必要）
netstat -nlpt # 如果找不到 netstat 命令，可以先安装一下这个工具
```

![](202205310031483.png)

3.  连接 docker
    如果没有这个配置，就去下载 Docker 插件
    ![](202205310032918.png)
    连接成功后，就可以使用服务器上的 Docker 了
    ![](202205310032132.png)

> [!link]- 参考链接
> [菜鸟的 IDEA 使用 Docker 插件](https://blog.csdn.net/qq_40298902/article/details/106543208) 
> 
> [IDEA 集成 Docker，IDEA 操作 Docker 的 build、push](https://blog.csdn.net/wangyue23com/article/details/110938283)
