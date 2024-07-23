---
created: 2023-10-26 16:52
modified: 2023-10-27 17:41
tags:
  - Linux
---

> [!link] Reference
> - [Linux 文件与目录管理 | 菜鸟教程](https://www.runoob.com/linux/linux-file-content-manage.html)

### 列出目录 ls

 - `-a` 查看全部的文件，包括隐藏文件
- `-l` 列出所有的文件，包含文件的属性和权限，没有隐藏文件
- `-h` 配合 `-l` 以人性化的方式显示文件大小

ls 通配符：

| 通配符 | 含义                           |
| ------ | ------------------------------ |
| .      | 代表任意个数字符               |
| ?      | 代表任意一个字符，至少 1 个      |
| []     | 表示可以匹配字符组中的任一个   |
| [abc]  | 匹配 a、b、c 中的任意一个        |
| [a-f]  | 匹配从 a 到 f 范围内的任意一个字符 |

> [!tip] 冷知识
> `ll -a` == `l -la`

### 切换目录 cd

- `cd 绝对路径/相对路径` 切换目录
- `cd .` 保持不动
- `cd ..` 返回上一级目录
- `cd ~` 返回用户主目录

### 当前所在目录 pwd

### 创建目录 mkdir

- `-p` 创建多级目录
- `-v` 显示详细操作

### 删除空目录 rmdir

- `-p` 删除多级空目录
- `-v` 显示详细操作

### 创建空文件 touch

- 如果文件不存在，则创建一个空文件
- 如果文件已经存在，则修改文件的最后修改日期

### 复制文件/目录 cp

```shell
cp [source] [target]
```

- `-v` 显示详细操作

> 将 `apps/zooApp/` 的 `Dockerfile` 文件，复制到 `apps/`，并命名为 `Dockerfile.bak`

```shell
cp apps/zooApp/Dockerfile apps/Dockerfile. bak
```

### 移动文件/目录 mv

```shell
mv [source] [target]
```

用法同 `cp`

- `-f` 强制移动
- `-u` 只替换更新后的文件
- `-v` 显示详细操作

**文件重命名**

```shell
# file 重命名为 file2
mv file.txt file2.txt
```

### 删除文件/目录 rm

- `-f` 忽略不存在的文件，不会出现警告，强制删除
- `-r` 递归删除
- `-i` 互动模式，在删除前会询问是否确定
- `-v` 显示详细操作
- `rm -r ./*` 删除当前目录所有文件
- ~~`rm -rf /* 删库跑路~~

### 以树状图列出文件目录结构 tree

- `-d` 只显示目录

### 查找文件

#### 查找文件或者目录 find

从指定目录向下递归地遍历其各个子目录，将满足条件的文件显示在终端

```shell
find [搜索范围] [选项]
```

| 选项               | 功能                             |
| ------------------ | -------------------------------- |
| `-name <查询方式>` | 按照指定的文件名查找模式查找文件 |
| `-user <用户名>`   | 查找属于指定用户名所有文件       |
| `-size <文件大小>` | 按照指定的文件大小查找文件       |
| `-type <文件类型>` | 文件（f）或者目录（d）           | 

例子：
```shell
# 根据名称查找/目录下的 filename.txt 文件
find opt/ -name *.txt
# 查找 /opt 目录下，用户名称为 -user 的文件
find opt/ -user test
# 在/home目录下查找大于 200m 的文件（+n：大于，-n：小于，n：等于）
find /home -size +204800
```

#### 快速定位文件路径 locate

locate 指令利用事先建立的系统中所有文件名称及路径的 locate 数据库，实现快速定位给定的文件。
locate 指令无需遍历整个文件系统，查询速度较快。
为了保证查询结果的准确度，管理员必须定期更新 locate 数据。

由于 locate 指令基于数据库进行查询，所以第一次运行前，必须使用 `updatedb` 指令创建 locate 数据库。

```shell
updatedb
locate tmp.log
```

#### grep 过滤查找及管道符 " | "

管道符 `|` 表示前一个命令的输出，是后一个命令的输入

```shell
grep [选项] 关键字 文件名
```

`grep` 选项：
- `-n` 显示行号
- `-c` 计算找到“关键字”的次数
- `-i`  忽略大小写的不同
- `--color` 彩色字体

### 压缩与解压

#### gzip/gunzip

压缩文件，只能将文件压缩为 `*.gz` 文件

```shell
gzip -<压缩效率> 文件
```

 **压缩效率**是一个介于 `1－9` 的数值，预设值为 `6`，数值越大，压缩效率就会越高

解压缩文件

```shell
gunzip 文件.gz
```

特点
- 只能压缩文件不能压缩目录
- 不保留原来的文件

#### zip/unzip

安装工具
```shell
yum -y install zip unzip
```

zip 压缩命令在 Window/Linux 都通用，可以压缩目录且保留源文件

压缩文件或目录

```shell
zip [选项] newName.zip 将要压缩的内容
```

- `-r` 压缩目录

解压缩

```shell
unzip [选项] XXX.zip
```

- `-d <目录>` 指定解压后文件的存放目录

#### tar

打包目录，压缩后的文件格式 `.tar.gz`

```shell
tar [选项] XXX.tar.gz 将要打包的内容
```

- `-c` 创建新的打包文件
- `-C <目录>` 解压到指定路径
- `-v` 显示详细信息
- `-f` 指定压缩后的文件名
- `-z` 打包同时压缩
- `-x` 解包 `.tar` 文件

例子：
```shell
# 压缩文件 aa 和 bb
tar -zcvf houma.tar.gz aa.txt bb.txt
# 压缩目录
tar -zcvf test.tar.gz test/
# 解压到当前目录
tar -zxvf test.tar.gz
# 解压到指定目录
tar -zxvf test.tar.gz -C /opt
```
