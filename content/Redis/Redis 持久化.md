---
created: 2022-08-27 15:58
modified: 2023-08-27 22:43
tags: Redis
---

## 为什么需要持久化

Redis 是个基于内存的数据库。那服务一旦宕机，内存中的数据将全部丢失。通常的解决方案是从后端数据库恢复这些数据，但后端数据库有性能瓶颈，如果是大数据量的恢复，1、会对数据库带来巨大的压力，2、数据库的性能不如 Redis。导致程序响应慢。所以对 Redis 来说，实现数据的持久化，避免从后端数据库中恢复数据，是至关重要的。

## RDB

RDB 就是 Redis DataBase 的缩写，中文名为快照/内存快照。RDB 持久化是把当前进程数据生成快照保存到磁盘上的过程，由于是某一时刻的快照，那么快照中的值要早于或者等于内存中的值。

### 手动触发

- save 命令：阻塞当前 Redis 服务器，直到 RDB 过程完成为止，该命令将在 redis.cnf#dir 目录中创建 dump.rdb 文件，对于内存比较大的实例会造成长时间阻塞，线上环境不建议使用
- bgsave 命令：Redis 进程执行 fork 操作创建子进程，RDB 持久化过程由子进程负责，完成后自动结束。阻塞只发生在 fork 阶段，一般时间很短
![](202208271600055.png)

具体流程：
1. redis 客户端执行 bgsave 命令或者自动触发 bgsave 命令
2. 主进程判断当前是否已经存在正在执行的子进程，如果存在，那么主进程直接返回
3. 如果不存在正在执行的子进程，那么就 fork 一个新的子进程进行持久化数据，fork 过程是阻塞的，fork 操作完成后主进程即可执行其他操作
4. 子进程先将数据写入到临时的 rdb 文件中，待快照数据写入完成后再原子替换旧的 rdb 文件
5. 同时发送信号给主进程，通知主进程 rdb 持久化完成，主进程更新相关的统计信息（info Persitence 下的 `rdb_*` 相关选项）

### 自动触发

在以下 4 种情况时会自动触发：
1. redis.conf 中配置 save m n，即在 m 秒内有 n 次修改时，自动触发 bgsave 生成 rdb 文件；
2. 主从复制时，从节点要从主节点进行全量复制时也会触发 bgsave 操作，生成当时的快照发送到从节点；
3. 执行 debug reload 命令重新加载 redis 时也会触发 bgsave 操作；
4. 默认情况下执行 shutdown 命令时，如果没有开启 aof 持久化，那么也会触发 bgsave 操作。

### redis.conf 中配置 RDB

```properties
# 周期性执行条件的设置格式为
save <seconds> <changes>

# 默认的设置为：
save 900 1
save 300 10
save 60 10000

# 以下设置方式为关闭 RDB 快照功能
save ""
```

以上三项默认信息设置代表的意义是：
- 如果 900 秒内有 1 个 Key 发生变化，则保存快照；
- 如果 300 秒内有 10 个 Key 发生变化，则保存快照；
- 如果 60 秒内有 10000 个 Key 发生变化，则保存快照。

### 其他配置

```properties
# 文件名称
dbfilename dump.rdb

# 文件保存路径
dir /home/work/app/redis/data/

# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes

# 是否压缩
rdbcompression yes

# 导入时是否检查
rdbchecksum yes
```

**dbfilename**：RDB 文件在磁盘上的名称。

**dir**：RDB 文件的存储路径。默认设置为“./”，也就是 Redis 服务的主目录。

**stop-writes-on-bgsave-error**：上文提到的在快照进行过程中，主进程照样可以接受客户端的任何写操作的特性，是指在快照操作正常的情况下。如果快照操作出现异常（例如操作系统用户权限不够、磁盘空间写满等等）时，Redis 就会禁止写操作。这个特性的主要目的是使运维人员在第一时间就发现 Redis 的运行错误，并进行解决。一些特定的场景下，您可能需要对这个特性进行配置，这时就可以调整这个参数项。该参数项默认情况下值为 yes，如果要关闭这个特性，指定即使出现快照错误 Redis 一样允许写操作，则可以将该值更改为 no。

**rdbcompression**：该属性将在字符串类型的数据被快照到磁盘文件时，启用 LZF 压缩算法。Redis 官方的建议是请保持该选项设置为 yes，因为“it’s almost always a win”。

**rdbchecksum**：从 RDB 快照功能的 version 5 版本开始，一个 64 位的 CRC 冗余校验编码会被放置在 RDB 文件的末尾，以便对整个 RDB 文件的完整性进行验证。这个功能大概会多损失 10% 左右的性能，但获得了更高的数据可靠性。所以如果您的 Redis 服务需要追求极致的性能，就可以将这个选项设置为 no。

### RDB 优缺点

**优点**
- RDB 文件是是经过压缩的二进制文件，占用空间很小，它保存了 Redis 某个时间点的数据集，适用于备份、全量复制等场景；
- RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快；
- RDB 可以最大化 Redis 的性能。父进程在保存 RDB 文件时唯一要做的就是 fork 出一个子进程，然后这个子进程就会处理接下来的所有保存工作，父进程无须执行任何磁盘 I/O 操作。

**缺点**
- RDB 方式实时性不够，无法做到秒级的持久化；
- 每次调用 bgsave 都需要 fork 子进程，fork 子进程属于重量级操作，频繁执行成本较高；
- RDB 文件是二进制的，没有可读性，AOF 文件在了解其结构的情况下可以手动修改或者补全；
- Linux fork 子进程采用的是 copy-on-write 的方式。在 Redis 执行 RDB 持久化期间，如果 client 写入数据很频繁，那么将增加 Redis 占用的内存，最坏情况下，内存的占用将达到原先的 2 倍。刚 fork 时，主进程和子进程共享内存，但是随着主进程需要处理写操作，主进程需要将修改的页面拷贝一份出来，然后进行修改。极端情况下，如果所有的页面都被修改，则此时的内存占用是原先的 2 倍。

## AOF

Redis 是“写后”日志，Redis 先执行命令，把数据写入内存，然后才记录日志。日志里记录的是 Redis 收到的每一条命令，这些命令是以文本形式保存。

![](202208271605836.png)

### 为什么采用写后日志

Redis 要求高性能，采用写日志有两方面好处：
1. **避免额外的检查开销**。Redis 在向 AOF 里面记录日志的时候，并不会先去对这些命令进行语法检查。所以，如果先记日志再执行命令的话，日志中就有可能记录了错误的命令，Redis 在使用日志恢复数据时，就可能会出错。
2. **不会阻塞当前的写操作**。

但这种方式存在潜在风险：
1. 如果命令执行完成，写日志之前宕机了，会丢失数据。
2. 主线程写磁盘压力大，导致写盘慢，阻塞后续操作。

### 如何实现 AOF

AOF 日志记录 Redis 的每个写命令，步骤分为：命令追加（append）、文件写入（write）和文件同步（sync）
1. 命令追加：当 AOF 持久化功能打开了，服务器在执行完一个写命令之后，会以协议格式将被执行的写命令追加到服务器的 aof_buf 缓冲区。
2. 文件写入和同步：关于何时将 aof_buf 缓冲区的内容写入 AOF 文件中，Redis 提供了三种写回策略：
![](202208271609182.png)
**Always**（同步写回）：每个写命令执行完，立马同步将日志写回磁盘；
**Everysec**（每秒写回）：每个写命令执行完，只是先把日志写到 AOF 文件的内存缓冲区，每隔一秒把缓冲区中的内容写入磁盘；
**No**（操作系统控制的写回）：每个写命令执行完，只是先把日志写到 AOF 文件的内存缓冲区，由操作系统决定何时将缓冲区内容写回磁盘。

### redis.conf 中配置 AOF

```properties
# appendonly参数开启AOF持久化
appendonly no

# AOF持久化的文件名，默认是appendonly.aof
appendfilename "appendonly.aof"

# AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的
dir ./

# 同步策略
# appendfsync always
appendfsync everysec
# appendfsync no

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof出错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```

**appendonly**：默认情况下 AOF 功能是关闭的，将该选项改为 yes 以便打开 Redis 的 AOF 功能。

**appendfilename**：AOF 文件的名字。

**appendfsync**：这个参数项是 AOF 功能最重要的设置项之一，主要用于设置“真正执行”操作命令向 AOF 文件中同步的策略。
什么叫“真正执行”呢？还记得 Linux 操作系统对磁盘设备的操作方式吗？ 为了保证操作系统中 I/O 队列的操作效率，应用程序提交的 I/O 操作请求一般是被放置在 Linux Page Cache 中的，然后再由 Linux 操作系统中的策略自行决定正在写到磁盘上的时机。而 Redis 中有一个 fsync()函数，可以将 Page Cache 中待写的数据真正写入到物理设备上，而缺点是频繁调用这个 fsync()函数干预操作系统的既定策略，可能导致 I/O 卡顿的现象频繁 。

appendfsync 参数项可以设置三个值：
- always：每处理一个命令都将 aof_buf 缓冲区中的所有内容写入并同步到 AOF 文件，即每个命令都刷盘。
- everysec（默认）：将 aof_buf 缓冲区中的所有内容写入到 AOF 文件，如果上次同步 AOF 文件的时间距离现在超过一秒钟， 那么再次对 AOF 文件进行同步， 并且这个同步操作是异步的，由一个后台线程专门负责执行，即每秒刷盘 1 次。
- no：将 aof_buf 缓冲区中的所有内容写入到 AOF 文件， 但并不对 AOF 文件进行同步， 何时同步由操作系统来决定。即不执行刷盘，让操作系统自己执行刷盘。

**no-appendfsync-on-rewrite**：always 和 everysec 的设置会使真正的 I/O 操作高频度的出现，甚至会出现长时间的卡顿情况，这个问题出现在操作系统层面上，所有靠工作在操作系统之上的 Redis 是没法解决的。为了尽量缓解这个情况，Redis 提供了这个设置项，保证在完成 fsync 函数调用时，不会将这段时间内发生的命令操作放入操作系统的 Page Cache（这段时间 Redis 还在接受客户端的各种写操作命令）。

**auto-aof-rewrite-percentage**：上文说到在生产环境下，技术人员不可能随时随地使用“BGREWRITEAOF”命令去重写 AOF 文件。所以更多时候我们需要依靠 Redis 中对 AOF 文件的自动重写策略。Redis 中对触发自动重写 AOF 文件的操作提供了两个设置：auto-aof-rewrite-percentage 表示如果当前 AOF 文件的大小超过了上次重写后 AOF 文件的百分之多少后，就再次开始重写 AOF 文件。例如该参数值的默认设置值为 100，意思就是如果 AOF 文件的大小超过上次 AOF 文件重写后的 1 倍，就启动重写操作。

**auto-aof-rewrite-min-size**：参考 auto-aof-rewrite-percentage 选项的介绍，auto-aof-rewrite-min-size 设置项表示启动 AOF 文件重写操作的 AOF 文件最小大小。如果 AOF 文件大小低于这个值，则不会触发重写操作。注意，auto-aof-rewrite-percentage 和 auto-aof-rewrite-min-size 只是用来控制 Redis 中自动对 AOF 文件进行重写的情况，如果是技术人员手动调用“BGREWRITEAOF”命令，则不受这两个限制条件左右。

### AOF 的优缺点

**优点**
1. AOF 比 RDB 可靠。你可以设置不同的 fsync 策略：no、everysec 和 always。默认是 everysec，在这种配置下，Redis 仍然可以保持良好的性能，即使出现系统崩溃，最多只会丢失一秒之内产生的数据。
2. AOF 文件是一个纯追加的日志文件。即使日志因为某些原因而包含了未写入完整的命令（比如写入时磁盘已满，写入中途停机等等）， 我们也可以使用 redis-check-aof 工具也可以轻易地修复这种问题。
3. 当 AOF 文件太大时，Redis 会自动在后台进行重写：重写后的新 AOF 文件包含了恢复当前数据集所需的最小命令集合。整个重写是绝对安全，因为重写是在一个新的文件上进行，同时 Redis 会继续往旧的文件追加数据。当新文件重写完毕，Redis 会把新旧文件进行切换，然后开始把数据写到新文件上。
4. AOF 文件有序地保存了对数据库执行的所有写入操作以 Redis 协议的格式保存， 因此 AOF 文件的内容非常容易被人读懂， 对文件进行分析也很轻松。如果你不小心执行了 FLUSHALL 命令，把所有数据刷掉了，但只要 AOF 文件没有被重写，那么只需停止服务器， 移除 AOF 文件末尾的 FLUSHALL 命令， 并重启 Redis ， 就可以将数据集恢复到 FLUSHALL 执行之前的状态。

**缺点**
1. 对于相同的数据集，AOF 文件的大小一般会比 RDB 文件大。
2. 根据所使用的 fsync 策略，AOF 的速度可能会比 RDB 慢。通常 fsync 设置为每秒一次就能获得比较高的性能，而关闭 fsync 可以让 AOF 的速度和 RDB 一样快。
3. AOF 在过去曾经发生过这样的 bug ：因为个别命令的原因，导致 AOF 文件在重新载入时，无法将数据集恢复成保存时的原样。（举个例子，阻塞命令 BRPOPLPUSH 就曾经引起过这样的 bug ） 。虽然这种 bug 在 AOF 文件中并不常见， 但是相较而言，RDB 几乎是不可能出现这种 bug 的。

## 混合持久化

混合持久化并不是一种全新的持久化方式，而是对已有方式的优化。混合持久化只发生于 AOF 重写过程。使用了混合持久化，重写后的新 AOF 文件前半段是 RDB 格式的全量数据，后半段是 AOF 格式的增量数据。

整体格式为：`[RDB file][AOF tail]`

**开启**：混合持久化的配置参数为 aof-use-rdb-preamble，配置为 yes 时开启混合持久化，在 redis 4 刚引入时，默认是关闭混合持久化的，但是在 redis 5 中默认已经打开了。

**关闭**：使用 aof-use-rdb-preamble no 配置即可关闭混合持久化。

混合持久化本质是通过 AOF 后台重写（bgrewriteaof 命令）完成的，不同的是当开启混合持久化时，fork 出的子进程先将当前全量数据以 RDB 方式写入新的 AOF 文件，然后再将 AOF 重写缓冲区（aof_rewrite_buf_blocks）的增量命令以 AOF 方式写入到文件，写入完成后通知主进程将新的含有 RDB 格式和 AOF 格式的 AOF 文件替换旧的的 AOF 文件。

**优点**：结合 RDB 和 AOF 的优点, 更快的重写和恢复。

**缺点**：AOF 文件里面的 RDB 部分不再是 AOF 格式，可读性差。

## 参考链接

> [!info] 
> - [Redis进阶 - 持久化：RDB和AOF机制详解](https://www.pdai.tech/md/db/nosql-redis/db-redis-x-rdb-aof.html)

