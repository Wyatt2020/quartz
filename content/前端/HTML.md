---
created: 2023-01-02 22:30
modified: 2023-08-27 23:37
tags:
  - 前端/HTML
cssclasses:
  - embed-strict
---

## 什么是 HTML

- HTML 指的是超文本标记语言 (**H**yper **T**ext **M**arkup **L**anguage)
- HTML 不是一种编程语言，而是一种**标记语言** (markup language)
- 标记语言是一套**标记标签** (markup tag)
- HTML 使用**标记标签**来描述网页

## HTML 元素

- HTML 元素指的是从开始标签（start tag）到结束标签（end tag）的所有代码
- 大多数 HTML 元素可以嵌套
- 没有内容的 HTML 元素被称为空元素。空元素是在开始标签中关闭的，例如 `<br />`
- HTML 标签对大小写不敏感，**推荐小写**
- [HTML 标签参考手册](https://www.w3school.com.cn/tags/index.asp)

## HTML 属性

- 属性和属性值对大小写不敏感，**推荐小写**
- 始终为属性值加引号，通常加双引号
- 如果属性值本身含有双引号，那么属性值必须使用单引号
  `name='Are "You" OK'`
- [HTML 全局属性](https://www.w3school.com.cn/tags/html_ref_standardattributes.asp)

## HTML 样式

- 外部样式表：

```html
<head>
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

- 内部样式表：

```html
<head>
	<style type="text/css">
	body {background-color: red}
	p {margin-left: 20px}
	</style>
</head>
```

- 内联样式（**不建议使用**）：

```html
<p style="color:white;font-size:16px;"></p>
```

## HTML 文档类型

- `<!DOCTYPE>` 用于声明[文档类型](https://www.w3school.com.cn/tags/tag_doctype.asp)
- 常用的声明：

  - **HTML5**：`<!DOCTYPE html>`
  - **HTML 4.01**：

    ```html
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
    "http://www.w3.org/TR/html4/loose.dtd">
    ```

  - **XHTML 1.0**：
    ```html
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    ```

## HTML 文档

- `<html>`

## 头部

`<head>` 定义文档的头部描述了文档的各种属性和信息

- `<title>`：定义文档标题
- `<base>`：为页面上的所有链接规定默认地址或默认目标（target）
  - **href**：规定页面中所有相对链接的基准 URL
  - **[target](HTML.md#^6cee5e)**：在何处打开页面中所有的链接
- `<link>`：最常见的用途是链接样式表
  `<link rel="stylesheet" type="text/css" href="/html/csstest1.css" >`
- `<meta>`：提供有关页面的元数据，元数据不会显示在页面上，但是对于机器是可读的
  `<meta http-equiv="content-type" content="text/html; charset=UTF-8">`
  - 一些搜索引擎会利用 meta 元素的 `name` 和 `content` 属性来索引您的页面
  - **charset**：规定 HTML 文档的字符编码
  - **content**：定义与 `http-equiv` 或 `name` 属性相关的元信息
  - **http-equiv** ：把 `content` 属性连接到 HTTP 头部，可用于模拟 HTTP 响应标头
    - content-security-policy：规定文档的内容策略
    - content-type：规定文档的字符编码
    - default-style：规定要使用的首选样式表
    - refresh：定义文档自我刷新的时间间隔
  - **name**：把 `content` 属性连接到 `name`
    - application-name
    - author
    - description（搜索引擎）
    - generator
    - keywords（搜索引擎）
    - viewport
    - 注意：如果设置了  `http-equiv`  属性，则不应设置  `name`  属性
  - **scheme**：设置或返回用于解释 `content` 属性的值的格式
    `<meta name="created" content="2023-01-03" scheme="YYYY-MM-DD" />`
- `<script>`：定义客户端脚本
  - script 元素既可以包含脚本语句，也可以通过 `src` 属性指向外部脚本文件
  - 必需的 `type` 属性规定脚本的 MIME 类型
  - `charset` 属性用于规定在外部脚本文件中使用的字符编码
- `<style>`：定义样式信息
  - `type` 属性是必需的，唯一可能的值是 "text/css"
  - 提示：如需链接外部样式表，请使用 `<link>`

## 主体

- `<body>`
- **background**：定义背景

## 标题

- 标题（Heading）是通过 `<h1>` - `<h6>` 等标签进行定义的。
- `<h1>` 定义最大的标题。`<h6>` 定义最小的标题。

## 水平线

- `<hr />` 可用于分隔内容

## 注释

- `<!-- 一条注释 -->`

## 段落

- `<p>`
- 浏览器会自动地在段落的前后添加空行。（`<p>` 是块级元素）

## 换行

- `<br />`

## 空格

- `&nbsp;`

## 格式化

- `<b>` **加粗**
- `<i>` _斜体_
- `<sub>` 下标 CO<sub>2</sub>
- `<sup>` 上标 3<sup>2</sup>
- `<pre>` 预格式文本，保留普通的空格符与换行符，很适合显示计算机代码
- `<bdo dir="rtl">` 改变文字输出方向，<bdo dir="rtl">出输左到右从</bdo>
- `<del>` ~~删除线~~
- `<ins>` <u>下划线</u>

## 引用

- `<q>` 短引用，给文本加<q>引号</q>
- `<blockquote>` 长引用，<blockquote>给文本进行缩进处理</blockquote>

## 超链接

- `<a href="www.google.com" target="_blank">`
- **href**：定义链接的目标
- **target**：定义被链接的文档在何处显示 ^6cee5e
  - `_blank` 在新窗口打开
  - `_self` 在当前窗口打开，默认值，可以省略
  - `_parent` 在父框架集中打开被链接文档
  - `_top` 在整个窗口中打开
  - _framename_ 在指定的框架中打开
- **name**：定义锚（anchor）的名称，它对读者是不可见的。
  - 我们可以创建直接跳至该命名锚（比如页面中某个小节）的链接，这样使用者就无需不停地滚动页面来寻找他们需要的信息了。
  - `<a name="top">回顶部</a>`
  - 可以使用 id 属性来替代 name 属性

## 图片

- `<img src="url" alt="新叶喵"/>`
- `<a href="www.google.com"> <img src="/img/google.jpg" /> </a>` 图片链接
- **src**：指定图片的位置
- **alt**：当图片无法加载时，或鼠标悬浮在图片上时，需要显示的文本
- **align**：对齐方式 [bottom(默认)，middle，top，left，right]
- **width**, **height**：图片尺寸
- **usemap**：指定使用的 `<map>`
  - `<map>`：定义图片地图
  - `<area>`：定义图片地图中的可点击区域

## 表格

- `<table>`：表格由 table 标签开始

  - **border**：边框像素
  - **cellpadding**：内容与其边框的边距
  - **cellspacing**：单元格的间距
  - **bgcolor**：背景颜色
  - **background**：背景图
  - **frame**：围绕表格的边框（box，above，below，hsides，vsides）

- `<caption>`：标题
- `<tr>`：行

- `<th>`：表头

  - **colspan**：横跨列数
  - **rowspan**：横跨行数

- `<td>`：表格数据

  - **bgcolor**：背景颜色
  - **background**：背景图
  - **align**：文本水平对齐方式（left，right，center）

- `<col>`：定义表格列的属性，只能在 table 或 colgroup 元素中使用

  - **class**：定义表格列的样式
  - **span**：定义影响的列数，默认为 1
  - **align**：文本水平对齐方式（left，right，center，justify，char）<span style="color:red">HTML 5 不支持</span>
  - **valign**：文本垂直对齐方式（top，middle，bottom，baseline）<span style="color:red">HTML 5 不支持</span>
  - **width**：定义宽度，<span style="color:red">HTML 5 不支持</span>

- `<colgroup>`：对表格中的列进行组合，以便对其进行格式化，只能在 table 元素中使用

  - **class**：定义表格列的样式
  - 其他属性与 col 标签类似

- `<thead>`：页眉
  - 应该与 `<tbody>` 和 `<tfoot>` 结合起来使用，用来规定表格的各个部分
  - 通过使用这些元素，使浏览器有能力支持独立于表格表头和表格页脚的表格主体滚动。
  - 当包含多个页面的长的表格被打印时，表格的表头和页脚可被打印在包含表格数据的每张页面上。
- `<tbody>`：主体
- `<tfoot>`：页脚

```html
<!DOCTYPE html>
<html>
    <head>
        <style>
        table {text-align: center;}
        tr:hover {background-color: tomato;color: #FFF;}
        #style1 {background-color: lawngreen;}
        .style2 {background-color: lightblue;}
        </style>
        <title>Pokemon</title>
    </head>
    <body>
        <table width="500" border="1">
            <caption>宝可梦朱紫御三家</caption>
            <colgroup>
                <col span="2" id="style1" />
                <col class="style2"/>
            </colgroup>
            <thead>
                <tr>
                    <th>草系</th>
                    <th>火系</th>
                    <th>水系</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>新叶喵</td>
                    <td>呆火鳄</td>
                    <td>润水鸭</td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <td>茂盛</td>
                    <td>猛火</td>
                    <td>激流</td>
                </tr>
            </tfoot>
          </table>
    </body>
</html>
```

## 列表

**无序列表**

```
<ul>
	<li>o_o</li>
	<li>呆火鳄</li>
</ul>
```

view

- o_o
- 呆火鳄

**有序列表**

```
<ol>
	<li>额啊</li>
	<li>呆火呆火</li>
</ol>
```

view

1. 额啊
2. 呆火呆火

**自定义列表**

- 自定义列表是项目及其注释的组合。
- 自定义列表以 `<dl>` 开始。每个自定义列表项以 `<dt>` 开始。每个自定义列表项的定义以 `<dd>` 开始。
- 列表项内部可以使用段落、换行符、图片、链接以及其他列表等等。

## 表单

![[HTML 表单.md#`<form>`]]

![[HTML 表单.md#`<input>`]]

![[HTML 表单.md#`<select>`]]

![[HTML 表单.md#`<输入限制>`]]

## 块

**div**

- `<div>` 是块级元素，可以把文档分割为独立的、不同的部分。
- 可用于组合其他 HTML 元素的容器，浏览器会在其前后显示换行。
- 配合 CSS 可用于对大的内容块设置样式属性。

**span**

- `<span>` 是内联元素，用来组合文档中的行内元素。
- 配合 CSS 可用于为部分文本设置样式属性。

## 类

- 如果定义了 HTML 元素的 `class` 属性，可通过 [[CSS 选择器.md#类选择器]]来指定样式
- 类名不能以数字开头
- 一个 HTML 元素可以有使用多个类，多个类名使用空格隔开

## id

- `id`  属性指定 HTML 元素的唯一 ID
- `id`  属性的值区分大小写
- `id` 不能以数字开头
- `id`  属性用于指向样式表中的特定样式声明

```html
<!DOCTYPE html>
<html>
<head>
<style>
#myH1 {
  color: black;
  text-align: center;
}
</style>
</head>
<body>

<h1 id="myH1">My H1</h1>

</body>
</html>
```

- **Class 与 ID 的差异**

  > 同一个类名可以由多个 HTML 元素使用，而一个 id 名称只能由页面中的一个 HTML 元素使用

- **通过 ID 和链接实现 HTML 书签**

  > 首先，用  `id`  属性创建书签：
  > `<h2 id="top">顶部</h2>`
  > 然后，在同一张页面中，向这个书签添加一个链接：
  > `<a href="#top">回到顶部</a>`

- 在 JavaScript 中使用 id 属性
  > JavaScript 可以使用  `getElementById()`  方法访问拥有特定 id 的元素

## Iframe

`iframe（内联框架）` 用于在网页内显示网页

- **src**：定义在 iframe 中显示的文档的 URL
- **width**，**height**：定义 iframe 的宽度和高度
- **name**：定义 iframe 的名称
- **scrolling**：是否在 iframe 中显示滚动条（yes, no, auto）
- 移除边框：`frameborder="0"`
- 使用 iframe 作为链接的目标

```html
<iframe src="demo_iframe.htm" name="iframe_a"></iframe>
<p><a href="www.google.com" target="iframe_a">google</a></p>
```

## 框架

通过使用[HTML 框架](https://www.w3school.com.cn/html/html_frames.asp)，可以在同一个浏览器窗口中显示不止一个页面。

## noscript

定义了替代内容，这些内容将显示给在浏览器中禁用了脚本或浏览器不支持脚本的用户

```html
<script>
document.getElementById("demo").innerHTML = "Hello JavaScript!";
</script>
<noscript>抱歉，您的浏览器不支持 JavaScript！</noscript>
<p>不支持 JavaScript 的浏览器将显示 noscript 元素内的文本。</p>
```

## 计算机代码元素

支持固定的字母尺寸和间距

| 标签 | 说明               |
| :--: | ------------------ |
| code | 定义计算机代码文本 |
| kbd  | 定义键盘文本       |
| samp | 定义计算机代码示例 |
| var  | 定义变量           |
| pre  | 定义预格式化文本   |

## HTML 5 语义元素

![[HTML 5#HTML 5 语义元素]]

## HTML 字符实体

HTML 中的预留字符必须被替换为字符实体

- [HTML ISO-8859-1 参考手册](https://www.w3school.com.cn/charsets/ref_html_8859.asp)
- [UTF-8 数学运算符](https://www.w3school.com.cn/charsets/ref_utf_math.asp)
- [UTF-8 希腊文和科普特文](https://www.w3school.com.cn/charsets/ref_utf_greek.asp)
- [UTF-8 货币符号](https://www.w3school.com.cn/charsets/ref_utf_currency.asp)
- [UTF-8 箭头](https://www.w3school.com.cn/charsets/ref_utf_arrows.asp)
- [UTF-8 杂项符号](https://www.w3school.com.cn/charsets/ref_utf_symbols.asp)

常用符号表

| 符号 | 实体名称   |
| :--: | ---------- |
| 空格 | `&nbsp;`   |
|  <   | `&lt;`     |
|  >   | `&gt;`     |
|  &   | `&amp;`    |
|  "   | `&quot;`   |
|  '   | `&apos;`   |
|  ￠  | `&cent;`   |
|  £   | `&pound;`  |
|  ¥   | `&yen;`    |
|  €   | `&euro;`   |
|  §   | `&sect;`   |
|  ©   | `&copy;`   |
|  ®   | `&reg;`    |
|  ™   | `&trade;`  |
|  ×   | `&times;`  |
|  ÷   | `&divide;` |

## 在 HTML 中使用表情符号

表情符号（Emoji）是来自 UTF-8 字符集的字符，为了正确显示 HTML 页面，Web 浏览器必须知道页面中使用的字符集。可以在  `<meta>`  标签中规定字符集：
`<meta charset="UTF-8">`

如果未规定，UTF-8 则是 HTML 中的默认字符集。

使用 `&#` + `字符编号` + `;` 来显示字符：

- 😄 是 `&#128516;`
- 😍 是 `&#128525;`
- 💗 是 `&#128151;`

## URL 字符编码

URL 只能使用  [ASCII 字符集](https://www.w3school.com.cn/tags/html_ref_ascii.asp "HTML ASCII 参考手册")来通过因特网进行发送。

由于 URL 常常会包含 ASCII 集合之外的字符，URL 必须转换为有效的 ASCII 格式。

URL 编码使用 "%" 其后跟随两位的十六进制数来替换非 ASCII 字符。

URL 不能包含空格。URL 编码通常使用 + 来替换空格。

## 代码规范

- [HTML 5 样式指南和代码约定](https://www.w3school.com.cn/html/html5_syntax.asp)
- 始终在文档的首行声明文档类型 `<!DOCTYPE html>`
- 使用两个空格缩进
- `script` 和 `link` 标签可以省略 `type` 属性
- 使用小写的文件名，属性名，元素名
- =号两边不要空格
- 始终对图像使用  `alt`  属性，当图像无法显示时该属性很重要
- 属性值加引号

> [!info] 参考链接
>
> - [HTML 教程 - W3school](https://www.w3school.com.cn/html/index.asp)
> - [HTML 教程 - 菜鸟教程](https://www.runoob.com/html/html-tutorial.html)
