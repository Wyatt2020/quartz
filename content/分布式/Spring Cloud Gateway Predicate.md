---
created: 2024-07-01 09:55
modified: 2024-07-01 09:55
tags:
  - 分布式
---

**Predicate** 是一种判断逻辑，用于决定一个请求是否应该被匹配到特定的路由规则上。它基于 HTTP 请求的各种属性（如请求方法、路径、标头、参数等）来制定匹配条件。

Route 路由和 Predicate 断言的对应关系：
- **一对多**：一个路由规则可以包含多个断言。
- **同时满足**：如果一个路由规则中有多个断言，则需要同时满足才能匹配。
- **第一个匹配成功**：如果一个请求可以匹配多个路由，则映射第一个匹配成功的路由。

常见的 Predicate 断言配置：

| 名称         | 说明                | 示例                                                                                                       |
| ---------- | ----------------- | -------------------------------------------------------------------------------------------------------- |
| After      | 是某个时间点后的请求        | `- After=2037-01-20T17:42:47.789-07:00[America/Denver]`                                                  |
| Before     | 是某个时间点之前的请求       | `- Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]`                                                  |
| Between    | 是某两个时间点之间的请求      | `- Between=2037-01-20T17:42:47.789-07:00[America/Denver], 2037-01-21T17:42:47.789-07:00[America/Denver]` |
| Cookie     | 请求必须包含某些cookie    | `- Cookie=chocolate, ch.p`                                                                               |
| Header     | 请求必须包含某些header    | `- Header=X-Request-Id, \d+`                                                                             |
| Host       | 请求必须是访问某个host（域名） | `- Host=**.somehost.org,**.anotherhost.org`                                                              |
| Method     | 请求方式必须是指定方式       | `- Method=GET,POST`                                                                                      |
| Path       | 请求路径必须符合指定规则      | `- Path=/red/{segment},/blue/**`                                                                         |
| Query      | 请求参数必须包含指定参数      | `- Query=name,Jack`                                                                                      |
| RemoteAddr | 请求者的ip必须是指定范围     | `- RemoteAddr=192.168.1.1/24`                                                                            |
| Weight     | 权重处理              | `- Weight=100`                                                                                           |