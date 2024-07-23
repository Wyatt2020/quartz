---
created: 2023-11-10 17:18
modified: 2023-11-10 17:26
tags:
  - docker
  - MySQL
  - Redis
---

> [!link]- Reference
> [docker 安装 mysql & redis](https://www.cnblogs.com/songjilong/p/12613167.html)
> 
> [docker 安装 nginx](https://blog.csdn.net/weixin_39393393/article/details/116456303?spm=1001.2014.3001.5501)
> 
> [docker 安装 mysql 和 redis 镜像(无配置文件版)](https://blog.csdn.net/boywcx/article/details/108903209)
> 
> [docker 安装 mysql5.7 并且配置 my.conf](https://blog.csdn.net/boywcx/article/details/117782448)
> 
> [docker 安装 redis 6.0.6 并且配置 redis.conf](https://blog.csdn.net/boywcx/article/details/117850797)
> 
> [值得收藏！my.cnf配置文档详解](https://www.cnblogs.com/zhangweizhong/p/12179438.html)
> 
> [MySQL my.cnf配置文件详解](http://c.biancheng.net/view/7618.html)
> 
> [my.cnf 配置大全](https://www.cnblogs.com/xiaohanhan1991/p/10057354.html)
> 
> [mysql 登录时报 socket 找不到终极解决方案](https://riclee.blog.csdn.net/article/details/82349641?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)
> 
> [chmod 777 到底是啥 ???看完这个你就完全懂了！](https://blog.csdn.net/rjszz1314/article/details/104399333?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-9.baidujs&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-9.baidujs)

### MySQL

#### 指定配置启动

##### my.cnf

```properties
# MySQL 5.7 my.cnf设置
[client]
port = 3306
socket=/etc/mysql/mysql.sock
 
[mysqld]
###############################基础设置#####################################
 
# MySQL 服务的唯一编号 每个 MySQL 服务ID 需唯一
server-id = 1
 
#服务端口号 默认3306
port = 3306
# MySQL 安装根目录
basedir = /etc/mysql
 
# MySQL 数据文件所在位置
datadir = /etc/mysql/data
 
#临时目录 比如load data infile会用到 
# tmpdir = /etc/mysql/tmp

#设置socket文件所在目录 
socket=/etc/mysql/mysql.sock
#设置语言包，默认的会报错
language = /usr/share/mysql/english
 
 
#数据库默认字符集,主流字符集支持一些特殊表情符号（特殊表情符占用4个字节）
character-set-server = utf8mb4
 
#数据库字符集对应一些排序等规则，注意要和character-set-server对应
collation-server = utf8mb4_general_ci
 
#设置 Client 连接 MySQL 时的字符集，防止乱码
init_connect='SET NAMES utf8mb4'
 
#是否对 SQL 语句大小写敏感，1表示不敏感
lower_case_table_names = 1
 
#最大连接数
max_connections = 400
#最大错误连接数
max_connect_errors = 1000
 
# MySQL 连接闲置超过一定时间后(单位:秒)将会被强行关闭
# MySQL 默认的 wait_timeout 值为8个小时,interactive_timeout参数需要同时配置才能生效
interactive_timeout = 1800
wait_timeout = 1800

#数据库错误日志文件
log_error = error.log
```

##### 启动

```shell
docker run -p 3306:3306  --name mysql --restart=unless-stopped --privileged=true \
-v /opt/mysql/etc/mysql:/etc/mysql \
-v /opt/mysql/etc/my.cnf:/etc/my.cnf \
-v /opt/mysql/opt/mysql:/opt/mysql \
-e MYSQL_ROOT_PASSWORD=R@DTx*m4DNUF7bmE \
-d mysql:5.7
```

启动参数
- --privileged=true
	- 使 container 内的 root 拥有真正的 root 权限

启动成功以后，给容器设置目录权限

```shell
docker exec -it mysql bash
ls -l
chmod 777 /etc/mysql
chmod 777 /opt/mysql
chown mysql /etc/mysql/tmp
```

#### 无配置启动

下载 MySQL 镜像

```shell
docker pull mysql:5.7
```

启动 MySQL 容器

```shell
docker run -p 3306:3306 --name mysql \
-v /mydata/mysql/log:/var/log/mysql \
-v /mydata/mysql/data:/var/lib/mysql \
-v /mydata/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7
```

进入 MySQL 容器

```shell
docker exec -it mysql /bin/bash
```

### Redis

下载 Redis 最新镜像

```shell
docker pull redis
```

创建配置文件

```shell
sudo mkdir -p /usr/local/redis
```

```shell
sudo touch /usr/local/redis/redis.conf
```

添加密码

```shell
sudo vi /usr/local/redis/redis.conf
```

#### 密码

```shell
requirepass [密码]
```

启动 redis 容器

```shell
docker run -p 6379:6379 --name redis --sysctl net.core.somaxconn=1024 \
-v /usr/local/redis/data:/data \
-v /usr/local/redis:/etc/redis \
-e TIME_ZONE="Asia/Shanghai" -e TZ="Asia/Shanghai" \
-d redis redis-server /etc/redis/redis.conf
```

启动参数
- `--sysctl`
	- 用于设置容器的内核参数
- `net.core.somaxconn=1024`
	- [net.core.somaxconn](https://www.cnblogs.com/xzongblogs/p/15206020.html) 是 Linux 内核参数之一，用于设置套接字监听队列的最大长度。默认值通常较小，而将其增加到较大的值可以提高服务器的并发性能，特别是在处理大量并发连接时。
- `/data`
	- 备份文件 (dump.rdb) 的路径，可以使用 `config get dir` 查看
- `redis. conf`
	- 配置文件
- `--requirepass 123456`
	- 设置密码

> [!attention]
> 如果在 redis.conf 指定了 dir 目录，举个栗子：`dir /usr/local/redis/data`。并且启动容器命令映射的是 `/data`，那么容器会在 `/data` 和 `/usr/local/redis/data` 生成 `dump.rdb`，使用 `config get dir` 显示的是 `dir /usr/local/redis/data`。目前不清楚这样做会有什么问题，所以根据情况看要不要设置 dir 吧。

启动客户端

```shell
docker exec -it redis redis-cli
```

登录

```shell
auth [密码]
```

---

### my. cnf 详细版

```properties
# MySQL 5.7 my.cnf 设置
[client]
port = 3306
socket = /etc/mysql/tmp/mysql.sock
 
[mysqld]
###############################基础设置#####################################
 
# MySQL 服务的唯一编号 每个 MySQL 服务 ID 需唯一
server-id = 1
 
#服务端口号 默认3306
port = 3306
# MySQL 安装根目录
basedir = /etc/mysql
 
# MySQL 数据文件所在位置
datadir = /etc/mysql/data
 
#临时目录 比如load data infile会用到 
tmpdir  = /etc/mysql/tmp
 
#设置socket文件所在目录 
socket  = /etc/mysql/tmp/mysql.sock
#设置语言包，默认的会报错
language = /usr/share/mysql/english
 
#主要用于MyISAM存储引擎,如果多台服务器连接一个数据库则建议注释下面内容
#skip-external-locking
 
#只能用IP地址检查客户端的登录，不用主机名
skip_name_resolve = 1
#数据库默认字符集,主流字符集支持一些特殊表情符号（特殊表情符占用4个字节）
character-set-server = utf8mb4
 
#数据库字符集对应一些排序等规则，注意要和character-set-server对应
collation-server = utf8mb4_general_ci
 
#设置 Client 连接 MySQL 时的字符集,防止乱码
init_connect='SET NAMES utf8mb4'
 
#是否对 SQL 语句大小写敏感，1表示不敏感
lower_case_table_names = 1
 
#最大连接数
max_connections = 400
#最大错误连接数
max_connect_errors = 1000
 
#TIMESTAMP如果没有显示声明NOT NULL，允许NULL值
explicit_defaults_for_timestamp = true
 
#SQL数据包发送的大小，如果有BLOB对象建议修改成1G
max_allowed_packet = 128M
 
# MySQL 连接闲置超过一定时间后(单位：秒)将会被强行关闭
# MySQL 默认的 wait_timeout 值为8个小时,interactive_timeout参数需要同时配置才能生效
interactive_timeout = 1800
wait_timeout = 1800
 
#内部内存临时表的最大值 ，设置成128M。
#比如大数据量的group by ,order by时可能用到临时表，
#超过了这个值将写入磁盘，系统IO压力增大
tmp_table_size = 134217728
max_heap_table_size = 134217728
 
 
##----------------------------用户进程分配到的内存设置BEGIN-----------------------------##
##每个session将会分配参数设置的内存大小
#用于表的顺序扫描，读出的数据暂存于read_buffer_size中，当buff满时或读完，将数据返回上层调用者
#一般在128kb ~ 256kb,用于MyISAM
#read_buffer_size = 131072
#用于表的随机读取，当按照一个非索引字段排序读取时会用到，
#一般在128kb ~ 256kb,用于MyISAM
#read_rnd_buffer_size = 262144
#order by或group by时用到
#建议先调整为2M，后期观察调整
sort_buffer_size = 2097152
#一般数据库中没什么大的事务，设成1~2M，默认32kb
binlog_cache_size = 524288
 
############################日志设置############################
#数据库错误日志文件
log_error = error.log
 
#慢查询 SQL 日志设置
slow_query_log = 1
slow_query_log_file = slow.log
#检查未使用到索引的 SQL
log_queries_not_using_indexes = 1
#针对log_queries_not_using_indexes开启后，记录慢 SQL 的频次、每分钟记录的条数
log_throttle_queries_not_using_indexes = 5
#作为从库时生效,从库复制中如何有慢 SQL 也将被记录
log_slow_slave_statements = 1
#慢查询执行的秒数，必须达到此值可被记录
long_query_time = 2
#检索的行数必须达到此值才可被记为慢查询
min_examined_row_limit = 100
 
# MySQL binlog 日志文件保存的过期时间，过期后自动删除
expire_logs_days = 5
 
############################主从复制 设置########################################
#开启 MySQL binlog 功能
log-bin=mysql-bin
# binlog 记录内容的方式，记录被操作的每一行
binlog_format = ROW
#作为从库时生效,想进行级联复制，则需要此参数
log_slave_updates
 
#作为从库时生效,中继日志 relay-log 可以自我修复
relay_log_recovery = 1
 
#作为从库时生效,主从复制时忽略的错误
slave_skip_errors = ddl_exist_errors
 
##---redo log和binlog的关系设置BEGIN---##
#(步骤1) prepare dml相关的SQL操作，然后将redo log buff中的缓存持久化到磁盘
#(步骤2)如果前面prepare成功，那么再继续将事务日志持久化到binlog
#(步骤3)如果前面成功，那么在redo log里面写上一个commit记录
#当innodb_flush_log_at_trx_commit和sync_binlog都为1时是最安全的，
#在mysqld服务崩溃或者服务器主机crash的情况下，binary log只有可能丢失最多一个语句或者一个事务。
#但是都设置为1时会导致频繁的io操作，因此该模式也是最慢的一种方式。
#当innodb_flush_log_at_trx_commit设置为0，mysqld进程的崩溃会导致上一秒钟所有事务数据的丢失。
#当innodb_flush_log_at_trx_commit设置为2，只有在操作系统崩溃或者系统掉电的情况下，上一秒钟所有事务数据才可能丢失。
#commit事务时,控制redo log buff持久化磁盘的模式 默认为1
innodb_flush_log_at_trx_commit = 2
#commit事务时,控制写入mysql binlog日志的模式 默认为 0
#innodb_flush_log_at_trx_commit和sync_binlog都为1时，mysql最为安全但性能上压力也是最大
sync_binlog = 1
##---redo log 和 binlog的关系设置END---##
 
############################Innodb设置############################
#数据块的单位8k，默认是16k，16kCPU压力稍小，8k对select的吞吐量大
#innodb_page_size的参数值也影响最大索引长度，8k比16k的最大索引长度小
#innodb_page_size = 8192
#一般设置物理存储的60% ~ 70%
innodb_buffer_pool_size = 1G
 
#5.7.6之后默认16M
#innodb_log_buffer_size = 16777216
#该参数针对unix、linux，window上直接注释该参数.默认值为NULL
#O_DIRECT减少操作系统级别VFS的缓存和Innodb本身的buffer缓存之间的冲突
innodb_flush_method = O_DIRECT
 
#此格式支持压缩, 5.7.7之后为默认值
innodb_file_format = Barracuda
 
#CPU多核处理能力设置，假设CPU是2颗4核的，设置如下
#读多，写少可以设成2:6的比例
#innodb_write_io_threads = 4
#innodb_read_io_threads = 4
 
#提高刷新脏页数量和合并插入数量，改善磁盘I/O处理能力
#默认值200（单位：页）
#可根据磁盘近期的IOPS确定该值
innodb_io_capacity = 500
 
#为了获取被锁定的资源最大等待时间，默认50秒，超过该时间会报如下错误:
# ERROR 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
innodb_lock_wait_timeout = 30
 
#调整 buffer pool 中最近使用的页读取并 dump 的百分比,通过设置该参数可以减少转储的 page 数
innodb_buffer_pool_dump_pct = 40
 
#设置 redoLog 文件所在目录，redoLog 记录事务具体操作内容
innodb_log_group_home_dir = /opt/mysql/redolog/
 
#设置 undoLog 文件所在目录，undoLog 用于事务回滚操作
innodb_undo_directory = /opt/mysql/undolog/
 
#在 innodb_log_group_home_dir 中的 redoLog 文件数，redoLog 文件内容是循环覆盖写入。
innodb_log_files_in_group = 3
 
# MySQL 5.7官方建议尽量设置的大些，可以接近innodb_buffer_pool_size的大小
#之前设置该值较大时可能导致 MySQL 宕机恢复时间过长，现在恢复已经加快很多了
#该值减少脏数据刷新到磁盘的频次
#最大值 innodb_log_file_size * innodb_log_files_in_group <= 512GB,单文件<=256GB
innodb_log_file_size = 1024M
 
#设置 undoLog 文件所占空间可以回收
#5.7之前的 MySQL 的 undoLog 文件一直增大无法回收
innodb_undo_log_truncate = 1
innodb_undo_tablespaces = 3
innodb_undo_logs = 128
 
#5.7.7默认开启该参数 控制单列索引长度最大达到3072
#innodb_large_prefix = 1
 
#5.7.8默认为4个, Inodb 后台清理工作的线程数
#innodb_purge_threads = 4
 
#通过设置配置参数 innodb_thread_concurrency 来限制并发线程的数量，
#一旦执行线程的数量达到这个限制，额外的线程在被放置到对队列中之前，会睡眠数微秒，
#可以通过设定参数 innodb_thread_sleep_delay 来配置睡眠时间
#该值默认为0,在官方doc上，对于 innodb_thread_concurrency 的使用，也给出了一些建议:
#(1)如果一个工作负载中，并发用户线程的数量小于64，建议设置innodb_thread_concurrency=0；
#(2)如果工作负载一直较为严重甚至偶尔达到顶峰，建议先设置innodb_thread_concurrency=128,
###并通过不断的降到 innodb_thread_concurrency = 0
############################其他内容 设置##########################################
[mysqldump]
quick
max_allowed_packet = 128M
[mysql]
no-auto-rehash
[myisamchk]
key_buffer_size = 20M
sort_buffer_size = 256k
read_buffer = 2M
write_buffer = 2M
[mysqlhotcopy]
interactive-timeout
[mysqld_safe]
#增加每个进程的可打开文件数量.
open-files-limit = 28192

```
