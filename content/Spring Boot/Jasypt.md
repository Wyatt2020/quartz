---
created: 2023-11-28 17:43
modified: 2023-11-28 17:43
tags:
  - 轮子
---

> [!summary] Jasypt Spring Boot 为 [[Spring Boot]] 应用中的属性值提供加密支持。

> [!github] [jasypt-spring-boot](https://github.com/ulisesbocchio/jasypt-spring-boot)

> [!link]- Reference
> [Spring Boot 集成 Jasypt 对数据库加密以及踩坑](https://zhuanlan.zhihu.com/p/165312886)
> [Spring Boot 项目使用 Jasypt 对配置文件敏感信息加密](https://blog.csdn.net/Mr_chenchen/article/details/113553046)

### 1. 添加 Maven 依赖

```xml
<!-- jasypt -->  
<dependency>  
    <groupId>com.github.ulisesbocchio</groupId>  
    <artifactId>jasypt-spring-boot-starter</artifactId>  
    <version>3.0.5</version>  
</dependency>
```

### 2. 添加 jasypt 密码加密配置

```yaml
# jasypt 密码加密配置
jasypt:
  encryptor:
    # 加密盐值
    password: jasypt
    # 加密算法设置 3.0.0 以后需要加上下面两个配置
    algorithm: PBEWithMD5AndDES
    iv-generator-classname: org.jasypt.iv.NoIvGenerator
```

### 3. 手动加密/解密工具类

```java
import org.jasypt.encryption.pbe.PooledPBEStringEncryptor;
import org.jasypt.encryption.pbe.config.SimpleStringPBEConfig;

public class JasyptUtil {

    /**
     * Jasypt生成加密结果
     * @param password 配置文件中设定的加密盐值
     * @param value 加密值
     * @return
     */
    public static String encyptPwd(String password,String value){
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        encryptor.setConfig(cryptor(password));
        String result = encryptor.encrypt(value);
        return result;
    }

    /**
     * 解密
     * @param password 配置文件中设定的加密盐值
     * @param value 解密密文
     * @return
     */
    public static String decyptPwd(String password,String value){
        PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
        encryptor.setConfig(cryptor(password));
        String result = encryptor.decrypt(value);
        return result;
    }

    public static SimpleStringPBEConfig cryptor(String password){
        SimpleStringPBEConfig config = new SimpleStringPBEConfig();
        config.setPassword(password);
        config.setAlgorithm("PBEWithMD5AndDES");
        config.setKeyObtentionIterations("1000");
        config.setPoolSize("1");
        config.setProviderName("SunJCE");
        config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator");
        config.setStringOutputType("base64");
        return config;
    }


    public static void main(String[] args) {
        // 加密
        String encPwd = encyptPwd("jasypt", "123456");
        // 解密
        String decPwd = decyptPwd("jasypt", encPwd);
        System.out.println(encPwd);
        System.out.println(decPwd);
    }
}
```

### 4. 修改配置中的明文密码

用官方提供的保留字 `ENC`，将加密的密码包裹即可

```yaml
spring:
  datasource:
    url: jdbc:mysql://xx.xx.xx.xx/xxxx?useUnicode=true&characterEncoding=utf8&useSSL=false
    username: root
    password: ENC(加密后的密码)
    driver-class-name: com.mysql.cj.jdbc.Driver
```

### 5. 加密结果与加密盐值隔离

> 加密盐值不应该直接放在代码中，容易造成开发人员泄露数据安全性

服务器中配置，同理将 Java 启动虚拟机参数新增配置，这样我们密码盐值就放在了服务器中，就不会造成加密盐值泄漏了

```
JAVA_OPTS="-Djasypt.encryptor.password=qwera@12345 -Djasypt.encryptor.algorithm=PBEWithMD5AndDES -Djasypt.encryptor.iv-generator-classname=org.jasypt.iv.NoIvGenerator"
```