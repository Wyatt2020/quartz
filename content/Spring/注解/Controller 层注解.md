---
created: 2023-11-30 15:29
modified: 2023-11-30 15:29
tags:
  - Spring/注解
---

### @Controller
标注一个类为 Controller，注册到 IOC 容器

### @RequestMapping

标注一个类或方法，设置控制器 `uri` 或者方法的 `uri`

```java
public @interface RequestMapping {

	/**
	* path 的别名
	* @RequestMapping("/foo") 等同于 @RequestMapping(path="/foo")
	*/
	@AliasFor("path")  
	String[] value() default {};
	
	@AliasFor("value")  
	String[] path() default {};

	/**
	* HTTP 请求方法
	* 
	* public enum RequestMethod {
	*     GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE
	* }
	*/
	RequestMethod[] method() default {};
}
```

#### @GetMapping

```java
@GetMapping("index")
```
等同于 
```java
@RequestMapping(value="index", method=RequestMethod.GET)
```

类似的注解还有 **@PostMapping**、**@DeleteMapping**、**@PutMapping**、**@PatchMapping**

### @ResponseBody

标注类或者方法，方法会返回 Json 数据，而不是跳转页面

### @RestController

[[Controller 层注解#@Controller|@Controller]] 和 [[Controller 层注解#@ResponseBody|@ResponseBody]] 的结合体

### @RequestBody

> [!tip]-
> [注解 @RequestParam 与 @RequestBody 的使用场景](https://cloud.tencent.com/developer/article/1414464)

接收的参数来自 requestBody 中，即**请求体**。一般用于处理 `Content-Type` 为 ` application/json `、` application/xml ` 等类型的数据。

对于 `application/json` 类型的数据而言，使用注解 `@RequestBody` 可以将 body 里面所有的 Json 数据传到后端。

```java
@PostMapping("/save")  
public Boolean save(@RequestBody SysUser sysUser){...}
```

### @RequestParam

`@RequestParam` 用来处理 `Content-Type` 为 `application/x-www-form-urlencoded` 编码的内容

接收 `url` 的参数，比如 `https://baidu.com/get?pwd=12345`

```java
@GetMapping("/get")  
public String get(@RequestParam(defaultValue = "1", value = "pwd") Integer pwd) {...}
```

`value`：请求参数名（必须配置）
`required`：是否必需，默认为 true，即请求中必须包含该参数，如果没有包含，将会抛出异常
`defaultValue`：默认值，如果设置了该值，required 将自动设为 false，无论你是否配置了 required，配置了什么值，都是 false（可选配置）

### @PathVariable

绑定 [[Controller 层注解#@RequestMapping|@RequestMapping]] 的参数

```java
@GetMapping("/user/{id}")
public void exmaple(@PathVariable Integer id) {…}
```

`name` 和 `value` 定义参数别名

```java
@GetMapping("/user/{i}")
public void exmaple(@PathVariable(value = "i") Integer id) {…}
```