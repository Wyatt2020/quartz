---
created: 2022-05-29 09:29
tags:
  - Redis
---
## 过期策略

### 定时删除
Redis 默认是每隔 100ms 就**随机抽取一些设置了过期时间的 key**，检查其是否过期，如果过期就删除。

### 惰性删除
并非是 key 到期了就会被自动删除，而是每次获取 key 时，才检査 key 是否过期，如果过期就删除。

## 内存淘汰策略
当 Redis 的内存空间（maxmemory 参数配置）已经用满时，Redis 将根据配置的淘汰策略（maxmemory-policy 参数配置），进行相应的动作。

- **noeviction**：**默认策略**，不淘汰任何 key，直接返回错误
- **allkeys-lru**：在所有的 key 中，使用 LRU 算法淘汰部分 key
- **allkeys-lfu**：在所有的 key 中，使用 LFU 算法淘汰部分 key（Redis 4.0 新增）
- **allkeys-random**：在所有的 key 中，随机淘汰部分 key
- **volatile-lru**：在设置了过期时间的 key 中，使用 LRU 算法淘汰部分 key
- **volatile-lfu**：在设置了过期时间的 key 中，使用 LFU 算法淘汰部分 key（Redis 4.0 新增）
- **volatile-random**：在设置了过期时间的 key 中，随机淘汰部分 key
- **volatile-ttl**：在设置了过期时间的 key 中，挑选 TTL（time to live，剩余时间）短的 key 淘汰

**LRU**：淘汰最长时间没有被使用的
**LFU**：淘汰一段时间内，使用次数最少的

## 手动实现一个 LRU 算法

### 原理
将原先的 key-val 再次封装成一个 node。让整体形成一个双链表。然后维护头结点和尾节点，以及一个阈值。 当一个 key 被查询的时候，就将这个 key 从原来的地方删除，然后再次插入到尾节点处。 如果在添加 key 的时候，发现内存达到了阈值，那么就删除头结点的 key。（因为最近调用的节点都重新插入链尾了，在链头结点的就是最近最少使用的 key） 这样就保证了每次淘汰都是最旧的数据。而热点数据可以长时间存在。

```java
public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int CACHE_SIZE;
    //这里就是传递进来最多能缓存多少数据
    public LRUCache(int cacheSize) {
        //设置一个hashmap 的初始大小，最后一个true指的是让linkedhashmap
        //按照访问顺序来进行排序，最近访问的放在头，最老访问的就在尾
        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true); 
        CACHE_SIZE = cacheSize;
    }
    
    @Override
    protected boolean removeEldestEntry(Map.Entry eldest) {
        //当map中的数据量大于指定的缓存个数的时，自动删除最老的数据
        return size() > CACHE_SIZE; 
    }
}   
```
![](202205290934621.png)

## 参考链接
> [!info] 
> - [《吊打面试官》系列-Redis常见面试题](https://mp.weixin.qq.com/s/R1TJMo2IbPUUMox9OAAafQ)
> - [Redis 的过期策略能介绍一下？要不你再手写一个LRU？](https://apppukyptrl1086.pc.xiaoe-tech.com/detail/v_5d2f48ee591ac_nLDCAAEE/3?from=p_5d3114935b4d7_CEcL8yMS&type=6)

