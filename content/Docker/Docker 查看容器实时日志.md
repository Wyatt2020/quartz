---
created: 2021-07-08 22:48
modified: 2024-03-04 22:48
tags:
  - docker
---

```shell
docker logs -f -t --since="2021-07-04" --tail=10 tomcat
```

`-f` 查看实时日志
`-t` 查看日志产生的日期，格式 `yyyy-mm-dd`
`--since` 指定输出日志开始日期，即只输出指定日期之后的日志
`-tail=10` 查看最后的 10 条日志
`tomcat8` 容器名称