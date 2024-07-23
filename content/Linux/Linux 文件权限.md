---
created: 2023-10-26 16:15
modified: 2023-11009-24642 17:01
tags:
  - Linux
---

> [!link] Reference
> - [Linux chmod 命令 | 菜鸟教程](https://www.runoob.com/linux/linux-comm-chmod.html)

### 查看全部文件权限

```shell
ll -a
```

![[Linux 文件权限.png|500]]

每行信息开头的第一个字母表示文件类型

- `d` 表示目录
- `-` 表示文件
- `l` 表示软连接

Linux 文件属性有两种设置方法，一种是数字（常用的是数据），一种是符号。

Linux 文件的基本权限就有九个，分别是 `owner/group/others` 三种身份各有自己的 `read/write/execute` 权限。

文件的权限字符为 `-rwxrwxrwx`，这九个权限是三个三个一组的。其中，我们可以使用数字来代表各个权限，各权限的分数对照表如下：

```
r:4     w:2         x:1
可读可写可执行    rwx  7
可读可写不可执行  rw-  6
```

### 修改权限 chmod

```shell
chmod [-R] [{ugoa}{+-=}{rwx}] [文件或目录]
```

```shell
chmod [-R] [mode=421] [文件或目录]
```

- `u` 所有者
- `g` 所有组
- `o` 其他人
- `a` 所有人(u、g、o 的总和)

- `+` 增加权限
- `-` 去除权限
- `=` 重新设置所有权限

- `r` = 4 读
- `w` = 2 写
- `x` = 1 执行
- `rwx` = 4 + 2 + 1 = 7 全部权限

```shell
chmod u+x test.txt #给所有者增加执行权限

chmod g+x test.txt #给所属组用户增加执行权限

chmod u-x,o+x test.txt #去除所有者的执行权限,并给其他用户增加执行权限

chmod 777 test.txt #设置所有者、所属组、其他用户都具有可读可写可执行权限

chmod -R 777 test/ #递归设置 text/ 及其子文件的权限，使所有者、所属组、其他用户都具有可读可写可执行权限
```

### 修改所有者 chown

改变文件或者目录的所有者

```shell
chown [-R] [用户/用户标识号] [文件或目录]
```

- `R` 递归设置

### 修改所有组 chgrp

改变文件或者目录的所有组

```shell
chgrp [-R] [用户组/用户组标识号] [文件或目录]
```

- `R` 递归设置
