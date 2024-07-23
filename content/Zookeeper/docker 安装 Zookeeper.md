---
created: 2023-06-26 11:21 
modified: 2023-08-27 23:15
tags: Zookeeper docker
related: "[[Zookeeper]]"
---

> [!attention] 注意
> 所有版本都存在**未授权访问漏洞**，因此安装 Zookeeper，需要设置指定 ip 可以访问

### 拉取镜像

```shell
sudo docker pull zookeeper:3.6.3
```

### 单机

```shell
docker run -d --name zookeeper --restart always -e JVMFLAGS="-Xmx1024m" -p 2181:2181 zookeeper:3.6.3
```

### 加映射

```shell
docker run --name zookeeper --restart always -e JVMFLAGS="-Xmx1024m" -p 2181:2181 \
-e TZ="Asia/Shanghai" \
-v /home/study/zookeeper/zk-single/conf/zoo.cfg:/conf/zoo.cfg \
-v /home/study/zookeeper/zk-single/data:/data \
-v /home/study/zookeeper/zk-single/datalog:/datalog \
-d zookeeper:3.6.3
```

> 如果要映射整个 conf 目录，需要新增以下文件

![[clipboard 2.png]]

zoo.cfg

```ini
# Zookeeper服务器心跳时间，单位为ms
tickTime=2000
# 允许follower连接并同步到leader的初始化连接时间，以tickTime的倍数来表示
initLimit=10
# leader与follower心跳检测最大容忍时间，响应超过syncLimit*tickTime，leader认为
# follower“死掉”，从服务器列表中删除follower
syncLimit=5
# 数据目录
dataDir=/data
# 日志目录
dataLogDir=/datalog
# ZooKeeper对外服务端口
clientPort=2181
# 审核日志(默认false)
audit.enable=true
# 客户端最大连接数
maxClientCnxns=100
```

## 集群

### 单机集群

#### docker-compose.yml

```yaml
version: "3"
services:
  zk1:
    image: zookeeper:3.6.3
    container_name: zk1
    networks:
      - zk_net
    environment:
      TZ: "Asia/Shanghai"
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=zk1:2888:3888;2181 server.2=zk2:2888:3888;2181 server.3=zk3:2888:3888;2181
    volumes:
      - ./zk-2181/data:/data
      - ./zk-2181/datalog:/datalog
    expose:
      - 2181

  zk2:
    image: zookeeper:3.6.3
    container_name: zk2
    networks:
      - zk_net
    environment:
      TZ: "Asia/Shanghai"
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zk1:2888:3888;2181 server.2=zk2:2888:3888;2181 server.3=zk3:2888:3888;2181
    volumes:
      - ./zk-2182/data:/data
      - ./zk-2182/datalog:/datalog
    expose:
      - 2181

  zk3:
    image: zookeeper:3.6.3
    container_name: zk3
    networks:
      - zk_net
    environment:
      TZ: "Asia/Shanghai"
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zk1:2888:3888;2181 server.2=zk2:2888:3888;2181 server.3=zk3:2888:3888;2181
    volumes:
      - ./zk-2183/data:/data
      - ./zk-2183/datalog:/datalog
    expose:
      - 2181

networks:
  zk_net:
    external:
      name: zk_net
```

`ZOO_MY_ID`

- 表示当前 zookeeper 实例在 zookeeper 集群中的编号，范围为 1-255，所以一个 zk 集群最多有 255 个节点
- 编号越大在 Leader 选择算法中的权重越大，比如初始化启动时就是根据服务器 ID 进行比较

`ZOO_SERVERS`

- `server.1=zk1:2888:3888;2181`
- 表示当前 zookeeper 实例所在 zookeeper 集群中的所有节点的编号（myid）、主机名（或 IP 地址）、服务器与集群中的 leader 服务器交换信息的端口、选举时服务器相互通信的端口、对 client 端提供服务的端口

进入容器内部，执行命令，查看 zk 状态

```shell
bin/zkServer.sh status
```

![[clipboard 3.png]]

节点的四种状态：
1. LOOKING：寻 找 Leader 状态。当服务器处于该状态时会认为当前集群中没有 Leader，因此需要进入 Leader 选举状态。
2. FOLLOWING：跟随者状态。处理客户端的非事务请求，转发事务请求给 Leader 服务器，参与事务请求 Proposal(提议) 的投票，参与 Leader 选举投票。
3. LEADING：领导者状态。事务请求的唯一调度和处理者，保证集群事务处理的顺序性，集群内部个服务器的调度者(管理 follower,数据同步)。
4. OBSERVING：观察者状态。3.0 版本以后引入的一个服务器角色，在不影响集群事务处理能力的基础上提升集群的非事务处理能力，处理客户端的非事务请求，转发事务请求给 Leader 服务器，不参与任何形式的投票。

### 多机集群

> [!info] Reference
>
> - [docker compose 搭建 Zookeeper 集群](https://zhuanlan.zhihu.com/p/121728783)
> - [Zookeeper 未授权访问漏洞](https://blog.csdn.net/Aaron_Miller/article/details/106049421)
