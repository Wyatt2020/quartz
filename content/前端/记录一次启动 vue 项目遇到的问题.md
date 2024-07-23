---
created: 2021-04-21 21:33
modified: 2024-07-21 21:35
tags:
  - 踩坑
  - 前端
---

[npm install 下载速度慢](https://www.jianshu.com/p/b837d7aff5c0)

> [!bug] 错误 1
> Can't find Python executable "python2.7"

没有安装 Python 2.7
- [下载地址](https://www.python.org/downloads/release/python-2718/)
- [安装与配置](https://blog.csdn.net/lyj_viviani/article/details/51763101)

>[!bug] 错误 2
> Can't find Python executable "python", you can set the PYTHON env variable.

以管理员身份运行 cmd，输入命令

```shell
npm install --global --production windows-build-tools
```

安装完成后

![[Pasted image 20240721214213.png]]

> [!bug] 错误 3
> Visual Studio 看到错误消息“另一个安装程序已开始运行。请先等它完成，然后再重试。”

稍等 windows-build-tools 安装完成

> [!bug] 错误 4
> Failed at the `node-sass@4.9.0` postinstall script.

[整理 node-sass 安装失败的原因及解决办法](https://www.jb51.net/article/180781.htm)

使用 cnpm 安装 node-sass 解决

```shell
cnpm install node-sass
```

> [!bug] 错误 5
> `sass-loader` requires `node-sass` >=4

```shell
npm install node-sass@latest
```

