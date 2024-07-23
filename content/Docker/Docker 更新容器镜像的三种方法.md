---
created: 2023-07-04 17:35 
modified: 2024-03-05 11:39
tags: [docker]
aliases: docker 升级
---

### 更新 docker 命令部署的容器

> 以 Portainer 为例

1. 查看容器名字

```shell
docker ps
```

![[Pasted image 20230704173934.png]]

2. 停止容器

```shell
docker stop portainer
```

3. 备份数据（重要）

利用 `docker inspect` 找出容器的映射到本地的文件路径

```shell
docker inspect portainer
```

![[Pasted image 20230704174311.png]]

备份数据

```shell
cp -r /var/lib/docker/volumes/bae8d4c14a6c9626e39d8b3adea04dbb4360e7a719bfdd8281122f89cf49b021/_data /var/lib/docker/volumes/bae8d4c14a6c9626e39d8b3adea04dbb4360e7a719bfdd8281122f89cf49b021/_data.backup
```

3. 拉取最新的容器镜像

```shell
docker pull portainer/portainer-ce:latest
```

4. 重新运行容器

```shell
docker run -d -p 9000:9000 \
--restart=always \
-v /var/run/docker.sock:/var/run/docker.sock \
--name portainer_2.18.3 \
portainer/portainer-ce:latest
```

### 更新 Docker-compose 部署的容器

1. 在 `docker-compose` 所在的文件夹下执行

```shell
docker-compose pull
```

2. 重启容器

```shell
docker-compose restart
```

3. 删除旧的镜像

```shell
docker image prune
```

### 利用 Portainer 更新容器

> 以 kafka 为例

1. 关闭容器
2. 在 `Container` 中点击容器名

![[Pasted image 20230704184317.png]]

3. 点击 `Recreate`

![[Pasted image 20230704184354.png]]

弹框勾选 `Re-pull image`

![[Pasted image 20230704184509.png|500]]

> [!info] Reference
> 
> - [Docker 更新容器镜像的三种方法](https://iwanlab.com/docker-container-update/)
> - [Docker 容器如何更新？如何迁移应用？如何卸载？](https://blog.laoda.de/archives/docker-issues)
