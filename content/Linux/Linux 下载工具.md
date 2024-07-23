---
created: 2023-10-27 13:40
modified: 2023-10-27 16:43
tags:
  - Linux
---

> [!link]- Reference
> - [wget 命令用法详解 - 博客园](https://www.cnblogs.com/lukelook/p/11201098.html)
> - [wget 命令用法详解 - 腾讯云](https://cloud.tencent.com/developer/article/1527592)
> - [curl 命令最全详解 - CSDN博客](https://blog.csdn.net/angle_chen123/article/details/120675472)
> - [curl 命令详解 - 掘金](https://juejin.cn/post/7210320199690305596?searchId=20231027155549C206785CB1936FC01373#heading-2)

### wget

#### 下载并重命名

```shell
wget -O [filename] [link]
```

#### 批量下载

首先，保存一份下载链接文件

```shell
cat > filelist.txt
url1
url2
```

接着使用这个文件和参数 `-i` 下载

```shell
wget -i filelist.txt
```

**限制总下载文件大小**

```shell
wget -Q5m -i filelist.txt
```

#### 限速下载

```shell
wget --limit-rate=300k [link]
```

#### 断点续传

```shell
wget -c [link]
```

#### 后台下载

```shell
wget -b [link]
```

查看下载进度

```shell
tail -f wget-log
```

#### 伪装代理名称下载

```shell
wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" [link]
```

#### 测试下载链接是否有效

```shell
wget --spider [link]
```

#### 增加重试次数

默认重试 20 次

```shell
wget --tries=3 [link]
```

#### 爬取整个网站

```shell
wget --random-wait -nv -r -p -e robots=off -U mozilla Website_URL
```

`--random-wait`：在下载文件之间引入随机等待时间，以模拟人类访问行为，避免对目标网站造成过大的负担。  
`-r` 或 `--recursive`：启用递归下载，允许 wget 下载整个网站的所有链接，包括子目录和文件。  
`-p` 或 `--page-requisites`：下载页面的所有必需文件，如图像、CSS 文件和 JavaScript 文件。  
`-e robots=off`：忽略目标网站的 `robots.txt` 文件，该文件通常用于指示网络爬虫哪些页面可以访问。  
`-U mozilla`：设置 User-Agent 头部，将其伪装成 Mozilla 浏览器，以防止网站对下载工具的识别和限制。
`-nv` 或 `--no-verbose`：禁止显示详细的下载进度和输出信息，使输出更为简洁。
`--accept-regex`：根据正则来过滤你需要的 uri。

#### 保存网站镜像

下载整个网站到本地

```shell
wget --mirror -p --convert-links -P ./LOCAL URL
```

`–miror`：启用镜像模式，递归下载整个网站并保持本地副本与源站点同步
`-p`：下载页面的所有必需文件，如图像、CSS 文件和 JavaScript 文件
`-k` 或 `–-convert-links`：在下载的文件中转换链接，使其指向本地副本，而不是源站点上的链接
`-P ./LOCAL`：保存所有文件和目录到本地指定目录

#### 过滤指定格式

```shell
wget --reject=gif [link]
```

#### 下载信息存入日志

```shell
wget -o download.log [link]
```

#### 下载指定格式文件

下载一个网站的所有 PDF 文件

```shell
wget -r -A.pdf [link]
```

#### FTP 下载

匿名下载

```shell
wget [ftp-link]
```

认证下载

```shell
wget --ftp-user=USERNAME --ftp-password=PASSWORD [link]
```

### curl

#### 发送简单请求

```shell
// 默认发送 GET 请求
curl https://www.baidu.com

// 发送 POST 请求
curl -X POST -d "" https://www.baidu.com

// 发送 PUT 请求
curl -X PUT https://www.baidu.com

// 发送 DELETE 请求
curl -X DELETE https://www.baidu.com
```

#### -A/--user-agent：User-Agent

```shell
curl -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36" https://www.baidu.com
```

#### -b/-c：读取/存储 Cookie

将请求时产生的 cookie 放到 `cookie_file` 中

```shell
curl -c cookie_file https://www.baidu.com
```

发出请求时将 `cookie_file` 中的数据带入请求中

```shell
curl -b cookie_file https://www.baidu.com
```

也可以用以下形式传递 cookie

```shell
curl -b "oraclelicense=accept-securebackup-cookie"  https://www.baidu.com
```

#### -C：断点续传

`-C offset`：从指定的 `offset` 位置开始续传
`-C -`：让`curl`自己分析该从什么位置开始续传

```shell
curl -C - -o forest.jpg https://lmg.jj20.com/up/allimg/1114/0406210Z024/2104060Z024-5-1200.jpg
```

#### -d：携带 POST 请求的 data

```shell
curl -X POST -d "name=andrew&age=22" https://www.baidu.com

curl -X POST -F "nm=da" https://www.baidu.com

# 将data编码
curl -X POST --data-urlencode "name=andrew age=22" https://www.baidu.com
```

`-d` 是发送 `post` 参数，`-F` 是发送 `form-data` 数据，两者不能同时使用，否则会报错

#### -D：把 header 信息写入到指定文件中

把请求返回的 header 信息写入到 `header_file` 文件中

```shell
curl -D header_file https://www.baidu.com
```

#### -e/--referer：带入来源网址 referer

部分`server`为了避免盗链问题会确认引用源是否来自同一个网站，此时就要欺骗服务器的检查机制来取得相关资源

```shell
curl -e "www.google.com" https://www.baidu.com
```

当某些连接必须通过 301 或 302 跳转过去时，用 `auto` 参数来让访问更加拟真

```shell
curl -L -v -e ";auto" https://www.baidu.com
```

#### -F/--form：表单提交

```shell
curl -X POST -F 'uid="123456789"' https://www.baidu.com
```

#### -H/--header：请求头

```shell
curl --header 'Content-Type: application/x-www-form-urlencoded' https://www.baidu.com
```

#### -i/-I：显示 response 的 header

`-i` 返回 header 和网页内容

```shell
curl -i http://www.baidu.com
```

`-I` 只返回 header

```shell
curl -I http://www.baidu.com
```

#### -K/--config：指定配置文件

`-K` 后接配置文件名，如果使用 `-` 符号，则通过 `stdin` 输入配置

```shell
echo "user = user:passwd" | curl -K - https://www.baidu.com
```

#### -L：跟随跳转

通常情况下 `curl` 命令不会跟随 `301` 或 `302` 跳转，如果期望跟随跳转可以加上 `-L` 参数。

比如我们在访问谷歌或百度时，`URL` 没有加上 `www` 前缀，会自动触发 `301` 或 `302` 跳转

```shell
curl -L http://google.com/
```

#### -m：限制完成时间

让 curl 必须在30分钟（1800s）内完成

```shell
curl -m 1800 -Y 3000 -y 60 www.far-away-site.com
```

#### -o/-O：下载文件

`-o` 代表下载文件并重命名
`-O`代表下载文件并使用原文件名

#### -r：返回指定范围内的字符

返回响应的前 100 个字符

```shell
curl -r 0-99 https://www.baidu.com
```

返回响应的最后 500 个字符

```shell
curl -r -500 https://www.baidu.com
```

#### -s：减少输出的信息

```shell
curl -s http://www.baidu.com
```

#### -T：上传文件

上传所有的 `stdin` 标准输入到 server，按 `ctrl+d` 结束输入，前提是该 server 能接收 PUT 类型的请求

```shell
curl -T - ftp://ftp.upload.com/myfile
```

也可以通过管道传递 `stdin`

```shell
echo "user = user: passwd" | curl -T - ftp://ftp.upload.com/myfile
```

上传指定文件到 server，并指定上传后的文件名为 `myfile`

```shell
curl -T uploadfile -u user: passwd ftp://ftp.upload.com/myfile
```

上传指定文件到 server，并沿用本地文件名

```shell
curl -T uploadfile -u user: passwd ftp://ftp.upload.com/
```

`-a`: 使用追加的方式上传文件

```shell
curl -T uploadfile -a ftp://ftp.upload.com/myfile
```

#### -u：设定用户名和密码

在访问 ftp 服务器时需要输入用户名和密码

```shell
curl -u name:passwd ftp://machine.domain:port/full/path/to/file
```

#### -v：输出完整信息

显示一次 `http` 通信的整个过程，通常用于 `debug`

```shell
curl -v http://www.baidu.com
```

#### -V：查看 curl 版本

```shell
curl -V
```

#### -w：请求完成后显示自定义信息

显示响应状态码

```shell
curl -w "%{http_code}\n" -i -s -o /dev/null https://www.baidu.com
```

显示响应 content_type

```shell
curl -w "%{content_type}\n" -i -s -o /dev/null https://www.baidu.com
```

#### -x：使用代理服务器（proxy）

```shell
curl -x 192.168.5.1:8888 http://www.baidu.com
```

如果代理服务器需要账号密码，可以使用 `-U` 或 `--proxy-user` 来指定

```shell
curl -U username:password -x 192.168.5.1:8888 http://www.baidu.com
```

不使用代理访问

```shell
curl --noproxy localhost,get.this http://www.baidu.com
```

#### -X/--request：指定请求类型

```shell
curl --request GET https://www.baidu.com
curl -X POST https://www.baidu.com
```

#### -Y/-y：限制下载速度

限制 curl 的下载速度在每秒 3000 字节以内，保持 60 秒

```shell
curl -Y 3000 -y 60 www.far-away-site.com
```

#### --dump-header：保存 header 信息

将返回的 header 信息保存到 `header.txt` 文件中

```shell
curl --dump-header header.txt https://www.baidu.com
```

#### --limit-rate：限制下载速度

```shell
curl --limit-rate 100k -o forest.jpg https://lmg.jj20.com/up/allimg/1114/0406210Z024/2104060Z024-5-1200.jpg
```

#### --local-port：强制使用本地端口号

```shell
curl --local-port 8765 https://www.baidu.com
```

#### --resolve：强制解析 Host 为指定 IP

```shell
curl --resolve www.google.com:443:142.251.35.164 -v https://www.google.com
```

#### --trace ：输出请求的详细信息

如果用 `-v` 还是不能定位问题，可以进一步用 `--trace` 以 `ascii` 编码格式将更详细的内容输出到指定文件中，据此来 `debug`

将 trace 信息保存到 trace.txt 文件中

```shell
curl --trace trace.txt https://www.baidu.com
```

#### -# :展示下载进度

通常在下载文件时配合 `-o/-O` 使用

```shell
curl -# -o forest.jpg https://lmg.jj20.com/up/allimg/1114/0406210Z024/2104060Z024-5-1200.jpg
```

