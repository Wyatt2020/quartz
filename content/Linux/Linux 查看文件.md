---
created: 2023-10-26 17:42
modified: 2023-10-26 17:42
tags:
  - Linux
---

> [!link] Reference
> - [Linux 文件与目录管理 | 菜鸟教程](https://www.runoob.com/linux/linux-file-content-manage.html)

### 》查看

### cat

从第一行开始显示文件内容。

- `-n` 显示行号

### tac

从最后一行开始显示。

### nl

显示时，输出行号。

### more

一页一页的显示文件内容空格代表翻页，回车代表向下看一行。

- `空白键` 代表向下翻一页
- `Enter` 代表向下翻一行
- `/字串` 代表在这个显示的内容中，向下搜索『字串』关键字
- `:f` 输出文件名和当前行的行号
- `ctrl + f` 向下滚动一屏
- `b` 或 `ctrl + b` 返回上一屏
- `q` 退出 `more`

### less

与 `more` 类似，但是比 `more` 更好的是，他可以往前翻页，上下键可以实现上下翻页。

- `空白键` 向下翻一页
- `pagedown` 向下翻一页
- `pageup` 向上翻一页
- `/字串` 向下搜索『字串』
- `?字串` 向上搜索『字串』
- `n` 重复前一个搜索 (与 `/` 或 `?` 有关)
- `N` 反向的重复前一个搜索 (与 `/` 或 `?` 有关)
- `q` 退出 `less`

### head

只看头几行

- `-n` 后面接数字，显示几行的意思

### tail

只看尾巴几行

- `-n` 后面接数字，显示几行的意思
- `-f` 实时更新

例子：
```shell
# 滚动显示最新日志
tail -f -n50 access.log
```

---

### 》过滤

### | 管道符

多个命令通过 `|` 链接，前一个命令的输出，是后一个命令的输入

```shell
ps -ef | grep docker
```

### grep

查找文件里符合条件的字符串

```shell
grep [选项] 关键字 文件名
```

- `-n` 显示行号
- `-c` 计算找到“关键字”的次数
- `-i`  忽略大小写的不同
- `--color` 彩色字体
- `-A<行数>` 除了显示符合搜索条件的那一行之外，并显示该行**之后**的 N 行内容
- `-B<行数>` 除了显示符合搜索条件的那一行之外，并显示该行**之前**的 N 行内容

例子：
```shell
# 除了显示符合搜索条件的那一行之外，并显示该行之后的 10 行内容
grep "aaa" log.log -A 10
# 除了显示符合搜索条件的那一行之外，并显示该行之前的 10 行内容
grep "aaa" log.log -B 10
# 同时搜索满足条件以上两个条件的结果，并向下在打印剩下的 10 行
grep "aaa" log.log | grep "bb" -A 10
# 统计满足条件的的次数
grep "aaa" log.log | wc -l
```

### awk

> [!tip]
> [Linux 中 awk 命令](https://www.cnblogs.com/My-IronMan/p/15721682.html#tocbar--13ohq1s)

对文本根据 `空格` 或者 `tab` 切割，然后根据操作获取符合条件的某行中某列的数据。

`$n` 获取第几个内容，`$0` 获取当前行，`$NF` 获取最后一个字段。

```shell
# a2
echo "a1 a2 a3" | awk '{print $2}'

# a3
echo "a1 a2 a3" | awk '{print $NF}'

# a2
echo "a1 a2 a3" | awk '{print $(NF-1)}'

```

内置函数：
- `toupper()` 字符转为大写
- `tolower()` 字符转为小写
- `length()` 返回字符串长度
- `substr()` 返回子字符串

### sed

### diff

逐行比较文件的差异。
如果指定要比较目录，则 diff 会比较目录中相同文件名的文件，但不会比较其中子目录。

- `-<行数>` 指定要显示多少行的文本。此参数必须与 `-c` 或 `-u` 参数一并使用
- `-a` 预设只会逐行比较文本文件
- `-b` 不检查空格字符的不同
- `-B` 不检查空白行
- `-c` 显示全部内文，并标出不同之处
- `-C<行数>` 与执行"-c<行数>"指令相同
- `-H` 比较大文件时，可加快速度
- `-i` 不检查大小写的不同
- `-u<列数>`,  `-U<列数>` 以合并的方式来显示文件内容的不同
- `-w` 忽略全部的空格字符
- `-W<宽度>` 在使用 -y 参数时，指定栏宽
- `-y` 以并列的方式显示文件的异同之处

例子：
```shell
# 并排输出两个文件的不同
diff log1.log log2.log -y -W 50
```

### echo

```shell
echo [-e] [输出内容]
```

- `-e` 支持反斜线控制的字符转换

| 控制字符 | 作用                    |
| -------- | ----------------------- |
| `\`      | 输出\本身               |
| `\n`     | 换行符                  |
| `\t`     | 制表符，也就是 `Tab` 键 |

### 输入/输出重定向

重定向命令列表如下：

| 命令            | 说明                                               | 
| --------------- | -------------------------------------------------- |
| command > file  | 将输出重定向到 file                              |
| command < file  | 将输入重定向到 file                              |
| command >> file | 将输出以**追加**的方式重定向到 file                  |
| n > file        | 将文件描述符为 n 的文件重定向到 file             |
| n >> file       | 将文件描述符为 n 的文件以**追加**的方式重定向到 file |
| n >& m          | 将输出文件 m 和 n 合并                           |
| n <& m          | 将输入文件 m 和 n 合并                           |
| << tag          | 将开始标记 tag 和结束标记 tag 之间的内容作为输入 |

文件描述符：
- 0 通常是标准输入（STDIN）
- 1 是标准输出（STDOUT）
- 2 是标准错误输出（STDERR）

#### 输出重定向

将 command 的结果输出到 file，file 内的已经存在的内容将被新内容**覆盖**
如果不希望文件内容被覆盖，可以使用 `>>` **追加**到文件末尾

```shell
[command] > [file]
```

例子：
```shell
echo "hello Linux" > file
# hello Linux
cat file

echo "hello Linux 2" >> file
# hello Linux
# hello Linux2
cat file

echo "hello Linux 3" > file
# hello Linux 3
cat file
```

#### 输入重定向

从 file 获取输入内容，重定向到 command

```shell
[command] < [file]
```

例子：
```shell
echo "hello" > file
# hello
cat < file

# 读取 file 的内容，并将内容输出到 file2
cat < file > file2
```

#### 错误重定向

```shell
# 2 表示错误文件描述符
command 2>file
```

例子：
```shell
aaa 2> file
# -bash: aaa: command not found
cat file
```

```shell
# 有时候我们需要将标准输出流和标准错误流输入到一个文件。a.txt 存在，bb.txt 不存在。
ls a.txt bb.txt > error.log 2>&1

# 如果内容需要追加
ls a.txt bb.txt >> error.log 2>&1

# 如果不想要输出内容, 写入到 /dev/null 的内容都会被丢弃掉 
ls a.txt bb.txt >> /dev/null 2>&1
```

---

### 》统计
### sort
### uniq
