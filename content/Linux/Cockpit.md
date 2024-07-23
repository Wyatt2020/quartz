---
created: 2023-11-21 16:53
modified: 2023-11-21 16:53
tags:
  - Linux
  - 工具
---

> [!summary]
> [Cockpit](https://cockpit-project.org/) 是一种开源免费的基于 Web 的图形服务器管理工具，可让管理员和 Linux 用户从浏览器轻松管理和配置其 Linux 服务器 / PC。它针对 Linux 新手用户或想要在图形界面上管理服务器的人，而无需在命令行上工作。 

> [!link] Reference
> [如何在 Ubuntu 22.04 安装 Cockpit Web 控制台](https://segmentfault.com/a/1190000042644816)

Cockpit 易于安装，使用简单，它允许您执行以下任务。

-   密切关注系统指标和性能
-   创建和管理用户
-   浏览和搜索系统日志
-   检查基于系统的服务并与之交互
-   进入终端并执行命令
-   检查系统硬件
-   创建和管理虚拟机
-   升级软件包到最新版本
-   配置防火墙等等

### (1) 更新本地包索引

首先，登录到服务器，更新本地包索引

```shell
sudo apt update
```

### (2) 安装 Cockpit Web 控制台

Cockpit Web 控制台包由官方 Ubuntu 存储库提供，运行包搜索命令

```shell
apt search cockpit
```

使用 APT 包管理器安装 cockpit

```shell
sudo apt install cockpit -y
```

该命令安装 Cockpit Web 控制台和其他附加包、库和依赖项。

![[1460000042644818.webp]]

安装完成后，运行以下命令验证 Cockpit 是否已安装

```shell
apt -qq list cockpit
OR
dpkg -l cockpit
```

从下面的输出，我们可以看到 Cockpit Web 版本 264-1 已经安装。

![[1460000042644819.webp]]

In case, you planning to manage [kvm virtual machines with cockpit](https://link.segmentfault.com/?enc=BjyJMrSsPCupwb9ikjkXLQ%3D%3D.lfHh%2FmrW8wz9xjcPDxOaHKhDqt1RemfsFf3YhMoWvST%2FrHF06z7mZRHXb81uYozhc17%2BcQRPT2sMCA1XQ%2FQBmkVygKuIIHmE1SCj13Zkmao%3D) then install following package

如果您计划管理 [带 Cockpit 的 KVM 虚拟机](https://link.segmentfault.com/?enc=Nd15B7L%2Fd%2Bo4eyCW5ldgkg%3D%3D.xC15Yj%2Bfj9MAYb6ykOk3cdqqOZqBXwwj8t8M61WWV2qrA1t8jhXm4OCWb53IhxFnT0zpErdA6mmptnXmnU8Hfqzz7LWCUtN7NPhba4a2aow%3D) ，需要安装以下软件包

```shell
sudo apt install cockpit-machines -y
```

### (3) 为 Cockpit 安装 Podman 支持

默认情况下，Cockpit 不提供 podman 支持。如果您希望使用 Cockpit 管理 podman 容器，请安装为 podman 提供支持的 cockpit-podman 包。

```shell
sudo apt install cockpit-podman y
```

![[1460000042644820.webp]]

### (4) 启动 Cockpit Web 控制台服务

与其他服务或守护进程不同，Cockpit 不会在安装后自动启动。因此，启动 Cockpit 系统服务，如图所示。

```shell
sudo systemctl start cockpit
```

接下来，查看 Cockpit 服务是否正在运行。

```shell
sudo systemctl status cockpit
```

输出显示 Cockpit 服务已启动并运行。

![[1460000042644821.webp]]

Cockpit listens on TCP port 9090. You can verify this is the case by running the following [ss command](https://link.segmentfault.com/?enc=iQ%2FFGfdpkUFeIt5%2BVP1PuQ%3D%3D.5nP4pmvT9L264BV76awO%2Fqg4vLirnihJoehaeHdMQJsdD%2B9GmfC7eOqFZfpb7CByIZfn57YnJgxgPo0377JoTHZcBL2BwRskFxhEEEoqUBs%3D).

Cockpit 监听 TCP 9090 端口，您可以通过运行以下 [ss command](https://link.segmentfault.com/?enc=5EiXE1vCQdDS2IQBNnmTMA%3D%3D.9Namw0By7GiJis3mLxDq90h%2FgaVmz7OTI37%2BJkZpOQXWD%2BheRGsNo7JIL6uprikcJFxrtPIqdD7WxvpmA53j4pBO3Fh4wG5QGd5JhzZvf93AYJaq7M4TQBVbmIqL28pF) 验证它。

```shell
ss -tunlpe | grep cockpit
```

![[1460000042644822.webp]]

如果在 Ubuntu 22.04 系统上启用了防火墙，则允许 9090/TCP 端口，以便可以从外部访问其 Web 控制台

```shell
sudo ufw allow 9090/tcp
sudo ufw reload
```

![[1460000042644823.webp]]

### (5) 访问 Cockpit Web 控制台

Cockpit 现在已经完全安装，唯一剩下的就是访问它，并使用它来管理您的服务器。

要访问 Cockpit Web 控制台，请浏览以下地址。

[https://server-ip:9090](https://link.segmentfault.com/?enc=No%2B8hP%2Fk%2BiCtcWHRbdXpYg%3D%3D.nw%2F10Zz%2FNpERpgnnI8ixwKJJ%2BKxhXbVDkP2mA%2BOzVl0%3D)

你会得到一个警告，你正在访问的网站不是私人的，你可能成为黑客的受害者。不要担心，这是因为服务器是由 CA 不识别的自签名 SSL 证书加密的。

要解决这个问题，只需单击 Advanced

![[1460000042644824.webp]]

然后单击 Proceed to server-ip link

![[1460000042644825.webp]]

之后，将出现以下登录页面。输入用户名和密码并单击 Log In

![[1460000042644826.webp]]

这将引导您进入 Cockpit 仪表盘。概述页面提供的系统指标，一目了然。您将看到关于 CPU 和内存使用情况、健康状态和系统信息的详细信息。

要执行管理任务，单击 Turn on administrative access，它将提示您输入用户的密码。

左边栏提供了各种可查看的选项。

![[1460000042644827.webp]]

例如，单击 Networking，可以查看带宽统计信息、网络接口信息和网络日志信息。

![[1460000042644828.webp]]

您也可以单击 Services，获取所有系统服务的信息。

![[1460000042644829.webp]]

查看和安装软件更新，点击 Software Updates

![[1460000042644830.webp]]


