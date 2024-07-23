---
created: 2023-06-26 10:47
modified: 2023-08-27 23:14
tags: Zookeeper
related: "[[Zookeeper]]"
---

## 什么是 Zookeeper

Zookeeper 是一个分布式协调框架，是 Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。

简单来说 Zookeeper = 文件系统 + 监听通知机制

## 安装 Zookeeper

1. [下载](https://zookeeper.apache.org/releases.html)并解压

```shell
cd /usr/local

wget https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.6.3/apache-zookeeper-3.6.3-bin.tar.gz

tar xvf apache-zookeeper-3.6.3-bin.tar.gz
```

2. 复制一份配置文件，并重命名为 `zoo`

```shell
cp conf/zoo_sample.cfg conf/zoo.cfg
```

3. 在 `bin` 目录下启动 zk 服务器

```shell
./zkServer.sh start
```

4. 在 `bin` 目录下运行客户端

```shell
./zkCli.sh
```

### 基本操作

- 查看当前 zk 中包含的内容

```shell
ls /
```

- 新建一个 `znode`

```shell
create /zk_test myData
```

![[clipboard.png]]

- 获取节点的值

```shell
get /zk_test
```

- 设置节点的值

```shell
get /zk_test 123
```

- 删除节点

```shell
delete /zk_test
```

## 客户端常用命令

### 连接其他的 ZooKeeper 服务

```shell
connect host:port
```

### 列表路径下的资源

```shell
ls /path
```

### 创建持久化节点

```shell
create /path data
```

### 创建持久化顺序节点

```shell
create -s /path data
```

### 创建临时节点

```shell
create -e /path data
```

### 创建临时顺序节点

```shell
create -e -s /path data
```

### 查看节点状态

```shell
stat /test
```

> [!note]
>
> ```shell
> cZxid = 0xd # 创建节点时的事务 ID
> ctime = Fri Jan 07 09:32:40 CST 2022 # 节点被创建的时间
> mZxid = 0x1f # 当前节点携带数据最后一次修改的事务 ID。
> mtime = Fri Jan 07 09:35:30 CST 2022 # 节点最后一次被修改的时间
> pZxid = 0x21 # 子节点列表最后一次修改的事务 ID。
> cversion = 1 # 节点版本号，当节点的子节点列表发生变化时，版本变更
> dataVersion = 2 # 数据版本号，当节点携带数据发生变化时，版本变更
> aclVersion = 0 # 节点最后一次被修改的时间
> ephemeralOwner = 0x0 # 如果此节点为临时节点，那么它的值为这个节点拥有者的会话ID；否则，它的值为0x0
> dataLength = 3 # 节点携带数据长度
> numChildren = 1 # 子节点数量
> ```

### 查看指定节点的数据

```shell
get /path
```

### 设置指定节点的数据

```shell
set /path data
```

### 删除指定节点

此命令不能删除有子节点的节点

```shell
delete /path
```

### 删除指定结点，包括子节点

旧版

```shell
rmr /path
```

新版

```shell
deleteall /path
```

### 退出控制台

```shell
quit
```

> [!info] Reference
>
> - [Zookeeper 入门看这篇就够了](https://my.oschina.net/u/3796575/blog/1845035)
> - [Zookeeper 快速入门](https://www.cnblogs.com/niechen/p/8597344.html)
> - [3y 写的 ZooKeeper 入门](https://mp.weixin.qq.com/s?__biz=MzI4Njg5MDA5NA==&mid=2247485115&idx=1&sn=5d269f40f820c82b460993669ca6242e&chksm=ebd747badca0ceac9953f82e08b1d1a49498ebd4af77ec5d628a0682bb9f0ac5ab347411f654&token=1741918942&lang=zh_CN#rd)
> - [讲解 Zookeeper 的五个核心知识点](https://mp.weixin.qq.com/s/W6QgmFTpXQ8EL-dVvLWsyg)
> - 命令
>   - [Zookeeper 常用命令行操作](https://zhanglianghhh.github.io/2019/12/24/zookeeper03/)
>   - [Zookeeper 的常见命令](https://zhuanlan.zhihu.com/p/69872600)
> - 工具
> 	- [Zookeeper 图形化的客户端工具：ZooInspector](https://www.cnblogs.com/cag2050/p/10278267.html)
