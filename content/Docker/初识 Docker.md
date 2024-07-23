---
created: 2021-12-21 15:14 
modified: 2023-11-09 16:17
tags:
  - docker
aliases: docker 基础命令
---

> [!link] 
> [dockerhub](https://hub.docker.com/search?type=image)
> 
> [DaoCloud | 镜像市场](https://hub.daocloud.io/)
> 
> [尚硅谷 docker 教程](https://www.bilibili.com/video/BV1Ls411n7mx?p=1)
> 
> [Centos 安装 docker 官方文档](https://docs.docker.com/engine/install/centos/)

### docker 架构图

![[初识 docker.png]]

### 安装

#### Centos 6

安装 epel 库

```shell
yum install -y epel-release
```

安装 docker

```shell
yum install -y docker-io
```

配置文件的路径

```shell
/etc/sysconfig/docker
```

查看版本

```shell
docker version
```

启动 docker 服务

```shell
service docker start
```

#### Centos 7

1. 卸载旧版本（ \表示换行，一个一个卸载即可）

```shell
sudo yum remove docker \
			  docker-client \
			  docker-client-latest \
			  docker-common \
			  docker-latest \
			  docker-latest-logrotate \
			  docker-logrotate \
			  docker-engine
```

2. Install using the repository

```shell
sudo yum install -y yum-utils
# 阿里云镜像
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# 官方镜像
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

3.1 安装最新版

```shell
sudo yum install -y docker-ce docker-ce-cli containerd.io
```

3.2 安装其他版本

```shell
yum list docker-ce --showduplicates | sort -r
sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

4. 启动 docker

```shell
sudo systemctl start docker
```

5. hello world

```shell
sudo docker run hello-world
```

6. 允许 docker 开机自启

```shell
systemctl enable docker
```

#### Ubuntu

> [!tldr] Document 
> [【官方】Ubuntu 安装 docker](https://docs.docker.com/engine/install/ubuntu/)
> 
> [最详细的 ubuntu 安装 docker 教程](https://zhuanlan.zhihu.com/p/651148141)

1. 卸载所有冲突软件包

```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

2. 卸载已安装的 docker

```shell
sudo apt-get remove docker docker-engine docker-ce docker.io
sudo apt-get update
```

3. apt-get 可以使用 https 库

```shell
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
```

4. 添加 docker 的使用的公钥

```shell
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

5. 添加 docker 的远程库

```shell
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update
```

6. 安装 docker 相关软件包

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

7. [[初识 Docker#镜像加速|配置镜像加速]]

8. 验证是否安装成功

```shell
sudo docker run hello-world
```

9. 将当前用户加入 docker 用户组

默认情况下，只有 root 用户和 docker 组的用户才能运行 Docker 命令。我们可以将当前用户添加到 docker 组，以避免每次使用 Docker 时都需要使用 sudo。

```shell
sudo usermod -aG docker $USER
```

##### 更新/安装特定版本

1. 列出可用版本

```shell
apt-cache madison docker-ce | awk '{ print $3 }'
```

2. 选中需要的版本

```shell
VERSION_STRING=5:24.0.0-1~ubuntu.22.04~jammy

sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-buildx-plugin docker-compose-plugin
```

##### 卸载

卸载 Docker Engine、CLI、containerd 和 Docker Compose 包

```shell
sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-ce-rootless-extras
```

主机上的映像、容器、卷或自定义配置文件不会自动删除。要删除所有映像、容器和卷，请执行以下操作：

```shell
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

#### 镜像加速

如果 docker 启动报错，试试修改 `/etc/docker/daemon.json`

> [!error] 
> Job for docker.service failed because the control process exited with error

> [!tip] 
> [阿里云镜像加速](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

![[初识 docker-1.png]]

```shell
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://3m1kni5g.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```

腾讯云镜像加速

```shell
{
  "registry-mirrors": ["https://mirror.ccs.tencentyun.com"]
}
```

查看帮助文档

```shell
docker --help
```

#### 日志大小

![[限制 Docker 容器日志大小#daemon.json 中限制|seamless]]

### 镜像常用命令

#### 查看本地所有镜像

- `-a` 列出本地所有的镜像
- `-q` 只显示镜像 ID
- `-qa` 列出本地所有镜像的 ID
- `--digests` 显示镜像的摘要信息
- `--no-trunc` 显示镜像的完整信息

```shell
docker images
```

#### 查询镜像

```shell
docker search
```

查询收藏数大于 30 的 tomcat 镜像

```shell
docker search -f stars=30 tomcat
```

#### 下载/更新镜像

```shell
docker pull tomcat:版本号
```

> 不写版本号，等价于 `docker pull tomcat:latest` 下载最新版

#### 删除镜像（唯一镜像名或镜像 ID）

```shell
docker rmi hello-world
```

强制删除

```shell
docker rmi -f hello-world
```

> 删除多个镜像用空格隔开

删除没有任何容器使用的所有镜像

```shell
docker image prune
```

删除全部镜像

```shell
docker rmi -f $(docker images -qa)
```

#### 给镜像打 tag

```sh
docker tag nginx nginx:test
```

#### 导出镜像

```sh
docker save nginx >/tmp/nginx.tar.gz
```

#### 导入镜像

对于无法访问外网的请情况下会经常使用这种方法导出镜像，然后使用 load 命令导入镜像

```sh
docker load </tmp/nginx.tar.gz
```

### 容器常用命令

#### 新建并启动容器（启动交互式容器；前台运行）

- `-i` 以交互模式运行容器，通常与 `-t` 同时使用
- `-t` 为容器重新分配一个伪输入终端

```shell
docker run -it centos
```

#### 新建并启动容器，并给容器取别名

```shell
docker run -it --name mycentos centos
```

#### 查看正在运行的容器

- `-l` 查看上一个容器
- `-n 2` 查看最近两个容器
- `-q` 只显示容器 ID
- `--no-trunc` 不截断输出
- `-a` 查看全部容器

```shell
docker ps
```

#### 启动容器

```shell
docker start 容器ID
```

#### 重启容器

```shell
docker restart 容器ID
```

#### 正常停止容器

```shell
docker stop 容器ID
```

#### 强制停止容器

```shell
docker kill 容器ID
```

#### 删除已停止的容器

```shell
docker rm 容器ID
```

#### 停止并删除容器

```shell
docker rm -f 容器ID
```

#### 强制删除所有容器

```shell
docker rm -f ${docker ps -a -q}
# 或
docker ps -a -q | xargs docker rm
```

#### 停止容器并退出伪输入终端

```shell
exit
```

#### 不停止容器退出伪输入终端，容器后台运行

```shell
ctrl + P + Q
```

#### 启动守护式容器（后台运行）

```shell
docker run -d 容器名
```

#### 容器重命名

```shell
docker rename 原容器名称 新容器名称
```

#### 查看容器日志

- `-f` 跟随最新的日志打印
- `-t` 显示日志时间
- `--tail 3` 查看最新的 3 条日志

```shell
docker logs -ft --tail 3 容器 ID
```

#### 查看容器内运行的进程

```shell
docker top 容器 ID
```

#### 查看容器内部细节

```shell
docker inspect 容器 ID
```

#### 进入正在运行的容器，并以命令行交互

```shell
docker attach 容器 ID
```

#### 在容器外面执行命令，并获取结果

```shell
docker exec -t 容器 ID bashShell
```

> bashShell 是 linux 命令

#### 从容器内拷贝文件到主机上

```shell
docker cp 容器id:/容器内路径 目的主机路径
```

#### 查看所有镜像的资源占用情况

```shell
docker stats $(docker ps -qa)
```
