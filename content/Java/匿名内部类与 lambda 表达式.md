---
created: 2023-12-04 14:20
modified: 2023-12-04 14:20
tags:
  - Java
---

2021-10-28

> [!question] 今天看别人的代码，创建一个 bean 没看懂，只看出来用了 lambda 表达式。我有几个疑惑：
> 1. 这个方法只注入了 `jwtTokenGenerator` ，那 `request`, `response`, `authentication` 这几个参数是谁传的？
> 2. 点进 `AuthenticationSuccessHandler` 看到里面定义了一个 `void onAuthenticationSuccess(HttpServletRequest var1, HttpServletResponse var2, Authentication var3)`，参数列表与上面的一致，说明这段代码实现了 `onAuthenticationSuccess()`。但这里明显是 *return* 一个 `AuthenticationSuccessHandler` 对象啊，`onAuthenticationSuccess()` 又没有返回值，为啥可以这么写呢？
> 3. 知道了通过这段代码可以 *new* 一个 `AuthenticationSuccessHandler` ，那什么情况下可以模仿他这样子写呢？
> 	- **Lambda 规定接口中只能有一个需要被实现的方法**

lambda 表达式写法

```java
@Bean
public AuthenticationSuccessHandler authenticationSuccessHandler(JwtTokenGenerator jwtTokenGenerator) {
    return (request, response, authentication) -> {
        if (response.isCommitted()) {
            log.debug("Response has already been committed");
            return;
        }
        Map<String, Object> map = new HashMap<>(5);
        map.put("time", LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
        map.put("flag", "success_login");
        User principal = (User) authentication.getPrincipal();

        String username = principal.getUsername();
        Collection<GrantedAuthority> authorities = principal.getAuthorities();
        Set<String> roles = new HashSet<>();
        if (CollectionUtil.isNotEmpty(authorities)) {
            for (GrantedAuthority authority : authorities) {
                String roleName = authority.getAuthority();
                roles.add(roleName);
            }
        }

        JwtTokenPair jwtTokenPair = jwtTokenGenerator.jwtTokenPair(username, roles, null);

        map.put("access_token", jwtTokenPair.getAccessToken());
        map.put("refresh_token", jwtTokenPair.getRefreshToken());

        ResponseUtil.responseJsonWriter(response, RestBody.okData(map, "登录成功"));
    };
}
```



匿名内部类的写法

```java
@Bean
public AuthenticationSuccessHandler authenticationSuccessHandler(JwtTokenGenerator jwtTokenGenerator) {
    return new AuthenticationSuccessHandler() {
        @Override
        public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
            if (response.isCommitted()) {
                log.debug("Response has already been committed");
                return;
            }
            Map<String, Object> map = new HashMap<>(5);
            map.put("time", LocalDateTime.now().format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")));
            map.put("flag", "success_login");
            User principal = (User) authentication.getPrincipal();

            String username = principal.getUsername();
            Collection<GrantedAuthority> authorities = principal.getAuthorities();
            Set<String> roles = new HashSet<>();
            if (CollectionUtil.isNotEmpty(authorities)) {
                for (GrantedAuthority authority : authorities) {
                    String roleName = authority.getAuthority();
                    roles.add(roleName);
                }
            }

            JwtTokenPair jwtTokenPair = jwtTokenGenerator.jwtTokenPair(username, roles, null);

            map.put("access_token", jwtTokenPair.getAccessToken());
            map.put("refresh_token", jwtTokenPair.getRefreshToken());

            ResponseUtil.responseJsonWriter(response, RestBody.okData(map, "登录成功"));
        }
    };
}
```



> [!link]- Reference
> [Java 匿名内部类与 Lambda 表达式](https://www.cnblogs.com/chaoaishow/p/12307195.html)
