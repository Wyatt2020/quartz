---
created: 2021-12-21 15:46 
modified: 2023-11-21 09:48
tags: [docker]
---

### daemon.json 中限制

```shell
sudo vim /etc/docker/daemon.json

{
  "log-driver":"json-file",
  "log-opts": {"max-size":"500m", "max-file":"3"}
}
```

- `max-size=500m` 表示一个容器日志大小上限是 500M
- `max-file=3` 表示一个容器有三个日志，分别是 `id+.json`、`id+1.json`、`id+2.json`

#### 重启 docker

```shell
#重新加载配置文件
sudo systemctl daemon-reload
#重启服务
sudo systemctl restart docker.service
```

> [!tip]
> Restart Docker for the changes to take effect for newly created containers. Existing containers do not use the new logging configuration.
> 
> 重启 docker，配置对新创建的容器生效，现有容器不生效

### docker-compose.yml 中限制

```yaml
nginx:
  image: nginx:1.12.1
  restart: always
  logging:
    driver: "json-file"
    options:
      max-size: "5g"
```

### docker run 中限制

```shell
docker run -d --log-opt max-size=1g nginx
```

> [!link] Reference
> 
> - [Docker 限制容器日志大小](https://www.cnblogs.com/angel-devil/p/12558908.html)
> - [官方文档](https://docs.docker.com/config/containers/logging/configure/)
