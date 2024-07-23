---
created: 2021-02-02 10:03
modified: 2024-04-27 11:34
tags:
  - 工具
---

> [!tool]
> [Nginx 可视化配置](https://www.digitalocean.com/community/tools/nginx?global.app.lang=zhCN)
> 
> [开源地址](https://github.com/digitalocean/nginxconfig.io)

### 正向代理

1. 正向代理服务是==在客户端==设立的
    
2. 在浏览器中配置代理服务器，通过代理服务器进行互联网访问
    
3. 帮助咱们实现突破访问权限，提高访问的速度，对目标服务器==隐藏客户端的 IP 地址==

![[Nginx 学习.png]]

### 反向代理

1. 反向代理服务器是配置==在服务端==的
    
2. 反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址
    
3. 反向代理服务器去选择目标服务器获取数据后，再返回给客户端
    
4. 达到负载均衡，并且可以==隐藏服务器真正的 IP 地址==

![[Nginx 学习-1.png]]

### 环境准备

#### 使用 Docker 部署 Nginx

在 `/opt` 新建 `/nginx` 目录，再新建 `docker-compose.yml` 文件

```yaml
version: '3.1'  
services:   
  nginx:  
    restart: always  
    image: daocloud.io/library/nginx:latest # 拉取镜像地址  
    container_name: nginx # 容器名称  
    ports:   
      - 80:80  
    volumes:  
     - /opt/nginx/conf.d/:/etc/nginx/conf.d # 目录挂载  
     - /opt/nginx/log/:/var/log/nginx
```

**启动 Nginx 容器**

```sh
docker-compose up -d
```

**重启容器**

```sh
docker restart nginx
```

或者

```sh
docker-compose restart
```

**关闭并删除容器**

```sh
docker-compose down
```

**进入容器终端**

```sh
docker exec -it nginx bash
```

### 使用 Docker 部署 Tomcat 8

在 `/opt` 新建 `/tomcat8` 目录，再新建 `docker-compose.yml` 文件

```yaml
version: '3.1' # docker-compose 版本  
services:  
  tomcat:  
    restart: always # docker 启动时自启  
    image: daocloud.io/library/tomcat:8.5.15-jre8 # 镜像地址  
    container_name: tomcat8 # 容器名称  
    ports: # 端口映射  
      - 8080:8080  
    environment: # 环境变量  
      TZ: Asia/Shanghai  
    volumes: # 目录挂载  
      - /opt/tomcat8/tomcat_webapps:/usr/local/tomcat/webapps  
      - /opt/tomcat8/tomcat_logs:/usr/local/tomcat/logs
```

### Nginx 反向代理

编辑 `./conf.d/default.conf`

```nginx
server {  
    listen  80;  
    listen  [::]:80;  
    server_name  localhost;  
​  
    # 通过反向代理访问 tomcat 8080 端口  
    location / {  
        proxy_pass http://192.168.56.10:8080/;  
    }  
​  
    # 默认地址  
    #location / {  
    #    root   /usr/share/nginx/html;  
    #    index  index.html index.htm;  
    #}  
}
```

### Nginx 负载均衡

如果请求数过大，单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器的情况改为请求分发到多个服务器上，就是负载均衡。

![[Nginx 学习-2.png]]

**负载均衡策略**

1. **轮询**： 将客户端发起的请求，平均分配给每一台服务器（**默认**）
    
2. **权重**： 会将客户端的请求，根据服务器的权重值不同，分配不同的数量。
    
3. **ip_hash**: 基于发起请求的客户端的 ip 地址不同，他始终会将请求发送到指定的服务器上。就是说如果这个客户端的请求的 ip 地址不变，那么处理请求的服务器将一直是同一个。

轮询的 `default.conf`

```nginx
# upstream 写在 server 块外  
upstream my-server{  
    server 47.108.77.227:8080;  
    server 47.110.239.230:8080;  
}  
​  
server {  
    listen       80;  
    listen  [::]:80;  
    server_name  localhost;  
​  
    location / {  
        proxy_pass http://my-server/;  
    }  
}
```

### Nginx 动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，降低原来单个服务器的压力。这里指的就是让动态程序（Java、PHP）去访问应用服务器，让缓存、图片、JS、CSS 等去访问 Nginx。

Nginx 的并发能力公式： `worker_processes * worker_connections / 4|2 =` Nginx 最终的并发能力动态资源需要/4，静态资源需要/2 , Nginx 通过动静分离来提升 Nginx 的并发能力，更快的给用户响应。

### Nginx 集群

为了防止单点故障（单台 Nginx 挂了），需要搭建 Nginx 集群。

准备多台 Nginx， 准备 keepalived ，监听 nginx 的健康情况 ；准备 haproxy，提供一个虚拟的路径，统一的去接收用户的请求。当有一台 nginx 服务器出现故障，keepalived 检测到了故障后，会及时通知 haproxy 切换到正常 nginx 服务器，保证程序不受影响。

![[Nginx 学习-3.png]]

> [!tip]
> 不要直接改线上环境的配置，可以平时写在代码库里，通过 git 管理，部署的时候通过命令 copy 到对应位置。

> [!link]- Reference
> [神器 Nginx 详细笔记及集群搭建](https://blog.csdn.net/haduwi/article/details/115273263?spm=1001.2014.3001.5501)
> 
> [【千锋】Nginx教程全面讲解（Nginx快速上手）](https://www.bilibili.com/video/BV1W54y1z7GM)
> 
> [nginx location配置详细解释](https://www.cnblogs.com/jpfss/p/10418150.html)
> 
> [Nginx 入门1](https://mp.weixin.qq.com/s/qpafmRq_3UKqkDFQfsxsww)
> 
> [Nginx 入门2](https://mp.weixin.qq.com/s/QO7Mf2kpy2x2-fFvzgy3_w)
> 
> [Nginx 常用命令与配置 - Java Note](https://www.bookstack.cn/read/sdky-java-note/9568ff77f2054d89.md)

