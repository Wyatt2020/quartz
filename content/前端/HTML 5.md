---
created: 2023-01-04 13:30
modified: 2023-08-27 23:40
tags: 前端/HTML
status: doing
---

### HTML 5 新增的表单元素

`<datalist>`：为 `<input>` 元素规定预定义选项列表
- 用户会在他们输入数据时看到预定义选项的下拉列表
- `<input>` 元素的 `list` 属性必须引用 `<datalist>` 元素的 `id` 属性
```html
<input list="browsers">
<datalist id="browsers">
   <option value="Edge">
   <option value="Chrome">
   <option value="Firefox">
   <option value="Safari">
</datalist>
```

`<keygen>`：定义键对生成器字段

`<output>`：定义计算结果

### HTML 5 新增的 input type

- color
- date
- datetime
- datetime-local
- email
- month
- number
- range
- search
- tel
- time
- url
- week

```html
<form>
	<input type="text" value="text">
	<br>
	<input type="email" value="email">
	<br>
	<input type="color" value="color">color
	<br>
	<input type="date" value="2023-01-03">date
	<br>
	<input type="datetime" value="2023-01-03 12:57">datetime
	<br>
	<input type="datetime-local" value="2023-01-03 12:57">datetime-local
	<br>
	<input type="month" value="2023-01">month
	<br>
	<input type="number" value="10" min="5" max="15" step="2">number
	<br>
	<input type="range" value="1" min="1" max="5">range
	<br>
	<input type="search" value="search">
	<br>
	<input type="tel" value="tel">
	<br>
	<input type="time" value="12:57">time
	<br>
	<input type="url" value="url">
	<br>
	<input type="week" value="week">week
</form>
```

### HTML 5 语义元素

HTML5 提供的新语义元素定义了网页的不同部分：

|  语义元素  | 说明                                             |
| :--------: | ------------------------------------------------ |
|   header   | 定义文档或节的页眉                               |
|    main    | 定义文档的主内容                                 |
|    nav     | 定义导航链接                                     |
|  section   | 定义文档中的节                                   |
|  article   | 定义独立的自包含文章                             |
|   aside    | 定义内容之外的内容（比如侧栏）                   |
|   footer   | 定义文档或节的页脚                               |
|  details   | 定义用户能够查看或隐藏的额外细节                 |
|  summary   | 定义 details 元素的可见标题                      |
| figcaption | 定义 figure 元素的标题                           |
|   figure   | 定义自包含内容，比如图示、图表、照片、代码清单等 |
|    mark    | 定义重要的或强调的文本                           |
|    time    | 定义日期/时间                                    |