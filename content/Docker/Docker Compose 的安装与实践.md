---
created: 2021-12-14 16:02 
modified: 2023-11-09 16:51
tags:
  - docker 
---

> [!link] 
> [DaoCloud | 镜像市场](https://hub.daocloud.io/)
> 
> [如何在 Ubuntu 22.04 LTS 中安装 Docker 和 Docker Compose](https://linux.cn/article-14871-1.html)

### yum 安装 

#### 安装 epel 源

```shell
yum install -y epel-release
```

#### 安装 docker-compose

```shell
yum install -y docker-compose
```

### pip 安装

```shell
pip install docker-compose
```

### ubuntu 安装

`v2.23` 可以换成[最新版 Docker Compose](https://github.com/docker/compose/releases)

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

如果 ubuntu 无法连接 github，可以在 Windows 下载完再上传到 ubuntu

> [!link]
> ```
>  https://github.com/docker/compose/releases/download/v2.23.0/docker-compose-Linux-x86_64
> ```

最后，使用下列命令赋予二进制文件可执行权限：

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

运行下列命令检查安装的 Docker Compose 版本：

```shell
docker-compose version
```

### 启动容器

```shell
docker-compose up -d
```

### 重启容器

```shell
docker restart nginx
# 或者
docker-compose restart
```

### 关闭并删除容器

```shell
docker-compose down
```

### 进入容器终端

```shell
docker exec -it nginx bash
```

### 查看实时日志

```shell
docker-compose logs -f
```

### tomcat

- `always` 总是重启
- `unless-stopped` 在容器主进程挂掉之后自动重启，除了显式 `docker stop` 容器

```yaml
version: '3.1' # docker-compose 版本
services:
  tomcat:
    restart: always # docker 启动时自启
    image: daocloud.io/library/tomcat:8.5.15-jre8 # 镜像地址
    container_name: tomcat # 容器名称
    ports: # 端口映射
      - 8080:8080
    environment: # 环境变量
      TZ: Asia/Shanghai
    volumes: # 目录挂载
      - /opt/tomcat/webapps:/usr/local/tomcat/webapps
      - /opt/tomcat/logs:/usr/local/tomcat/logs
```

### mysql

```yaml
version: '3.1' # docker-compose 版本
services:
  mysql:
    restart: always # docker 启动时自启
    image: daocloud.io/library/mysql:5.7.4 # 镜像地址
    container_name: mysql57 # 容器名称
    ports: # 端口映射
      - 3306:3306
    environment: # 环境变量
      MYSQL_ROOT_PASSWORD: 123456
      TZ: Asia/Shanghai
    volumes: # 目录挂载
      - /opt/docker_mysql/log:/var/log/mysql
      - /opt/docker_mysql/data:/var/lib/mysql
      - /opt/docker_mysql/conf:/etc/mysql
```

> [!note]
> 
> - [mysql 查看当前使用的配置文件 my.cnf 的方法](https://blog.csdn.net/fdipzone/article/details/52705507)
> - 如果 `conf` 目录没有 `my.cnf`（配置文件），进入到 mysql 容器内部，将 `/usr/local/my.cnf` 复制到 `/etc/mysql`，或者直接在挂载目录 `conf` 新建一个

### redis

```yaml
version: '3.1'
services:
  redis:
    restart: always
    image: daocloud.io/library/redis:6.0.6
    container_name: redis
    ports:
      - 6379:6379
    environment:
      TZ: Asia/Shanghai
    volumes:
      - /opt/redis/redis.conf:/usr/local/redis/redis.conf
    command: ["redis-server","/usr/local/redis/redis.conf"]
```

#### redis.conf

```conf
requirepass 密码
```
