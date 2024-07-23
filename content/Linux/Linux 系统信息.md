---
created: 2023-10-27 12:39
modified: 2023-11-09 14:44
tags:
  - Linux
cssclasses:
  - embed-strict
---

### 系统信息

```shell
cat /etc/redhat-release
```

### 系统版本

```shell
lsb_release -a
```

### 系统架构

```shell
dpkg --print-architecture
```

```shell
arch
```

### 内核信息 uname

`-a` 显示所有系统信息

### 内存信息 free

`Mem` 表示物理内存，`Swap` 表示虚拟内存

- `-b` 以 Byte 为单位显示内存使用情况
- `-k` 以 KByte 为单位显示内存使用情况
- `-m` 以 MByte 为单位显示内存使用情况
- `-g` 以 GByte 为单位显示内存使用情况
- `-h` 以合适的单位显示内存使用情况，最大为三位数，自动计算对应的单位值
- `-t` 以总和的形式显示内存的使用情况
- `-s <秒数>` 每隔 n 秒刷新数据

#### 查看实际的硬件内存大小

```shell
sudo dmidecode -t memory
```

### 磁盘信息 df

![[Linux 磁盘管理#查看磁盘空间使用情况 df|seamless]]

### 进程信息 top

- `-d <秒数>` 指定 top 命令的刷新时间间隔，单位为秒
- `-n <次数>` 指定 top 命令运行的次数后自动退出
- `-p <进程ID>` 仅显示指定进程 ID 的信息
- `-u <用户名>` 仅显示指定用户名的进程信息
- `-H` 在进程信息中显示线程详细信息
- `-i` 不显示闲置（idle）或无用的进程
- `-b` 以批处理（batch）模式运行，直接将结果输出到文件
- `-c` 显示完整的命令行而不截断
- `-S` 累计显示进程的 CPU 使用时间

总体系统信息：

- uptime：系统的运行时间和平均负载
- tasks：当前运行的进程和线程数目
- CPU：总体 CPU 使用率和各个核心的使用情况
- 内存（Memory）：总体内存使用情况、可用内存和缓存

进程信息：

- PID：进程的标识符
- USER：运行进程的用户名
- PR（优先级）：进程的优先级
- NI（Nice值）：进程的优先级调整值
- VIRT（虚拟内存）：进程使用的虚拟内存大小
- RES（常驻内存）：进程实际使用的物理内存大小
- SHR（共享内存）：进程共享的内存大小
- %CPU：进程占用 CPU 的使用率
- %MEM：进程占用内存的使用率
- TIME+：进程的累计 CPU 时间

功能和交互操作：

- 按键命令：在 `top` 运行时可以使用一些按键命令进行操作，如按下 `k` 可以终止一个进程，按下 `h` 可以显示帮助信息等
- 排序：可以按照 CPU 使用率、内存使用率、进程 ID 等对进程进行排序
- 刷新频率：可以设置 `top` 的刷新频率，以便动态查看系统信息

### 网络信息 ifconfig

> [!link] Reference
> [Linux ifconfig命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-ifconfig.html)

### 端口信息 netstat

`netstat -ant`

- `-a` 显示所有连线中的 Socket
- `-n` 直接使用 IP 地址，而不通过域名服务器
- `-t` 显示 TCP 传输协议的连线状况
- `-u` 显示 UDP 传输协议的连线状况
- `-p` 显示正在使用 Socket 的程序识别码和程序名称
- `-i` 显示网卡列表
