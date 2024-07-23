---
created: 2022-01-06 22:59
modified: 2024-04-28 10:26
tags: [docker]
---

### 网络驱动

- Bridge Driver
- Host Driver
- Overlay Driver
- MacLan Driver
- None Driver

### docker 容器互联的三种方式

- `docker0`
- `--link`
- 自定义 bridge 网络

### docker0

docker 会默认创建一个内部的桥接网络 `docker0`，每创建一个容器分配一个虚拟网卡，容器之间可以根据 IP 互相访问。

### --link

要让一个容器连接到另外一个容器，我们可以在容器通过 `docker create` 或 `docker run` 创建时通过 `--link` 选项进行配置。

启动容器，WebApp 连接到 MySQL

```shell
docker run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql

docker run -d --name webapp --link mysql webapp:latest
```

如果在 Web 应用中使用的是 JDBC 进行数据库连接的，我们可以这么填写连接，docker 会将其指向 MySQL 容器的 IP 地址。

```shell
String url = "jdbc:mysql://mysql:3306/webapp";
```

> 3306 是容器内部端口

#### 通过别名连接

```shell
docker run -d --name webapp --link mysql:database webapp:latest
```

使用 `--link <name>:<alias>` 的形式，连接到 MySQL 容器，并设置它的别名为 `database`。当我们要在 Web 应用中使用 MySQL 连接时，我们就可以使用 `database` 来代替连接地址了。

```shell
String url = "jdbc:mysql://database:3306/webapp";
```

#### 暴露端口

虽然容器间的网络打通了，但并不意味着我们可以任意访问被连接容器中的任何服务。docker 为容器网络增加了一套安全机制，只有容器自身允许的端口，才能被其他容器所访问。

端口的暴露可以通过 docker 镜像进行定义，也可以在容器创建时进行定义。在容器创建时进行定义的方法是借助 `--expose` 这个选项。

```shell
docker run -d --name mysql --expose 13306 --expose 23306 -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql:5.7
```

容器暴露了端口只是类似我们打开了容器的防火墙，具体能不能通过这个端口访问容器中的服务，还需要容器中的应用监听并处理来自这个端口的请求。

### 自定义 bridge 网络

#### docker network

- `docker network --help` 查看使用文档
- `connect` 将某个容器连接到一个 docker 网络
- `create` 创建一个 docker 局域网络
- `disconnect` 将某个容器退出某个局域网络
- `inspect` 显示某个局域网络信息
- `ls` 显示所有 docker 局域网络
- `prune` 删除所有未引用的 docker 局域网络
- `rm` 删除 docker 网络

#### 创建网络

```shell
docker network create -d bridge mynet
```

通过 `-d` 参数我们可以为新的网络指定驱动的类型，其值可以是刚才我们所提及的 `bridge`、`host`、`overlay`、`maclan`、`none`，也可以是其他网络驱动插件所定义的类型。这里我们使用的是 Bridge Driver ( 当我们不指定网络驱动时，docker 也会**默认**采用 Bridge Driver 作为网络驱动 )

#### 查看 docker 中已经存在的网络

```shell
docker network ls | docker network list
```

#### 指定容器所加入的网络

##### 容器启动时指定

```shell
docker run -d --name mysql --network mynet --network-alias mysql -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql:5.7
```

- `--network` 指定使用的网络

- `--network-alias` 给容器设置网络别名

- 所有加入到 `mynet` 网络的容器，都可以互联

- 若访问容器中服务，可以使用 `<网络别名>:<容器内部端口>` 的方式访问

- Web 容器的配置也可以使用别名

##### 已运行的容器加入网络

```shell
docker network connect --alias redis mynet redis
```

> `--alias redis` 可选

#### 将容器移除局域网络

```shell
network disconnect mynet redis
```

#### 查看容器网络

```shell
docker inspect mysql
```

#### 查看网络包含的容器

```shell
docker network inspect mynet
```

#### 查看未使用的网络

```shell
docker network ls --filter "dangling=true" --format "table {{.Name}}\t{{.Driver}}"
```

- `--filter "dangling=true"`：这个选项用来过滤出未连接到任何容器的网络，也就是所谓的“悬挂”（dangling）网络。
- `--format "table {{.Name}}\t{{.Driver}}"`：这将输出结果以表格形式显示，列出了网络名称和驱动类型。你可以根据需要调整输出格式。

#### 删除网络

```shell
docker network rm [network_id]
```

### 端口映射

通过 docker 端口映射功能，我们可以把容器的端口映射到宿主操作系统的端口上，当我们从外部访问宿主操作系统的端口时，数据请求就会自动发送给与之关联的容器端口。

```shell
docker run -d --name nginx -p 80:80 -p 443:443 nginx:1.12
```

> 端口映射选项的格式是 `-p <ip>:<host-port>:<container-port>`
> 
> 其中 IP 是宿主操作系统的监听 IP，可以用来控制监听的网卡，默认为 `0.0.0.0`，也就是监听所有网卡。

> [!link] Reference
> [Docker 容器互访三种方式](https://www.cnblogs.com/ExMan/p/12987656.html)
> 
> [Docker 容器之间通讯 -network](https://blog.csdn.net/cjbfzxz/article/details/106652867)
> 
> [Docker network 详解、教程](https://blog.csdn.net/wangyue23com/article/details/111172076)
