---
created: 2022-05-30 10:22
modified: 2023-11-06 13:51
tags:
  - Redis
---

## 简介

Redis 是完全开源免费的，遵守 BSD 协议，是一个高性能的 key-value 数据库。

Redis 与其他 key-value 缓存产品有以下三个特点：
- Redis 支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
- Redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。
- Redis 支持数据的备份，即 master-slave 模式的数据备份。

## 启动服务

启动 Redis 服务器

```bash
redis-server
```

连接本地 Redis

```bash
redis-cli
```

连接远程 Redis

```bash
redis-cli -h host -p port -a password
```

## 数据结构

常用：String、Hash、List、Set、SortedSet（zset）

高级：HyperLogLog、Geo、Pub/Sub、Redis Module、BloomFilter、RedisSerch、Redis-ML

## 命令

### [String](https://www.redis.net.cn/tutorial/3508.html)

**SET**：设置指定 key 的值

```bash
set key value
```

**GET**：获取指定 key 的值

```bash
get key
```

**DEL**：删除指定 key 的值

```bash
del key
```

**MSET**：同时设置一个或多个 key-value 对

```bash
MSET key value [key value ...]
```

**MSETNX**：同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。

```bash
MSETNX key value [key value ...]
```

**MGET**：获取所有(一个或多个)给定 key 的值

```bash
MGET key1 [key2..]
```

**STRLEN**：返回 key 所储存的字符串值的长度

```bash
strlen key
```

**APPEND**：将 value 追加到 key 原来的值的末尾，没有则新增

```bash
append key value
```

> **string 作为数值操作**
> - string 在 Redis 内部存储默认是一个字符串，当遇到增减类操作 incr，decr 时会转成数值型进行计算
> - **Redis 所有操作都是原子性的**，采用单线程处理所有业务，因此无需考虑并发带来的数据影响（也就是说 **Redis 是线程安全的**）
> - 如果原始数据不能转成数值，或超越了 Redis 数值的上限（Java 中的 long 最大值），将会报错

**INCR**：将 key 中储存的`数值+1`

```bash
incr key
```

> 应用
> - 生成分布式 ID
> - 热门微博点赞数
> - 大V粉丝数

**INCRBY**：将 key 所储存的值加上给定的增量值（increment）

```bash
incrby key [increment]
```

**INCRBYFLOAT**：将 key 所储存的值加上给定的浮点增量值（increment） 

```bash
incrbyfloat key [increment]
```

**DECR**：将 key 中储存的`数值-1`

```bash
decr key
```

> 应用
> - 取消点赞
> - 取消关注

**DECRBY**：key 所储存的值减去给定的减量值（decrement） 

```bash
decrby key [decrement]
```

**SETEX | PSETEX**：设置数据具有指定的生命周期

```bash
setex key seconds [value]
psetex key milliseconds [value]
```

> 应用
> - 单个微信号每 4 小时只能投 1 票
> - 短信验证码 5 分钟内有效
> - 热门商品维持 3 天，3 天后取消热门
> - 自动控制热点新闻的时效性

#### key 的命名规范

`表名:主键名:主键值:字段名`

#### string 的应用场景

- 将高热度数据缓存起来，后台设定定时刷新策略即可
- 把常用信息，字符串，图片或者视频等信息放到 Redis 中，Redis 作为缓存层，MySQL 做持久化层，降低 MySQL 的读写压力
- Spring Session + Redis 实现 Session 共享

### [Hash](https://www.redis.net.cn/tutorial/3509.html)

#### hash 的应用场景

能直观，相比 String 更节省空间地维护缓存信息，如用户信息，视频信息等

### [List](https://www.redis.net.cn/tutorial/3510.html)

#### list 的应用场景

- 微博 TimeLine: 有人发布微博，用 lpush 加入时间轴，展示新的列表信息
- lpush + lpop = Stack（栈） 
- lpush + rpop = Queue（队列） 
- lpush + ltrim = Capped Collection（有限集合） 
- lpush + brpop = Message Queue（消息队列）

### [Set](https://www.redis.net.cn/tutorial/3511.html)

#### set 的应用场景

- 标签（tag），给用户添加标签，或者用户给消息添加标签，这样有同一标签或者类似标签的可以给推荐关注的事或者关注的人
- 点赞、点踩、收藏等，可以放到 set 中实现

### [Zset](https://www.redis.net.cn/tutorial/3512.html)

#### zset 的应用场景

排行榜：有序集合经典使用场景。例如小说视频等网站需要对用户上传的小说视频做排行榜，榜单可以按照用户关注数、更新时间、字数等打分，做排行

## 配置参数

> [官方 Redis 配置参数说明](https://www.redis.net.cn/tutorial/3504.html)

```bash
# 查看配置 
config get [配置项] 
# 修改配置（重启服务后失效） 
config set [配置项]
```

### 常用配置

> [Redis 配置文件 redis.conf 详细配置说明](https://www.jb51.net/article/135837.htm)

```shell
# 端口
port 6379
# 密码
requirepass 123456
# 当客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能（单位：秒）
timeout 0

# protected-mode 是3.2 之后加入的新特性
# 为了禁止公网访问redis cache，加强redis安全的
# 它启用的条件有两个：1.没有bind IP; 2.没有设置访问密码
# protected-mode no

############### 限制 ###############
maxclients 128 # 设置最大连接数，0为不限制
maxmemory <bytes> # 内存清理策略，如果达到此值，将采取以下动作：
# volatile-lru ：只对设置过期时间的key进行LRU算法删除
# allkeys-lru ：删除不经常使用的key
# volatile-random ：随机删除即将过期的key
# allkeys-random ：随机删除一个key
# volatile-ttl ：删除即将过期的key
# noeviction ：不过期，写操作返回报错
maxmemory-policy volatile-lru#如果达到maxmemory值，采用此策略
maxmemory-samples 5  #默认随机选择5个key，从中淘汰最不经常用的

############### 快照方式 ###############
save 3600 1    # 在3600s（1h）之后，至少有1个key发生变化，则快照
save 300 100  # 在300s（5m）之后，至少有100个key发生变化，则快照
save 60 10000 # 在60s（1m）之后，至少有1000个key发生变化，则快照
rdbcompression yes  # dump时是否压缩数据
dir /var/lib/redis  # 数据库（dump.rdb）文件存放目录
```

## 系统信息

### 主从连接状态

```bash
info replication
```

### 内存使用情况

> [info memory 参数详解](https://blog.csdn.net/b1303110335/article/details/106037816)

```bash
info memory
```

### cpu 信息

```bash
info cpu
```

### server 信息

```bash
info server
```

> [!link]  Reference
> - [Redis教程](https://www.redis.net.cn/tutorial/3505.html)
> - [Redis【入门】就这一篇!](https://www.wmyskxz.com/2018/05/31/redis-ru-men-jiu-zhe-yi-pian/)
> - [Redis专辑 - 敖丙](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=MzAwNDA2OTM1Ng==&action=getalbum&album_id=1323328139262754819&subscene=24&scenenote=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzAwNDA2OTM1Ng%3D%3D%26mid%3D2453140867%26idx%3D2%26sn%3D5c573be40ff3e897bed2ede542ef8c34%26chksm%3D8cf2d700bb855e166f6c8db7d89185b26139ace92ef70484084ce2abd9a6c9e66eb0f84b9695%26mpshare%3D1%26scene%3D24%26srcid%3D%26sharer_sharetime%3D1589377337673%26sharer_shareid%3D8cb32907e73938a856072848bc2de62b%26ascene%3D14%26devicetype%3Dandroid-29%26version%3D27001139%26nettype%3DWIFI%26abtest_cookie%3DAAACAA%253D%253D%26lang%3Dzh_CN%26exportkey%3DA6tw6EvfmsCC5drPHxxqKBk%253D%26pass_ticket%3D36ahSPzN7ow6BXi4ykFmmmYSbNQ3ivY1iXCHv%252BGItmvrYovqGsI0r3FyiGx3Iav8%26wx_header%3D1&devicetype=android-29&version=27001139&lang=zh_CN&nettype=WIFI&ascene=0&pass_ticket=36ahSPzN7ow6BXi4ykFmmmYSbNQ3ivY1iXCHv%2BGItmvrYovqGsI0r3FyiGx3Iav8&wx_header=1)
> - [Redis - 狂乱的贵公子](https://www.cnblogs.com/cjsblog/category/1155233.html)
> - [Redis - 那啥快看](https://www.cnblogs.com/shamo89/tag/redis/default.html?page=2)
> - [Redis 快速入门 通俗易懂](https://blog.csdn.net/haduwi/article/details/109477037?spm=1001.2014.3001.5501)
> - [为你自己学 Redis](https://redis.sai.show/)
