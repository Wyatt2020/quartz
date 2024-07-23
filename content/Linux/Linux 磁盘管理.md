---
created: 2023-10-27 11:38
modified: 2023-10-27 11:38
tags:
  - Linux
---

> [!link] Reference
> - [Linux fdisk 命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-fdisk.html)
> - [Linux mount命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-mount.html)
> - [Linux umount 命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-umount.html)

### 查看磁盘空间使用情况 df

- `-a` 列出所有的文件系统，包括系统特有的 `/proc` 等文件系统
- `-k` 以 KBytes 的容量显示各文件系统
- `-m` 以 MBytes 的容量显示各文件系统
- `-h` 以易读的 GBytes, MBytes, KBytes 等格式显示
- `-H` 以 1000 取代 1024 的进位方式
- `-T` 显示文件系统类型, 连同该 partition 的 filesystem 名称 (例如 ext3) 也列出
- `-i` 不用硬盘容量，而以 inode 的数量来显示

### 查询指定文件/目录的磁盘占用情况 du

```shell
du -h /目录
```

- `-a` 显示目录中个别文件的大小
- `-b` 以 byte 为单位显示目录或文件的大小
- `-k` 以 KByte 为单位显示目录或文件的大小
- `-m` 以 MByte 为单位显示目录或文件的大小
- `-c` 除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和
- `-D` 显示指定符号连接的源文件大小
- `-h` 以易读的 GBytes, MBytes, KBytes 等格式显示
- `-H ` 以 1000 取代 1024 的进位方式
- `-s` 仅显示指定目录或文件的总大小，而不显示其子目录的大小
- `-S` 显示个别目录的大小时，并不含其子目录的大小
- `--exclude=<目录或文件>` 略过指定的目录或文件
- `--max-depth=<目录层数>` 超过指定层数的目录后，予以忽略

```shell
# 显示当前目录的总大小，不含子目录的大小
du -sh .
# 显示当前目录的总大小，不含子目录的大小
du -h --max-depth=0 .
```

### 查看分区 fdisk

必须在 root 用户下才能使用

```shell
fdisk -l
```

- Device：分区序列
- Boot：引导
- Start：从 X 磁柱开始
- End：到 Y 磁柱结束
- Blocks：容量
- Id：分区类型 ID
- System：分区类型

### 挂载/卸载 mount/umount

对于 Linux 用户来讲，不论有几个分区，分别分给哪一个目录使用，它总归就是一个根目录、一个独立且唯一的文件结构。
Linux 中每个分区都是用来组成整个文件系统的一部分，它在用一种叫做“挂载”的处理方法，它整个文件系统中包含了一整套的文件和目录，并将一个分区和一个目录联系起来，要载入的那个分区将使它的存储空间在这个目录下获得。

挂载设备
```shell
mount [-t vfstype] [-o options] device dir
```

卸载设备
```shell
umount 设备文件名或挂载点
```