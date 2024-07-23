---
created: 2021-07-11 17:28 
tags: docker 工具
---

1. 查询当前有哪些 Portainer 镜像

```shell
sudo docker search portainer
```

![[clipboard 25.png]]

2. 拉取 stars 数最高的镜像

>[!tip]
> `portainer/portainer` 已弃用，所以选择 `portainer/portainer-ce`

```shell
sudo docker pull portainer/portainer-ce
```

3. 启动容器

```shell
sudo docker run -d -p 9000:9000 \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
--name portainer \
portainer/portainer-ce
```

4. 访问 `http://localhost:9000`
5. 首次登录需要给 admin 用户设置密码
   ![[clipboard 26.png]]
6. 单机模式选择 `local`
   ![[clipboard 27.png]]
> [!attention] 注意
> 在启动时必须指定该挂载文件，不能改路径
> 
   > ![[clipboard 28.png]]

   > [!link] Reference
   >
   > - [Docker 使用 Portainer 搭建可视化界面](https://www.cnblogs.com/ExMan/p/11657069.html)
   > - [Releases · portainer/portainer](https://github.com/portainer/portainer/releases)
