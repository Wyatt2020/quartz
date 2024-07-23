---
created: 2023-10-27 13:09
modified: 2023-10-27 14:16
tags:
  - Linux
---

> [!link] Reference
> - [Linux rpm命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-rpm.html)
> - [linux yum 命令 | 菜鸟教程](https://www.runoob.com/linux/linux-yum.html)

## rpm

### 查询所安装的所有 rpm 软件包

```shell
rpm -qa
```

### 查询指定软件的安装情况

```shell
rpm -qa | grep rpm包名
```

### 查询软件安装信息

```shell
rpm -qi rpm包名
```

### 查询 rpm 包安装哪些文件及位置

```shell
rpm -ql rpm包名
```

### 安装

```shell
rpm -ivh rpm包名
```

| 选项       | 功能                  |
| ---------- | --------------------- |
| `-i`       | install，安装         | 
| `-v`       | verbose，显示详细信息 |
| `-h`       | hash，进度条          |
| `--nodeps` | 不检测依赖            |

### 卸载

```shell
rpm -e rpm包名
```

```shell
rpm -e --nodeps rpm包名
```

| 选项       | 功能                                                               |
| ---------- | ----------------------------------------------------------------- |
| `-e`       | 卸载软件包                                                           |
| `--nodeps` | 卸载软件时不检查依赖。这样的话，那些使用该软件包的软件，之后可能就不能正常工作了 |

### 升级

```shell
rpm -Uvh rpm包名
```

### 查询文件属于的安装程序

```shell
rpm -qf 系统文件名
```

### 校验安装的包中的文件是否被修改

```shell
rpm -V rpm包名
```

## yum

基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

### ubuntu 安装 yum

```shell
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essential
sudo apt-get install yum
```

如果报错提示：<font color="#ff0000">File "/usr/bin/yum", line 30     except KeyboardInterrupt</font>

修改 yum 配置，将第一行改成 `#! /usr/bin/python2.7`

```shell
vim /usr/bin/yum
```

查看已安装的 python 版本
`python` + `Tab`

### 配置 yum 源

1. 备份原 yum 源
```shell
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
```

2. 使用新 yum 源

- [网易 CentOS5](http://mirrors.163.com/.help/CentOS5-Base-163.repo)
- [网易 CentOS6](http://mirrors.163.com/.help/CentOS6-Base-163.repo)
- [网易 CentOS7](http://mirrors.163.com/.help/CentOS7-Base-163.repo)

```shell
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

- [阿里](https://developer.aliyun.com/mirror/centos)
```shell
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

3. 清除旧缓存

```shell
yum clean all
```

4. 生成新缓存

```shell
yum makecache
```

### 参数

- `-y`：当安装过程提示选择全部为 "yes"
- `-q`：不显示安装的过程
- `-h`：帮助

### 全局检查更新

```shell
yum check-update
```

### 安装

```shell
yum -y install 包名list
```

安装本地包

```shell
yum install 包名list

yum localinstall 包名list
```

### 升级

如果不指定包名，则全局更新

```shell
yum -y update 包名list
```

### 卸载

```shell
yum -y remove 包名list
```

### 包信息

```shell
yum info 包名list
```

### 查找包

```shell
yum search 关键词
```

### 列出仓库的包

```shell
yum list 关键词list
```

```shell
yum list | grep 关键字
```
`@`代表已经安装成功

### 列出已安装的包

```shell
yum list installed
```

### 显示包的依赖关系

```shell
yum deplist 关键词
```

## apt-get

### 更换软件源

> [!link]
> - [ubuntu | 清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)
> - [Ubuntu 20.04 系统下更改 apt 源为阿里源](https://zhuanlan.zhihu.com/p/251009600)
> - [ubuntu 镜像 - 阿里巴巴开源镜像站](https://developer.aliyun.com/mirror/ubuntu)

Ubuntu 的软件源配置文件是 `/etc/apt/sources.list`

1. 先备份原版

```shell
mv /etc/apt/sources.list /etc/apt/sources.list.bak
```

2. 创建新的软件源

```shell
vim /etc/apt/sources.list
```

3. 替换成以下内容

```
deb https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

# deb https://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

5. 更新软件列表

```shell
sudo apt-get update
```

6. 更新软件包

```shell
sudo apt-get upgrade
```

