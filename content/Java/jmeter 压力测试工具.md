---
created: 2022-06-06 09:50
modified: 2024-07-05 10:22
tags:
  - Java
source:
  - https://blog.csdn.net/qq_24391607/article/details/105309171
---

## 一. JMeter 介绍以及应用场景

Apache JMeter 是 Apache 组织开发的基于 Java 的压力测试工具。用于对软件做压力测试，它最初被设计用于 Web 应用测试，但后来扩展到其他测试领域。它可以用于测试静态和动态资源，例如静态文件、Java 小服务程序、CGI 脚本、Java 对象、数据库、FTP 服务器，等等。JMeter 可以用于对服务器、网络或对象模拟巨大的负载，来自不同压力类别下测试它们的强度和分析整体性能。另外，JMeter 能够对应用程序做功能 / 回归测试，通过创建带有断言的脚本来验证你的程序返回了你期望的结果。为了最大限度的灵活性，JMeter 允许使用正则表达式创建断言。

JMeter 是一个 Java 桌面应用程序，具有使用 Swing 图形 API 的图形界面。因此，它可以在任何接受 Java 虚拟机的环境 / 工作站上运行，例如 Windows，Linux，Mac 等。

**JMeter 支持的协议**

1. **Web - HTTP，HTTPS 站点 web 1.0, web 2.0 (ajax，flex 和 flex-ws-amf)**  
2. Web 服务 -SOAP/XML-RPC  
3. **数据库通过 JDBC 驱动程序**  
4. 目录 - LDAP  
5. 通过 JMS 提供面向消息传递的服务  
6. 服务 -POP 3，IMAP，SMTP  
7. FTP 服务

**JMeter 的作用**

1. **接口测试**  
2. 性能测试：程序的算法  
3. **压力测试：外在负载**  
4. Web 自动化测试  
5. 数据库测试  
6. java 测试

在我们编写代码的过程中，经常会出现单机测试下并无任何问题和 bug，但是在并发或者集群场景下就会出现许许多多未知的 bug。所以根据面向 KPI 编程定律，为了避免在测试手中或者线上场景出现未知的问题导致 KPI 扣除，我们就要本地通过 JMeter 去模拟高并发和集群场景以提升代码的稳定性和高可用性。

## 二. JMeter 的安装与使用

首先进入 Jmeter 的官网 [https://jmeter.apache.org/](https://jmeter.apache.org/)，如下图所示，在左边 Download 一栏找到 Download Releases 节点并点击。  

![[JMeter 压力测试工具.png]]

在下载页面分别有两个节点，以. tgz 为结尾的是 linux 版本下的, 这里我们选择. zip 为结尾的压缩包进行下载安装。  

![[JMeter 压力测试工具-1.png]]

这里我下载的是 5.2.1 版本，如果执行有问题的朋友建议对比下 jdk 与 JMeter 的版本号。

下载完成后对我们的压缩包进行解压，然后再进入 / bin 目录之下找到 jmeter. bat 并运行。

这里需要说明一点，如果用记事本打开我们会发现在脚本中已经写好环境变量的相关配置所以我们并不需要做更多的操作。如果仍旧无法打开的朋友可以尝试用管理员身份打开脚本。

![[JMeter 压力测试工具-2.png]]

至此安装结束，进入 JMeter 主界面。 

#### 设置中文

**临时设置**

![[Pasted image 20240721094454.png]]

**持久化设置**

打开 *bin/jmeter.properties*，修改 `language=zh_CN`，并保存。

![[Pasted image 20240721094509.png]]

## 三. 使用 JMeter 模拟高并发场景

第一步：通过 JMeter 文件点开新建一个测试计划，快捷键 Ctrl + L  

![[JMeter 压力测试工具-4.png]]

第二步：在测试计划上右键选择添加>线程 (用户)> 线程组  

![[JMeter 压力测试工具-5.png]]

第三步：打开线程组，线程数代表同时并发的用户数 (建议不要太高，计算机性能会受到影响)，Ramp-up 决定多长时间启动所有线程 (如果使用 150 个线程，ramp-up period 是 10 秒，那么 JMeter 用 10 秒使所有 150 个线程启动并运行)，循环次数指对并发重复执行的次数。  

![[JMeter 压力测试工具-6.png]]

第四步：设置完线程相关的参数后，在线程组上右键选择添加 > 取样器 > Http 请求  

![[JMeter 压力测试工具-7.png]]

第五步：在 Http 请求上进行相关的参数设置，同时这里可以添加请求参数。  

![[JMeter 压力测试工具-8.png]]

第六步：配置监听器，配置监听器：察看结果树、聚合报告、生成概要结果、图形结果

![[JMeter 压力测试工具-9.png]]

第七步：启动服务器，并运行 JMeter  

![[JMeter 压力测试工具-10.png]]

测试代码如下

```java
@RestController
@RequestMapping(value = "/jmeter")
public class TestJMeter {

    private Integer count = 100;

    @GetMapping(value = {"/test"})
    public String test() throws InterruptedException {
        if (count <= 0) {
            System.out.println("已经没有count了");
            return "end";
        }
        //模拟网络延时
        Thread.sleep(10);
        System.out.println("现有count数量:" + --count);
        return "ok";
    }
}
```

根据后台打印可知出现了负数情况，模拟高并发成功。  

![[JMeter 压力测试工具-11.png]]

## 四. 使用 JMeter 模拟网络集群场景

这里先放一张简易的集群架构图，以 Nginx 做服务的分发、Redis 作为数据缓存。  

![[JMeter 压力测试工具-12.png]]

首先是 Nginx 做服务分发、负载均衡的一个配置，修改 Nginx 文件夹 conf 目录下的 nginx. conf

```java
http {
	upstream 127.0.0.1{ #这个是在浏览器中访问的地址
	    server 127.0.0.1:8080 weight=1;#Server端1的地址 weight代表权重即分配请求次数的比重
	    server 127.0.0.1:8081 weight=1;#Server端2的地址
	}
    server {
        listen       80;#nginx监听的端口
        server_name  localhost;
        location / {
			proxy_pass http://127.0.0.1;#相对路径
			proxy_set_header X-Forwarded-For $remote_addr;
       		proxy_set_header Host $host;
        }
    }
}
```

然后修改 JMeter 中的访问的端口号为 80 并修改请求路径，请求次数仍然设定为 150 次  

![[JMeter 压力测试工具-13.png]]

同时启动配置好 Redis 的两个项目，分别开启端口 8080 和 8081

测试代码如下

```java
@RestController
@RequestMapping(value = "/jmeter")
public class TestJMeter {
	@Autowired
    private RedisUtil redisUtil;

    @GetMapping(value = {"/testCluster"})
    public String testCluster(HttpServletRequest httpServletRequest) {
        //redisUtil为封装的Redis操作类，可根据自身需要修改
        //这条指令仅key不存在的时候才会设值
        //key value expire超时时间
        redisUtil.setnx("count", "100", 10);
        Integer myCount = Integer.parseInt(redisUtil.get("count"));

        if (myCount.compareTo(0) <= 0) {
            System.out.println("已经没有count了");
            return "end";
        }
        System.out.println(httpServletRequest.getLocalPort() + "现有count数量" + --myCount);
        
        //key value
        redisUtil.set("count", myCount.toString());
        return "ok";
    }
}
```

由于本次设定的请求数为 150 次，理论上每个集群节点会分发到 75 次的请求，设定的总 count 数量为 100 个，可是根据实际运行结果可以看出 count 并没有被消耗完，这里就出现了集群环境下最常见的数据不一致性的问题。具体解决方案可以使用分布式锁，关于分布式锁的内容本文这里就不再过多阐述了。  

![[JMeter 压力测试工具-14.png]]

![[JMeter 压力测试工具-15.png]]

## 五. 聚合报告参数详解

1. Label：每个 JMeter 的 element（例如 HTTP Request）都有一个 Name 属性，这里显示的就是 Name 属性的值   
2. `#Samples`：请求数——表示这次测试中一共发出了多少个请求，如果模拟 10 个用户，每个用户迭代 10 次，那么这里显示 100   
3. Average：平均响应时间——默认情况下是单个 Request 的平均响应时间，当使用了 Transaction Controller 时，以 Transaction 为单位显示平均响应时间   
4. Median：中位数，也就是 50％ 用户的响应时间   
5. 90% Line：90％ 用户的响应时间   
6. Min：最小响应时间   
7. Max：最大响应时间   
8. Error%：错误率——错误请求数/请求总数   
9. Throughput：吞吐量——默认情况下表示每秒完成的请求数（Request per Second），当使用了 Transaction Controller 时，也可以表示类似 LoadRunner 的 Transaction per Second 数   
10. KB/Sec：每秒从服务器端接收到的数据量，相当于 LoadRunner 中的 Throughput/Sec

一般而言，性能测试中我们需要重点关注的数据有： `#Samples` 请求数，Average 平均响应时间，Min 最小响应时间，Max 最大响应时间，Error% 错误率及 Throughput 吞吐量。
