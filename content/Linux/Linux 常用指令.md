---
created: 2023-07-04 22:54 
tags: Linux
---

### 清屏 clear / ctrl + l

### 自动补全 tab

### 查看已经执行过历史命令 history

### 查看命令的帮助信息 man/help

- `command --help`
- `man command`

使用 `man` 时的操作键

| 操作键 | 功能               |
| ------ | ------------------ |
| 空格键 | 显示手册页的下一屏 |
| Enter  | 一次滚动一行       |
| b      | 回滚一屏           |
| f      | 前滚一屏           |
| q      | 退出               |
| /word  | 搜索 word 字符串     |

### 修改主机名 hostnamectl

> [!link] [Linux 查看及修改主机名](https://blog.csdn.net/sssssuuuuu666/article/details/106469859)

该命令是 `centos7` 后才引入的

查看静态主机名
```shell
hostnamectl status
```

#### 临时修改

1. 通过 `hostname $主机名` 来修改，重启后就会恢复回去
2. 查看是否成功
```shell
cat /etc/hostname
```

#### 永久修改

```shell
hostnamectl set-hostname $主机名
```

### 时间日期类

#### date

`date` 显示当前时间
`date +%Y` 显示当前年份
`date +%m` 显示当前月份
`date +%d` 显示当前是哪一天
`date "+%Y-%m-%d %H:%M:%S"` 显示年月日时分秒（YYYY-MM-dd HH:mm:ss）
`date -d '1 days ago'` 显示前一天时间
`date -d '-1 days ago'` 显示明天时间

#### cal

`cal 2023` 显示 2023 年日历，不加数字显示本月日历

