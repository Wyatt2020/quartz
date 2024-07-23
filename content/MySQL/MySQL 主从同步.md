---
created: 2023-11-16 16:54
modified: 2023-11-16 16:57
tags:
  - MySQL
---

> [!link]- Reference
> - [MySQL高可用之主从复制](https://mp.weixin.qq.com/s/yhAwaP9yynpLMFAfP5RCPA)
> - [MySQL主从复制(Master-Slave)实践](https://www.cnblogs.com/gl-developer/p/6170423.html)
> - [MySQL 主从复制操作步骤](https://blog.csdn.net/boywcx/article/details/115345492)
> - [【对线面试官】MySQL调优](https://mp.weixin.qq.com/s/3fVfZ4UWxYBTwEhuEKja0Q)
> - [在现有 MySQL 主从复制集群中增加数据库](https://www.jianshu.com/p/28b97d5c9c23)

> [!tip]
> MySQL 版本最好一致
> 同步之前，要保证两个数据库数据一致

| 主机名称         | IP 地址          | MySQL |
| ------------ | -------------- | ----- |
| mysql-master | 47.128.67.227  | 5.7   |
| mysql-slave  | 47.114.229.230 | 5.7   |

### 主库

#### 1.修改主库 my.cnf

```properties
[mysqld]
#定义二进制日志名称
log-bin=master-bin
#服务器唯一ID，一般取IP最后一段
server-id=227
#二进制日志格式
binlog-format=ROW
#指定复制的数据库(可选)
binlog-do-db=kkb2
#和replicate-ignore-db是一样的
binlog-ignore-db=kkb
#指定不复制的数据库(可选，mysql5.7版本及以上)
replicate-ignore-db=kkb
#指定忽略的表(可选，mysql5.7版本及以上)
replicate-ignore-table=db.table1
```

#### 2. 重启 MySQL 服务

#### 3. 授权从服务器登录主服务器

```sql
GRANT REPLICATION SLAVE ON *.* TO 'root'@'%' identified by 'root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' identified by 'root';
--刷新权限
FLUSH PRIVILEGES;
```

#### 4. 查看主库状态

```sql
show master status;
```

![[MySQL 主从同步.png]]

*File*：`binlog` 日志名称，只要数据库重启就会生成新的名称
*Position*：定位从库开始读的位置，也就是 1152 之前的 sql 数据是不会同步到从库中的，如果要同步之前的数据，使用 `mysql dump` 进行复制即可

### 从库

#### 1. 修改从库 my.cnf

```properties
[mysqld]
server-id=230  #服务器唯一ID，一般取IP最后一段
```

#### 2. 关闭从库

```sql
stop slave
```

#### 3. 初始化从库的状态

```sql
change master to
master_host='47.108.77.227',
master_port=3306,
master_user='root',
master_password='1232',
master_log_file='master-bin.000001',
master_log_pos=1152;
```

- 主库 IP 地址
- 主库端口
- 主库账号
- 主库密码
- 主库 binlog 日志名称
- 从库进行同步主库 binlog  日志开始的位置

#### 4. 启动从库

```sql
start slave
```

#### 5. 查看从库状态

```sql
show slave status \G
```

![[MySQL 主从同步-1.png]]

> [!attention] 遇到的坑
> 把从库的字段调整了顺序，导致同步报错。所以要保持主库和从库的表完全一致！
>  ![[MySQL 主从同步-2.png]]