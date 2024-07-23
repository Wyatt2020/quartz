---
created: 2023-12-03 11:49
modified: 2023-12-03 12:00
tags: [docker]
source: https://blog.csdn.net/wangshuai6707/article/details/132299930
---

## 前言

Docker run 命令是在 Docker 中创建和运行容器的主要命令之一。它允许根据需要配置容器的各种属性。 下面是 `docker run` 命令的一些常见用法和示例，我们共同学习。

### docker run 命令示例

以下是一个比较常见的 Docker run 命令示例，用于创建一个 NGINX 容器：

```shell
docker run -d \
  --name my_nginx \
  -p 80:80 \
  -v /path/to/nginx/conf:/etc/nginx/conf.d \
  -v /path/to/nginx/html:/usr/share/nginx/html \
  nginx:latest
```

-   `-d`: 在后台以守护进程模式运行容器。
-   `--name my_nginx`: 为容器指定一个名称（可以根据需要更改为您喜欢的名称）。
-   `-p 80:80`: 将主机的端口 80 映射到容器的端口 80。这样，您可以通过访问 `http://localhost` 来访问 NGINX 容器中的网站。
-   `-v /path/to/nginx/conf:/etc/nginx/conf.d`: 将主机上的 NGINX 配置文件目录挂载到容器中的 `/etc/nginx/conf.d` 目录，以便使用自定义的 NGINX 配置。
-   `-v /path/to/nginx/html:/usr/share/nginx/html`: 将主机上的 HTML 文件目录挂载到容器中的 `/usr/share/nginx/html` 目录，以便在容器中提供自定义的静态网页内容。
-   `nginx:latest`: 指定要使用的 NGINX 镜像及其标签（可以根据实际情况替换为您自己的镜像名称和标签）。  
    Docker run 命令是在 Docker 中创建和运行容器的主要命令之一。它允许您根据需要配置容器的各种属性。以下是 Docker run 命令的 30 个常用参数的详细解释和示例用法，帮助您更好地理解和使用这些参数。

1.  `-d` 或 `--detach`：以后台模式运行容器，将容器放置在后台运行，作为守护进程。  
    示例：`docker run -d image_name`
    
2.  `-it`：以交互模式运行容器，允许与容器进行交互。  
    示例：`docker run -it image_name`
    
3.  `--name`：为容器指定一个名称。  
    示例：`docker run --name container_name image_name`
    
4.  `-p`：将容器的端口映射到主机上的一个端口。  
    示例：`docker run -p host_port:container_port image_name`
    
5.  `-v`：挂载主机上的文件或目录到容器内部。  
    示例：`docker run -v host_path:container_path image_name`
    
6.  `-e`：设置容器的环境变量。  
    示例：`docker run -e ENV_VARIABLE=value image_name`
    
7.  `--restart`：指定容器在退出时的重启策略。  
    示例：`docker run --restart=always image_name`
    
8.  `--link`：将容器连接到另一个容器，在两个容器之间建立网络连接。  
    示例：`docker run --link container_name:image_alias image_name`
    
9.  `--dns`：指定容器使用的自定义 DNS 服务器。  
    示例：`docker run --dns 8.8.8.8 image_name`
    
10.  `--dns-search`：指定容器的 DNS 搜索域。  
    示例：`docker run --dns-search example.com image_name`
    
11.  `--cap-add` 和 `--cap-drop`：增加或删除容器的 Linux 能力，用于控制容器的权限。  
    示例：`docker run --cap-add=SYS_ADMIN image_name`
    
12.  `--privileged`：给容器赋予特权，可以访问主机的设备。  
    示例：`docker run --privileged image_name`
    
13.  `--tmpfs`：在容器内创建临时文件系统，用于存储临时数据。  
    示例：`docker run --tmpfs /tmp image_name`
    
14.  `--ulimit`：设置容器的资源限制，如最大打开文件数、最大进程数等。  
    示例：`docker run --ulimit nofile=1024:1024 image_name`
    
15.  `--security-opt`：设置容器的安全选项，如 AppArmor 配置、Seccomp 配置等。  
    示例：`docker run --security-opt seccomp:unconfined image_name`
    
16.  `--cpu-shares`：设置容器的 CPU 份额，用于控制 CPU 资源的分配。  
    示例：`docker run --cpu-shares 512 image_name`
    
17.  `--memory`：设置容器可使用的内存限制。  
    示例：`docker run --memory 1g image_name`
    
18.  `--network`：指定容器使用的网络模式。  
    示例：`docker run --network bridge image_name`
    
19.  `--hostname`：设置容器的主机名。  
    示例：`docker run --hostname my_container image_name`
    
20.  `--user`：指定容器运行时的用户名或 UID。  
    示例：`docker run --user username image_name`
    
21.  `--volume-driver`：指定容器使用的卷驱动程序。  
    示例：`docker run --volume-driver my_driver image_name`
    
22.  `--shm-size`：设置容器的共享内存大小。  
    示例：`docker run --shm-size 2g image_name`
    
23.  `--add-host`：向容器的 `/etc/hosts` 文件添加自定义主机名和 IP 映射。  
    示例：`docker run --add-host myhost:192.168.0.100 image_name`
    
24.  `--read-only`：将容器的文件系统设置为只读模式。  
    示例：`docker run --read-only image_name25.` –cpu-quota`：设置容器的 CPU 配额，以微秒为单位。 示例：`docker run --cpu-quota=50000 image_name`
    
25.  `--cpu-period`：设置容器的 CPU 周期，以微秒为单位。  
    示例：`docker run --cpu-period=100000 image_name`
    
26.  `--dns-option`：为容器的 DNS 配置添加自定义选项。  
    示例：`docker run --dns-option=timeout:5 image_name`
    
27.  `--sysctl`：设置容器的内核参数。  
    示例：`docker run --sysctl net.ipv4.ip_forward=1 image_name`
    
28.  `--label`：为容器添加标签，用于识别和组织容器。  
    示例：`docker run --label env=production image_name`
    
29.  `--workdir`：设置容器的工作目录。  
    示例：`docker run --workdir /app image_name`

## Docker run 多种用法

### 知其然知其所以然

Docker run 命令是用于创建和运行容器的主要命令之一。它可以根据提供的参数配置容器的各种属性。下面是 Docker run 命令的一些常见用法和示例，从入门到精通，帮助您更好地理解和使用该命令。

#### 1. 基本用法

最简单的 Docker run 命令形式如下：

```shell
docker run image_name
```

这将使用指定的镜像创建一个新的容器，并在容器中运行默认的启动命令。

#### 2. 启动交互式容器

使用 `-it` 参数可以启动一个交互式容器，允许您与容器进行交互，类似于在终端中打开一个新的会话：

```shell
docker run -it image_name
```

这将在容器内打开一个新的终端，并将您连接到容器的标准输入（stdin）。

#### 3. 映射端口

使用 `-p` 参数可以将容器的端口映射到主机上的一个端口，以便可以通过主机访问容器中运行的服务：

```shell
docker run -p host_port:container_port image_name
```

例如，将容器的端口 8080 映射到主机的端口 80：

```shell
docker run -p 80:8080 image_name
```

这样，您可以通过访问 `http://localhost` 来访问容器中的服务。

#### 4. 挂载文件 / 目录

使用 `-v` 参数可以将主机上的文件或目录挂载到容器内部，实现主机和容器之间的文件共享：

```shell
docker run -v host_path:container_path image_name
```

例如，将主机上的 `/path/to/host/directory` 目录挂载到容器的 `/path/to/container/directory` 目录：

```shell
docker run -v /path/to/host/directory:/path/to/container/directory image_name
```

这样，容器内部的操作将反映在主机上的相应目录中，实现了数据的共享和持久化。

#### 5. 设置环境变量

使用 `-e` 参数可以设置容器的环境变量：

```shell
docker run -e ENV_VARIABLE=value image_name
```

例如，设置一个名为 `DATABASE_URL` 的环境变量：

```shell
docker run -e DATABASE_URL=mysql://user:password@host:port/database image_name
```

这样，在容器内部可以通过读取该环境变量来配置应用程序。

#### 6. 指定容器名称

使用 `--name` 参数可以为容器指定一个名称：

```shell
docker run --name container_name image_name
```

例如：

```shell
docker run --name my_container image_name
```

这样，您可以使用容器名称来管理和操作容器，而不仅仅依赖于容器的 ID。

#### 7. 后台运行容器

使用 `-d` 参数可以将容器放置在后台运行，作为守护进程：

```shell
docker run -d image_name
```

#### 8. 重启策略

使用 `--restart` 参数可以指定容器在退出时的重启策略，常见的选项有 `no`、`on-failure[:max-retries]`、`always`、`unless-stopped`。

```shell
docker run --restart=always image_name
```

这将在容器退出时自动重新启动容器，确保容器的持续可用性。

#### 9. 其他参数

除了上面的常用的参数，其实还有一些参数也是比较有用的  
Docker run 还有许多其他参数可用于更高级的容器配置，如网络设置、安全选项、资源限制等

1.  `--restart`：指定容器在退出时的重启策略，可选值为 `no`、`on-failure[:max-retries]`、`always`、`unless-stopped`。  
    例如：`docker run --restart=always image_name`
    
2.  `--link`：将容器连接到另一个容器，在两个容器之间建立网络连接。  
    例如：`docker run --link container_name:image_alias image_name`
    
3.  `--dns`：指定容器使用的自定义 DNS 服务器。  
    例如：`docker run --dns 8.8.8.8 image_name`
    
4.  `--dns-search`：指定容器的 DNS 搜索域。  
    例如：`docker run --dns-search example.com image_name`
    
5.  `--cap-add` 和 `--cap-drop`：增加或删除容器的 Linux 能力，用于控制容器的权限。  
    例如：`docker run --cap-add=SYS_ADMIN image_name`
    
6.  `--privileged`：给容器赋予特权，可以访问主机的设备。  
    例如：`docker run --privileged image_name`
    
7.  `--tmpfs`：在容器内创建临时文件系统，用于存储临时数据。  
    例如：`docker run --tmpfs /tmp image_name`
    
8.  `--ulimit`：设置容器的资源限制，如最大打开文件数、最大进程数等。  
    例如：`docker run --ulimit nofile=1024:1024 image_name`
    
9.  `--security-opt`：设置容器的安全选项，如 AppArmor 配置、Seccomp 配置等。  
    例如：`docker run --security-opt seccomp:unconfined image_name`
    
10.  `--cpu-shares`：设置容器的 CPU 份额，用于控制 CPU 资源的分配。  
    例如：`docker run --cpu-shares 512 image_name`
    
11.  `--memory`：设置容器可使用的内存限制。  
    例如：`docker run --memory 1g image_name`

### 2. docker run 命令参数详解

`docker run` 命令是 Docker 中用于创建并运行容器的命令。它有许多参数，用于控制容器的运行方式、网络连接、环境变量等。下面是对一些常用参数的详细解释：

##### 1. `-d`：以后台模式（detached mode）运行容器，即在容器启动后将其放入后台运行。

```shell
docker run -d image_name
```

##### 2. `-it`：以交互模式（interactive mode）运行容器，即启动一个新的终端会话并连接到正在运行的容器。

```shell
docker run -it image_name
```

##### 3. `--name`：为容器指定一个名称。

```shell
docker run --name container_name image_name
```

##### 4. `-p`：将容器的端口映射到主机的端口。

```shell
docker run -p host_port:container_port image_name
```

这个参数允许你将容器内部的端口映射到主机上，以便可以通过主机的端口访问容器内的服务。

##### 5. `-v`：将主机的目录或文件挂载到容器中。

```shell
docker run -v host_path:container_path image_name
```

这个参数允许你将主机上的目录或文件挂载到容器中，以便在容器中使用或共享数据。

##### 6. `--network`：指定容器使用的网络类型。

```shell
docker run --network network_name image_name
```

这个参数允许你将容器连接到指定的网络，以便容器可以与其他容器或主机进行通信。

##### 7. `--env`：设置容器的环境变量。

```shell
docker run --env key=value image_name
```

这个参数允许你在容器中设置环境变量，以便容器内的应用程序可以使用这些变量。

##### 8. `--rm`：容器退出时自动删除容器。这在临时容器中非常有用。

```shell
docker run --rm image_name
```

##### 9. `--link`：将容器连接到另一个容器。

```shell
docker run --link container_name:image_alias image_name
```

这个参数允许你将一个容器连接到另一个容器，以便容器之间可以进行通信。

##### 10. `--privileged`：以特权模式运行容器，具有访问主机系统的权限。

```shell
docker run --privileged image_name
```

这个参数允许容器访问主机的设备，以便执行特权操作。

##### 11. `--volume-driver`：指定容器数据卷的驱动程序。

```shell
docker run --volume-driver driver_name -v volume_name:image_name
```

这个参数允许你指定一个自定义的数据卷驱动程序，以便在容器中使用数据卷。

##### 12. `--ulimit`：设置容器的资源限制。

```shell
docker run --ulimit resource_name=value image_name
```

这个参数允许你设置容器的资源限制，如最大打开文件数、最大进程数等。

##### 13. `--detach-keys`：指定分离容器的键盘序列。

```shell
docker run --detach-keys="ctrl-]" image_name
```

这个参数允许你自定义分离容器的键盘序列，用于在交互模式下分离容器。

##### 14. `--env-file`：从文件中读取环境变量。

```shell
docker run --env-file=file_path image_name
```

这个参数允许你从文件中读取环境变量，并将其传递给容器。

##### 15. `--hostname`：设置容器的主机名。

```shell
docker run --hostname=custom_hostname image_name
```

这个参数允许你为容器设置一个自定义的主机名。

##### 16. `--dns`：设置容器使用的 DNS 服务器。

```shell
docker run --dns=dns_server_ip image_name
```

这个参数允许你为容器设置使用特定的 DNS 服务器进行域名解析。

##### 17. `--shm-size`：设置容器的共享内存大小。

```shell
docker run --shm-size=2g image_name
```

这个参数允许你为容器设置共享内存的大小，以便容器可以使用更多的内存资源。

##### 18. `--link`：连接到其他容器。

```shell
docker run --link=container_name:image_alias image_name
```

这个参数允许你将一个容器连接到另一个容器，并为连接的容器指定一个别名。

##### 19. `--restart`：设置容器的重启策略。

```shell
docker run --restart=always image_name
```

这个参数允许你设置容器在退出时的重启策略，可以设置为 `no`、`on-failure` 或 `always`。

##### 20. `--cpu-shares`：设置容器的 CPU 份额。

```shell
docker run --cpu-shares=512 image_name
```

这个参数允许你为容器设置 CPU 的份额，用于控制容器在 CPU 资源竞争时的优先级。

##### 21. `--memory`：设置容器的内存限制。

```shell
docker run --memory=1g image_name
```

这个参数允许你为容器设置内存的限制，以确保容器不会占用过多的内存资源。

##### 22. `--cap-add` 和 `--cap-drop`：添加或删除容器的特权。

```shell
docker run --cap-add=SYS_ADMIN image_name
```

这些参数允许你为容器添加或删除特定的权限，用于控制容器的安全性。

##### 23. `--rm`：容器退出时自动删除容器。这在临时容器中非常有用。

```shell
docker run --rm image_name
```

##### 24. `--link`：将容器连接到另一个容器。

```shell
docker run --link container_name:image_alias image_name
```

这个参数允许你将一个容器连接到另一个容器，以便容器之间可以进行通信。

##### 25. `--privileged`：以特权模式运行容器，具有访问主机系统的权限。

```shell
docker run --privileged image_name
```

这个参数允许容器访问主机的设备，以便执行特权操作。

##### 26. `--volume-driver`：指定容器数据卷的驱动程序。

```shell
docker run --volume-driver driver_name -v volume_name:image_name
```

这个参数允许你指定一个自定义的数据卷驱动程序，以便在容器中使用数据卷。

##### 27. `--ulimit`：设置容器的资源限制。

```shell
docker run --ulimit resource_name=value image_name
```

这个参数允许你设置容器的资源限制，如最大打开文件数、最大进程数等。

## 参考文档

1. [Docker run 命令参考](https://www.runoob.com/docker/docker-run-command.html)：  
    Runoob 网站提供了一个简明的 Docker run 命令参考，解释了一些常用参数的含义和用法，并提供了示例。
2. [Docker run 命令文档](https://docs.docker.com/engine/reference/run/)：
    官方文档中关于 Docker run 命令的详细说明，包括所有参数的解释和示例用法。
3. [https://docker-curriculum.com/#docker-run](https://docker-curriculum.com/#docker-run)  
    这是一个国外的网站提供了一些具体的 Docker run 命令示例，涵盖了各种常见的用例和配置。
