---
created: 2023-01-04 14:01
tags:
  - 前端/CSS
aliases:
---

CSS 选择器用于查找要设置样式的 HTML 元素

我们可以将 CSS 选择器分为五类：

- 简单选择器（根据名称、id、类来选取元素）
- [组合器选择器](https://www.w3school.com.cn/css/css_combinators.asp "CSS 组合器")（根据它们之间的特定关系来选取元素）
- [伪类选择器](https://www.w3school.com.cn/css/css_pseudo_classes.asp "CSS 伪类")（根据特定状态选取元素）
- [伪元素选择器](https://www.w3school.com.cn/css/css_pseudo_elements.asp "CSS 伪元素")（选取元素的一部分并设置其样式）
- [属性选择器](https://www.w3school.com.cn/css/css_attribute_selectors.asp "CSS 属性选择器")（根据属性或属性值来选取元素）

## 简单选择器

### 元素选择器

根据元素名称来选择 HTML 元素

```css
table {text-align: center;color: blue;}

p {color: red;}
```

### id 选择器

`#` + `元素 id`：根据 HTML 元素的 `id` 属性来选择 HTML 元素

```css
#btn1 {color: red;}
```

### 类选择器

`.` + `类名` ：选择所有 `class="类名"` 的 HTML 元素

`元素名` + `.` + `类名` ：选择特定的 HTML 元素

```html
<!DOCTYPE html>
<html>
<head>
<style>
p.center {text-align: center;color: red;}

p.large {font-size: 300%;}
</style>
</head>
<body>

<h1 class="center">这个标题不受影响</h1>
<p class="center">本段将是红色并居中对齐。</p>
<p class="center large">本段将是红色、居中对齐，并使用大字体。</p>

</body>
</html>
```

### 通用选择器

`*`：选择页面上的所有的 HTML 元素

```css
* {text-align: center;}
```

### 分组选择器

具有相同样式定义的 HTML 元素，最好对选择器进行分组，以最大程度地缩减代码

```css
h1, h2, p {text-align: center;color: red;}
```

## 组合选择器

组合器是解释选择器之间关系的某种机制

CSS 中有四种不同的组合器：

- 后代选择器 (`空格`)
- 子选择器 (`>`)
- 相邻兄弟选择器 (`+`)
- 通用兄弟选择器 (`~`)

### 后代选择器

匹配属于指定元素后代的所有元素

> [!example] 例子：选择 `<div>` 元素内的所有 `<p>` 元素
> 
> ```css
> div p {background-color: yellow;}
> ```

```html
<!DOCTYPE html>
<html>
<head>
<style>
div p {background-color: yellow;}
</style>
</head>
<body>

<h1>后代选择器</h1>
<p>后代选择器匹配作为指定元素后代的所有元素。</p>

<div>
  <p>div 中的段落 1。</p>    <!-- 受影响 -->
  <section><p>div 中的段落 2。</p></section>   <!-- 受影响 -->
</div>

<p>段落 3。不在 div 中。</p>

</body>
</html>
```

### 子选择器

匹配**父元素是指定元素**的所有元素

> [!example] 例子：选择父元素是 `<div>` 元素的所有 `<p>` 元素
> 
> ```css
> div > p {background-color: yellow;}
> ```

```html
<!DOCTYPE html>
<html>
<head>
<style>
div > p {background-color: yellow;}
</style>
</head>
<body>

<h1>子选择器</h1>
<p>子选择器 (>) 选择属于指定元素子元素的所有元素。</p>

<div>
  <p>div 中的段落 1。</p>        <!-- 受影响 -->
  <section><p>div 中的段落 2。</p></section> <!-- 父元素是<section>，不受影响 -->
</div>

<p>段落 3。不在 div 中。</p>

</body>
</html>
```

### 相邻兄弟选择器

匹配所有紧随指定元素的同级元素

兄弟（同级）元素必须具有相同的父元素，“相邻”的意思是**紧随其后**

> [!example] 例子：选择紧随 `<div>` 元素之后的所有 `<p>` 元素
> 
> ```css
> div + p {background-color: yellow;}
> ```

```html
<!DOCTYPE html>
<html>
<head>
<style>
div + p {background-color: yellow;}
</style>
</head>
<body>

<h1>相邻兄弟选择器</h1>
<p>相邻的同胞选择器（+）选择所有作为指定元素的相邻的同级元素。</p>

<div>
  <p>div 中的段落 1。</p>
  <p>div 中的段落 2。</p>
</div>

<p>段落 3。不在 div 中。</p> <!-- 紧随 div 其后，受影响 -->
<p>段落 4。不在 div 中。</p>

</body>
</html>
```

### 通用兄弟选择器

匹配指定元素之后且同级的所有元素

> [!example] 例子：选择前面有 `<div>` 元素且同级的所有 `<p>` 元素
> 
> ```css
> div ~ p {background-color: yellow;}
> ```

```html
<!DOCTYPE html>
<html>
<head>
<style>
div ~ p {background-color: yellow;}
</style>
</head>
<body>

<h1>通用兄弟选择器</h1>
<p>通用的兄弟选择器（~）选择指定元素的所有同级元素。</p>

<p>段落 1。</p>

<div>
  <p>段落 2。</p>
</div>

<p>段落 3。</p>   <!-- 受影响 -->
<code>一些代码。</code>
<p>段落 4。</p>   <!-- 受影响 -->

</body>
</html>
```

## 伪类选择器

伪类用于定义元素的特殊状态

例如：

- 设置鼠标悬停在元素上时的样式
- 为已访问和未访问链接设置不同的样式
- 设置元素获得焦点时的样式

语法：

```css
selector:pseudo-class {
  property: value;
}
```

> [!info] [所有 CSS 伪类](https://www.w3school.com.cn/css/css_pseudo_classes.asp)

### 锚伪类

> [!tip] 注意
> - `a:hover`  必须在  `a:link`  和  `a:visited`  之后才能生效
> - `a:active`  必须在  `a:hover`  之后才能生效
> - 伪类名称对大小写不敏感

```css
a:link {color:#FF0000;} /* 未访问的链接 */
a:visited {color:#00FF00;} /* 已访问的链接 */
a:hover {color:#FF00FF;} /* 鼠标悬停在链接上 */
a:active {color:#0000FF;} /* 已选择的链接 */
```

### 伪类与 CSS 类结合使用

```html
<style>
a.red:visited {color:#FF0000;}
</style>

<!-- 如果链接已被访问，它会显示为红色 -->
<a class="red" href="css-syntax.html">CSS 语法</a>
```

### 简单的提示悬停

把鼠标悬停到 `<div>` 元素以显示 `<p>` 元素

```html
<!DOCTYPE html>
<html>
<head>
<style>
p {display: none;}

div:hover p {display: block;}
</style>
</head>
<body>

<div>答案：
  <p>略</p>
</div>

</body>
</html>
```

### :first-child()

使用 `:first-child` 伪类来选择父元素的第一个子元素

> [!tip] 注意
> 在 IE 8 的之前版本必须声明 `<!DOCTYPE>`，这样 `:first-child` 才能生效

**例子 1**：择器匹配作为任何元素的第一个子元素的任何 `<p>` 元素

```html
<!DOCTYPE html>
<html>
<head>
<style>
p:first-child {color: blue;}
</style>
</head>
<body>

<p>这是一段文本。</p>   <!-- 受影响 -->

<p>这是一段文本。</p>

</body>
</html>
```

**例子 2**：选择器匹配所有 `<p>` 元素中的第一个 `<i>` 元素

```html
<!DOCTYPE html>
<html>
<head>
<style>
p i:first-child {color: blue;}
</style>
</head>
<body>

<p>我是一个<i>强壮</i>的男人。我是一个<i>强壮</i>的男人。</p>   <!-- 第一个“强壮”受影响 -->

<p>我是一个<i>强壮</i>的男人。我是一个<i>强壮</i>的男人。</p>   <!-- 第一个“强壮”受影响 -->

</body>
</html>
```

**例子 3**：匹配所有首个 `<p>` 元素中的所有 `<i>` 元素

```html
<!DOCTYPE html>
<html>
<head>
<style>
p:first-child i {color: blue;} 
</style>
</head>
<body>

<p>我是一个<i>强壮</i>的男人。我是一个<i>强壮</i>的男人。</p>   <!-- 受影响 -->

<p>我是一个<i>强壮</i>的男人。我是一个<i>强壮</i>的男人。</p>

</body>
</html>
```

### :lang()

允许为不同的语言定义特殊的规则

例子：给属性为 `lang="en"` 的 `<q>` 元素定义引号

```html
<!DOCTYPE html>
<html>
<head>
<style>
q:lang(en) {quotes: "~" "~";}
</style>
</head>
<body>

<p>Some text <q lang="en">A quote in a paragraph</q> Some text.</p>
<!-- Some text ~A quote in a paragraph~ Some text. -->
</body>
</html>
```

### :is()

### :not()

### :hover()

## 伪元素选择器

CSS 伪元素用于设置元素指定部分的样式

例如：

-   设置元素的首字母、首行的样式
-   在元素的内容之前或之后插入内容

语法：

```css
selector::pseudo-element {
  property: value;
}
```

### ::first-line 伪元素

`::first-line` 伪元素用于向文本的首行添加特殊样式

例子：为所有 `<p>` 元素中的首行添加样式

```css
p::first-line {
  color: #ff0000;
  font-variant: small-caps;
}
```

`::first-line` 伪元素只能应用于块级元素

以下属性适用于 `::first-line` 伪元素：

-   字体属性
-   颜色属性
-   背景属性
-   word-spacing
-   letter-spacing
-   text-decoration
-   vertical-align
-   text-transform
-   line-height
-   clear

**双冒号表示法** - `::first-line` 对比 `:first-line`

在 CSS3 中，双冒号取代了伪元素的单冒号表示法。这是 W3C 试图区分 *伪类* 和 *伪元素* 的尝试。

在 CSS2 和 CSS1 中，伪类和伪元素都使用了单冒号语法。

为了向后兼容，CSS2 和 CSS1 伪元素可接受单冒号语法。

### ::first-letter 伪元素

`::first-letter` 伪元素用于向文本的首字母添加特殊样式

例子：设置所有 `<p>` 元素中文本的首字母格式

```css
p::first-letter {
  color: #ff0000;
  font-size: xx-large;
}
```

`::first-letter` 伪元素只适用于块级元素

下面的属性适用于 ::first-letter 伪元素：

-   字体属性
-   颜色属性
-   背景属性
-   外边距属性
-   内边距属性
-   边框属性
-   text-decoration
-   vertical-align（仅当 "float" 为 "none"）
-   text-transform
-   line-height
-   float
-   clear

### ::before 伪元素

`::before` 伪元素可用于在元素内容之前插入一些内容。

例子：在每个 `<h1>` 元素的内容之前插入一幅图像

```css
h1::before {content: url(smiley.gif);}
```

### ::after 伪元素

`::after` 伪元素可用于在元素内容之后插入一些内容

例子：在每个 `<h1>` 元素的内容之后插入一幅图像

```css
h1::after {content: url(smiley.gif);}
```

### ::selection 伪元素

`::selection` 伪元素匹配用户选择的元素部分

以下 CSS 属性可以应用于 `::selection`：

-   `color`
-   `background`
-   `cursor`
-   `outline`

例子：使所选文本在黄色背景上显示为红色

```css
::selection {
  color: red; 
  background: yellow;
}
```

### 伪元素与 CSS 类结合使用

例子：将以红色和较大的字体显示 `class="intro"` 的段落的首字母

```css
p.intro::first-letter {
  color: #ff0000;
  font-size: 200%;
}
```

### 组合多个伪元素

例子：段落的第一个字母将是红色，字体大小为 `xx-large`。第一行的其余部分将变为蓝色，并使用小型大写字母。该段的其余部分将是默认的字体大小和颜色

```html
<!DOCTYPE html>
<html>
<head>
<style>
p::first-letter {
  color: #ff0000;
  font-size: xx-large;
}

p::first-line {
  color: #0000ff;
  font-variant: small-caps;
}
</style>
</head>
<body>

<p>您可以结合 ::first-letter 和 ::first-line 伪元素来为文本的首字母和首行添加特殊效果！</p>

</body>
</html>
```

## 属性选择器

为带有特定属性的 HTML 元素设置样式

### [attribute] 选择器

选取带有指定属性的元素

例子：选择所有带有 target 属性的 `<a>` 元素

```css
a[target] {background-color: yellow;}
```

### [attribute="value"] 选择器

选取带有指定属性和值的元素

例子：选取所有带有 `target="_blank" `属性的 `<a>` 元素

```css
a[target="_blank"] {background-color: yellow;}
```

### [attribute~="value"] 选择器

选取属性值**包含**指定词的元素

例子：选取 `title` 属性包含 `"flower"` 单词的所有元素

```css
[title~="flower"] {border: 5px solid yellow;}
```

上面的例子会匹配以下属性的元素：title="flower"、title="summer flower" 以及 title="flower new"，但不匹配：title="my-flower" 或 title="flowers"

### [attribute|="value"] 选择器

选取指定属性**以指定值开头**的元素，同 `[attribute^="value"]` 选择器

例子：选取 `class` 属性以 `"top"` 开头的所有元素

> [!tip] 注意
> 值必须是完整或单独的单词，比如 `class="top"` 或者后跟连字符的，比如 `class="top-text"`

```html
<!DOCTYPE html>
<html>
<head>
<style>
[class^="top"] {background: yellow;}
</style>
</head>
<body>

<h1 class="top-header">Welcome</h1>   <!-- 受影响 -->
<p class="top-text">Hello world!</p>   <!-- 受影响 -->
<p class="topcontent">Are you learning CSS?</p>   <!-- 受影响 -->

</body>
</html>

```

### [attribute$="value"] 选择器

选取指定属性以指定值结尾的元素，值不必是完整单词

例子：选取 `class` 属性以 `"test"` 结尾的所有元素

```html
<!DOCTYPE html>
<html>
<head>
<style> 
[class$="te"] {background: yellow;}
</style>
</head>
<body>

<div class="first_test">The first div element.</div>   <!-- 受影响 -->
<div class="second">The second div element.</div>
<div class="my-test">The third div element.</div>   <!-- 受影响 -->
<p class="mytest">This is some text in a paragraph.</p>   <!-- 受影响 -->

</body>
</html>
```

### [attribute*="value"] 选择器

选取属性值包含指定词的元素，值不必是完整单词

例子：选取 `class` 属性包含 `"te"` 的所有元素

```html
<!DOCTYPE html>
<html>
<head>
<style> 
[class*="te"] {background: yellow;}
</style>
</head>
<body>

<div class="first_test">The first div element.</div>   <!-- 受影响 -->
<div class="second">The second div element.</div>
<div class="my-test">The third div element.</div>   <!-- 受影响 -->
<p class="mytest">This is some text in a paragraph.</p>   <!-- 受影响 -->

</body>
</html>
```