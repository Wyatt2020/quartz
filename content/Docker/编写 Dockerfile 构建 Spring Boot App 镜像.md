---
created: 2024-07-06 09:18
modified: 2024-07-06 09:25
tags:
  - docker
---

我们可以基于 Ubuntu 基础镜像，利用 Dockerfile 描述镜像结构，也可以直接基于 JDK 为基础镜像，省略前面的步骤：

![[Pasted image 20240720180616.png]]

```Dockerfile
# 指定基础镜像为OpenJDK 11的JRE版本
FROM openjdk:11-jre-slim

# 设置容器内的工作目录，也就是应用的根目录
WORKDIR /app

# 将编译好的应用jar包复制到容器内的工作目录
COPY target/your-spring-boot-app.jar /app/app.jar

# 可选：如果应用需要配置环境变量，可以使用ENV指令设置
ENV TZ=Asia/Shanghai

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 健康检查，确保应用运行正常
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s \
  CMD curl -f http://localhost:8080/actuator/health || exit 1

# 运行应用的命令，这里使用java -jar命令来启动Spring Boot应用
# 其中`-Djava.security.egd=file:/dev/./urandom`是为了避免在容器内使用java.security.egd警告
CMD ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/app/app.jar"]

# 指定容器启动后监听的端口，确保与Spring Boot应用配置的端口一致
EXPOSE 8080
```

> [!link] Reference
>  [Dockerfile 语法_bilibili](https://www.bilibili.com/video/BV1S142197x7?p=30&spm_id_from=pageDriver&vd_source=7684be089e60d522b937867a63e07bf7)
