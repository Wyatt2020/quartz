---
created: 2022-05-30 23:06
modified: 2023-08-27 23:34
tags:
  - MySQL
---

> [!download]- 
> - [Download MySQL Community Server (Archived Versions)](https://downloads.mysql.com/archives/community/)
> - GUI
> 	- [Beekeeper Studio 社区版](https://github.com/beekeeper-studio/beekeeper-studio) | Navicat 平替（小心律师函）

### MySQL 8

> [!link] Reference
> [MySQL 8.0 安装配置](https://blog.csdn.net/m0_52559040/article/details/121843945)

### MySQL 5.7 解压版

1. 先停止运行的 MySQL，用管理员权限打开 cmd，MySQL 的名字在 `services.msc` 里看

```shell
net stop mysql80    #停止
net start mysql80   #启动
```

2. 修改 `my.ini` 配置，如果没有就手动新建一个
   ![[MySQL 安装.png]]

```properties
[mysqld]
# 设置端口（MySQL 的默认端口为3306）
port = 3307

# 设置 MySQL 的安装目录
basedir=D:\Program Files\mysql-5.7.30-winx64

# 设置 MySQL 数据库的数据的存放目录
datadir=D:\Program Files\mysql-5.7.30-winx64\data

# 允许最大连接数
max_connections=100

# 允许连接失败的次数。
max_connect_errors=10

# 设置 MySQL 服务器使用的字符集，默认为 utf8
character-set-server=utf8mb4

# MySQL 服务器使用 utf8 通用的比较规则，ci 表示不区分大小写
collation_server=utf8mb4_general_ci

# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB

explicit_defaults_for_timestamp=true

# sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

# 时区
default-time_zone='+8:00'

[mysql]
# 设置 MySQL 客户端默认字符集
default-character-set=utf8mb4

[client]
# 设置 MySQL 客户端连接服务端时默认使用的端口
port=3307
default-character-set=utf8mb4
```

3. 以管理员身份打开 cmd ，切换到 MySQL 的 `bin` 目录下，安装 MySQL，将服务命名为 mysql57

```shell
mysqld install MySQL57 --defaults-file="D:\Program Files\mysql-5.7.30-winx64\my.ini"
```

![[MySQL 安装-1.png]]

4. 在 `bin` 目录下执行 `mysqld` 命令，初始化数据库，红框的是随机密码

```shell
mysqld --initialize --console
```

![[MySQL 安装-2.png]]

5. 运行 `regedit` 打开注册表，找到`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\mysql57`，确认 `ImagePath` 参数无误

```shell
"D:\Program Files\mysql-5.7.30-winx64\bin\mysqld" --defaults-file="D:\Program Files\mysql-5.7.30-winx64\my.ini" MySQL57
```

![[MySQL 安装-3.png]]

6. 启动服务
![[MySQL 安装-4.png]]

7. 用初始化的临时密码登录，如果忘记了，可以在 `data/xxx.err` 文件中找

```shell
mysql -uroot -p
```

8. 修改密码

```shell
ALTER USER 'root'@'localhost' IDENTIFIED BY '12345';
```

![[MySQL 安装-5.png]]

9. 允许使用用户名 root 密码 12345 从任何主机连接到 MySQL 服务器

```shell
use mysql;
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '12345' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

![[MySQL 安装-6.png]]

> 如果想**卸载重装**，可以按下面的步骤操作
>
> 1. 管理员运行 cmd，进入`D:\devs\MySQL\mysql-5.7.25-winx64\bin`目录
> 2. 输入命令：`sc delete MySQL57`
> 3. 删除 `data` 目录下的所有文件

> [!link] Reference
> [Windows 上同时安装两个版本的 MySQL](https://blog.csdn.net/wudinaniya/article/details/82455431)