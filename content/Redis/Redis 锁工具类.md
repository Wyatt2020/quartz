---
created: 2022-05-30 09:45
tags:
  - Redis
---

适合单机程序
-   判断锁是否存在和加锁应该同时进行，此程序有瑕疵

```java
@Service("redisLock")
public class RedisLock {
    @Autowired
    public RedisTemplate redisTemplate;

    /**
     * 加锁
     */
    public boolean lock(String key, String value, Long timeout, TimeUnit timeUnit) {
        if (redisTemplate.hasKey(key)) {
            return false;
        }
        redisTemplate.opsForValue().set(key, value, timeout, timeUnit);
        return true;
    }

    /**
     * 释放锁
     */
    public void unlock(String key, String threadId) {
        //同一个线程id才能删除key
        String currentValue = (String)redisTemplate.opsForValue().get(key);
        if (StringUtils.isNotBlank(currentValue) && threadId.equals(currentValue)) {
            redisTemplate.delete(key);
        }
    }
}
```


