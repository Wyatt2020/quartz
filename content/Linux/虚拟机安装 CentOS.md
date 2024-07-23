---
created: 2020-11-15 00:11
modified: 2024-05-07 15:18
tags: [Linux, Windows]
---

> [!link] Reference
> [下载镜像](http://isoredirect.centos.org/centos/7/isos/x86_64/) | [7.6 镜像](https://archive.kernel.org/centos-vault/7.6.1810/isos/x86_64/) | [阿里云镜像](http://mirrors.aliyun.com/centos/7.9.2009/isos/x86_64/)
> 
> [安装教程](https://blog.csdn.net/weixin_41976398/article/details/85758032) | [VirtualBox安装CentOS Linux](https://www.qikegu.com/uncategorized/1195) | [vagrant 安装 centos](https://blog.csdn.net/weixin_39393393/article/details/115964175?spm=1001.2014.3001.5502)
> 
> [阿里云 CentOS 安装 java 环境](https://blog.csdn.net/qq_32365919/article/details/81866974)
> 
> [Xshell官方免费版](https://www.netsarang.com/zh/free-for-home-school/) | [XShell的安装和使用](https://www.jianshu.com/p/4716cc35750f)

### 配置 yum 源

```shell
# 备份原yum源
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 使用新yum源
#curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
# 生成缓存
yum makecache
```

### 更新 yum

```shell
yum -y update
```

如果报错 `could not retrieve mirrorlist`，说明网络没配置好，点此解决

### [安装 vim](https://jingyan.baidu.com/article/046a7b3efd165bf9c27fa915.html)

```shell
set nu                                                                                     
set tabstop=4
set cursorline
set ruler
set autoindent
```

### 安装 wget

```shell
yum -y install wget
```

### 安装 jdk

查看 jdk 版本

```shell
yum search java|grep jdk
```

安装

```shell
yum -y install java-1.8.0-openjdk
```

查看是否安装成功

```shell
java -version
```

查看 jdk 默认安装目录

```shell
find / -name 'java'
```

卸载 jdk

```shell
yum -y remove java-1.8.0-openjdk*
```

上面的步骤安装的 jdk 没有 `javac` 和 `jar` 命令，所以**推荐**下面的方法

```shell
yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
```

### 安装 tomcat

[官网地址](http://tomcat.apache.org/)
复制 tar 版本下载地址

![[虚拟机安装 CentOS.png]]

**新建一个 java 目录**

```shell
cd /usr
mkdir java
cd java
```

**安装 tomcat**

```shell
#下载
wget https://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.59/bin/apache-tomcat-8.5.59.tar.gz
#解压
tar -xzvf apache-tomcat-8.5.59.tar.gz
#重命名
mv apache-tomcat-8.5.59 tomcat8
```

**启动 tomcat**
- 切换到 tomcat 的 bin 目录，执行命令

```shell
./startup.sh   #启动
./shutdown.sh    #关闭
```

切换到 tomcat 的 logs 目录下执行**查看日志**

```shell
tail catalina.out
```

如果启动没有错误信息，说明启动配置成功

**查看 tomcat 版本**

在 `tomcat` 目录下执行命令

```java
./bin/version.sh
```

**查看 `8080` 端口是否开启**

```shell
netstat -ntlp
```

**关闭防火墙**

```shell
systemctl stop firewalld    # 关闭防火墙
systemctl status firewalld  # 查看状态
systemctl disable firewalld    # 开机自动关闭
```

浏览器输入 http://ip:8080 就可以访问了

**查看 IP** 

```shell
ip addr / ifconfig
```

**查看 CPU 使用情况**

```shell
top
```

**查看内存使用情况**

```shell
free -m
```

**查看硬盘使用情况**

```shell
df -h
```

### 安装 MySQL

[参考](https://blog.csdn.net/qq_32365919/article/details/81941066) [参考二](https://www.cnblogs.com/louby/p/10207310.html)

[官方 yum 源](https://dev.mysql.com/downloads/repo/yum/)

**启动服务**

```java
systemctl start mysqld
```

**查看服务状态**

```java
systemctl status mysqld
```

**重启服务**

```java
systemctl restart mysqld
```

**关闭服务**

```shell
systemctl stop mysqld
```

### 安装 Redis

[Linux安装部署Redis(超级详细)](https://www.cnblogs.com/hunanzp/p/12304622.html)

[linux上安装卸载redis的两种方法](https://blog.csdn.net/zz_i80/article/details/82775972?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&dist_request_id=&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control)

[Linux中Redis的安装和卸载](https://blog.csdn.net/a_liuge/article/details/79758135)

如果在阿里云下安装，需要开放 6379 端口

![[虚拟机安装 CentOS-1.png]]

将 `redis/bin` 里面的 `redis-server` 和 `redis-cli` 复制到 `/usr/local/bin` 下面，就可以在任意路径下启动它们

```shell
cd /usr/local/redis/bin
cp redis-server /usr/local/bin
```

**启动 redis**

`redis-server` 的位置 & `redis. conf` 的位置

```shell
/usr/local/redis-server 
/usr/local/redis/redis.conf
```

**查看 redis 启动状态**

```shell
ps -ef | grep redis
```

**关闭 redis**

- 启动 `redis-cli`

- `shutdown`

- `exit`

安装 `redis-cli`（如果安装了完整的 redis ，redis-cli 在 `redis/src` 目录下）

```shell
wget http://download.redis.io/redis-stable.tar.gz（下载redis-cli的压缩包）
tar xvzf redis-stable.tar.gz（解压）
cd redis-stable（进入redis-stable目录）
make（安装）
sudo cp src/redis-cli /usr/local/bin/（将redis-cli拷贝到bin下，让redis-cli指令可以在任意目录下直接使用）
```

**redis-cli 连接 redis-server**

连接到主机 IP 为 `127.0.0.1`，端口号为 `6379` ，密码为 `mypass` 的 redis 服务上。

```shell
redis-cli -h 127.0.0.1 -p 6379 -a "mypass"
```

**修改密码**

- 启动 `redis-cli`

- `auth passwod` (认证：没有密码则忽略这一步)

- `config set requirepass password` (password 是要设置的密码)
