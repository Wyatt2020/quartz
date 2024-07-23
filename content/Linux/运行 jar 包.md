---
created: 2023-11-09 12:30
modified: 2023-11-09 12:32
tags:
  - Linux
  - Java
---

### 直接运行

```shell
java -jar shareniu.jar
```

- 特点：当前 ssh 窗口会被占用，可按 CTRL + C 打断程序运行，或直接关闭窗口，程序退出

### 后台运行

```shell
java -jar shareniu.jar &
```

- `&` 代表在后台运行
- 特点：当前 ssh 窗口不被占用，但是当窗口关闭时，程序中止运行

### 不挂断运行命令+后台运行

```shell
nohup java -jar shareniu.jar &
```

- `nohup` 是不挂断运行命令，当账户退出或终端关闭时，程序仍然运行
- 当用 `nohup` 命令执行作业时，缺省情况下该作业的所有输出被重定向到 `nohup.out` 的文件中，除非另外指定了输出文件

### 不挂断运行命令+后台运行+输出重定向

```shell
nohup java -jar shareniu.jar >/dev/null &
```

- `command > out.file` 是将 command 的输出重定向到 out.file 文件，即输出内容不打印到屏幕上，而是输出到 out.file 文件中
- 把 `/dev/null` 看作"黑洞"，它等价于一个只写文件，并且所有写入它的内容都会永远丢失

### 查看后台运行任务

```shell
jobs
```

将会**列出所有后台执行的作业**，并且每个作业前面都有个编号。

如果想将某个作业调回前台控制，只需要 `fg 编号` 即可。

### 查看某端口占用的线程的 pid

```shell
netstat -nlp |grep :918
```
