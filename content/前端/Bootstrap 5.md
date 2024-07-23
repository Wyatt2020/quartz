---
created: 2023-01-13 19:17
status: doing
tags:
  - 前端
---

## Bootstrap 5 CDN

```html
<link href="https://www.w3school.com.cn/lib/bs/bootstrap.css" rel="stylesheet">
<script src="https://www.w3school.com.cn/lib/bs/bootstrap.js"></script>
```

## 响应移动设备

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

`width=device-width`  设置了页面的宽度以跟随设备的 screen-width（将因设备而异）

`initial-scale=1`  设置了浏览器首次加载页面时的初始缩放级别

## 表单

### 输入组合

[输入组合（Input group](https://www.bootstrap.cn/doc/read/128.html)

### 控件

#### 文件输入

- [表单控件（Form control）](https://www.bootstrap.cn/doc/read/124.html#file-input)
- [关于 input 的 accept 属性的一些总结 - CSDN博客](https://blog.csdn.net/yuliqi0429/article/details/90445234)

```html
<!-- 默认文件输入 -->
<div class="mb-3">
	<label for="formFile" class="form-label">Default file input example</label>
	<input class="form-control" type="file" id="formFile">
</div>

<!-- 多文件文件输入 -->
<div class="mb-3">
	<label for="formFileMultiple" class="form-label">Multiple files input example</label>
	<input class="form-control" type="file" id="formFileMultiple" multiple>
</div>

<!-- 禁用文件文件输入 -->
<div class="mb-3">
	<label for="formFileDisabled" class="form-label">Disabled file input example</label>
	<input class="form-control" type="file" id="formFileDisabled" disabled>
</div>

<!-- 小文件文件输入 -->
<div class="mb-3">
	<label for="formFileSm" class="form-label">Small file input example</label>
	<input class="form-control form-control-sm" id="formFileSm" type="file">
</div>

<!-- 大文件文件输入 -->
<div>
	<label for="formFileLg" class="form-label">Large file input example</label>
	<input class="form-control form-control-lg" id="formFileLg" type="file">
</div>
```

### 多选与单选

- [复选框](https://www.bootstrap.cn/doc/read/126.html#checks)


## 组件

- [按钮（Buttons）](https://www.bootstrap.cn/doc/read/136.html)
- [面包屑导航（Breadcrumb）](https://www.bootstrap.cn/doc/read/135.html)
- [吐司消息（Toasts）](https://www.bootstrap.cn/doc/read/153.html)

> [!info] 参考链接
>
> - [Bootstrap 5 教程](https://www.w3school.com.cn/bootstrap5/index.asp)
> - [Bootstrap5 中文手册-俺老刘](https://www.bootstrap.cn/doc/book/2.html)
