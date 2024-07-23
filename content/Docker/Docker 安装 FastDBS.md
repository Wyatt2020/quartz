---
created: 2022-04-27 00:00
modified: 2024-03-05 11:43
tags: [docker]
---

### docker0

```bash
sudo docker run -id --name fastdfs-tracker -p 22122:22122 --restart=always -v /home/study/fastdfs/tracker/data:/fastdfs/tracker/data season/fastdfs:1.2 tracker
```

```bash
sudo docker run -id --name fastdfs-storage --restart=always -v /home/study/fastdfs/storage/data:/fastdfs/store_path -e TRACKER_SERVER="172.17.0.5:22122" season/fastdfs:1.2 storage
```

```bash
sudo docker run -id --name fastdfs-nginx --restart=always -v /home/study/fastdfs/storage/data:/fastdfs/store_path -v /home/study/fastdfs/nginx/nginx.conf:/etc/nginx/conf/nginx.conf -p 8080:80 -e TRACKER_SERVER=172.17.0.5:22122 season/fastdfs:1.2 nginx
```

### host

```bash
sudo docker run -id --name fastdfs-tracker --restart=always --network=host -v /home/study/fastdfs/tracker/data:/fastdfs/tracker/data season/fastdfs:1.2 tracker
```

```bash
sudo docker cp /home/study/fastdfs/client.conf fastdfs-tracker:/etc/fdfs
sudo docker exec -it fastdfs-tracker bash
```

```bash
sudo docker run -id --name fastdfs-storage --restart=always --network=host -v /home/study/fastdfs/storage:/fastdfs/store_path -e TRACKER_SERVER=47.108.77.227:22122 season/fastdfs:1.2 storage
```

```bash
sudo docker run -id --name fastdfs-nginx --restart=always -v /home/study/fastdfs/storage:/fastdfs/store_path -v /home/study/fastdfs/nginx/nginx.conf:/etc/nginx/conf/nginx.conf -p 8080:80 -e TRACKER_SERVER=47.108.77.227:22122 season/fastdfs:1.2 nginx
```

> [!info] 参考链接
> - [文件管理系统FastDFS原理详解](https://mp.weixin.qq.com/s/qmULlbIn4lol1zRV0cGjxw)
> - [Docker 中搭建 FastDFS 文件系统](https://www.cnblogs.com/niceyoo/p/13511082.html)
> - [FastDFS 基于 Docker 安装，免踩坑版](https://mp.weixin.qq.com/s/2WXESAIFHfzHyoEWNE7M6g)
> - [SpringBoot集成FastDFS依赖实现文件上传](https://www.cnblogs.com/niceyoo/p/13512089.html)
