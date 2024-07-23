---
created: 2021-07-04 22:52
modified: 2024-03-04 22:52
tags:
  - docker
---

1. 查看 tomcat 的 `webapps` 目录是否有文件，没有就新建 `ROOT/index.html` 文件

2. 查看 tomcat 是否启动成功，日志有没有报错

```shell
docker ps

tail catalina.2021-07-04.log
```

3. 查看端口是否开启

```shell
netstat -ntlp
```

4. 查看防火墙是否关闭

```shell
firewall-cmd --state    #查看状态
systemctl stop firewalld    #关闭防火墙
systemctl disable firewalld    #开机自动关闭
```

5. 查看阿里云的安全组是否开放 8080 端口

> [!link] Reference
> - [Centos 查看端口占用、开启端口](https://www.cnblogs.com/zhaoyq/p/9432160.html)
> - [Docker Tomcat 为何看不到访问界面](https://blog.csdn.net/qq_42875895/article/details/104324960)