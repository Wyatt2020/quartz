---
created: 2023-06-29 10:48
tags:
  - docker
  - 消息队列
status: doing
---

## 单机

### 创建网络

```shell
docker network create kafka_net
```

### 安装 Zookeeper

```shell
docker run -d --name zookeeper \
--network kafka_net \
-e ALLOW_ANONYMOUS_LOGIN=yes \
bitnami/zookeeper:latest
```

如果已存在 Zookeeper 容器，则省略安装 Zookeeper 的步骤

将现有的 Zookeeper 加入 `kafka_net` 网络即可

```shell
docker network connect --alias zookeeper kafka_net zookeeper
```

### 启动 Kafka 容器

```shell
docker run --name kafka -p 9092:9092 --network kafka_net \
-e KAFKA_BROKER_ID=0  \
-e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181/kafka \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
-e ALLOW_PLAINTEXT_LISTENER=yes \
-e TZ="Asia/Shanghai" \
-d bitnami/kafka:2.7.0
```

参数含义

![[clipboard 21.png]]

在 zk 查看 Kafka 注册信息

![[clipboard 22.png]]

### 连接 Kafka 客户端

```shell
docker exec -it kafka /bin/sh
cd /opt/bitnami/kafka/bin
```

### 新建 Kafka 客户端容器

```shell
docker run -it --rm \
--network kafka_net \
-e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181 \
bitnami/kafka:2.7.0 kafka-topics.sh --list --bootstrap-server kafka:9092
```

> [!info] Reference
> - [kafka 2.8 首次脱离 Zookeeper 独立运行](https://www.confluent.io/blog/kafka-without-zookeeper-a-sneak-peek/)
> - [Apache Kafka 2.8 不再需要 ZooKeeper 就能运作](https://www.ithome.com.tw/news/143569)
> - [kafka-docker](https://developer.confluent.io/quickstart/kafka-docker/)
> - [docker-hub:bitnami/kafka](https://hub.docker.com/r/bitnami/kafka)
> - [使用 docker 安装 kafka(结合ZK)](https://blog.csdn.net/weixin_42854904/article/details/118438475)
> - [【Kafka精进系列003】Docker 环境下搭建 Kafka 集群](https://blog.csdn.net/noaman_wgs/article/details/103757791)
