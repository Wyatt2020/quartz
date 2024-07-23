---
created: 2023-01-07 16:01
tags:
  - 前端/CSS
---

## display

`display`  规定是否/如何显示元素

每个 _HTML_ 元素都有一个默认的 `display` 值，具体取决于它的元素类型

大多数元素的默认 `display` 值为  *block*  或  *inline*

### 块级元素（block element）

块级元素**总是从新行开始**，并占据可用的全部宽度（尽可能向左和向右伸展）

块级元素的一些例子：

- `<div>`
- `<h1>` - `<h6>`
- `<p>`
- `<form>`
- `<header>`
- `<footer>`
- `<section>`

### 行内元素（inline element）

内联元素**不从新行开始**，仅占用所需的宽度

行内元素的一些例子：

- `<span>`
- `<a>`
- `<img>`

### 覆盖默认的 display 值

每个元素都有一个默认 `display` 值

可以覆盖它，将行内元素更改为块元素，反之亦然

一个常见的例子是为实现水平菜单而生成行内的  `<li>`  元素：

```css
li {display: inline;}
```

### 隐藏元素 - display: none

`display: none;`  通常与 JavaScript 一起使用，以隐藏和显示元素，而无需删除和重新创建它们

默认情况下，`<script>`  元素使用  `display: none;`

### 隐藏元素 - visibility: hidden

`visibility: hidden;`  也可以隐藏元素。

但是，该元素仍将占用与之前相同的空间。元素将被隐藏，但仍会影响布局

- _visible_：默认值，元素是可见的
- _hidden_：元素是不可见的
- _collapse_：当在表格元素中使用时，此值可删除一行或一列，但是它不会影响表格的布局。被行或列占据的空间会留给其他内容使用。如果此值被用在其他的元素上，会呈现为` "hidden"`
- _inherit_：规定应该从父元素继承 `visibility` 属性的值

## 定位 | position

`position`  规定应用于元素的定位方法的类型

有五个不同的位置值：

- static
- relative
- fixed
- absolute
- sticky

### position: static

_HTML_ 元素默认情况下的定位方式为 _static_（静态）

静态定位的元素不受 top、bottom、left 和 right 属性的影响

`position: static` 的元素不会以任何特殊方式定位，它始终根据页面的正常流进行定位

```css
div.static {
  position: static;
  left: 30px;  /* left/right/top/bottom 属性不生效 */
  border: 3px solid #73AD21;
}
```

### position: relative

`position: relative`  的元素**相对于其正常位置**进行定位。

设置相对定位的元素的 top、right、bottom 和 left 属性将导致其偏离其正常位置进行调整，不会对其余内容进行调整来适应元素留下的任何空间

```css
div.relative {
  position: relative; /* 相对左边移动 30px */
  left: 30px;
  border: 3px solid #73AD21;
}
```

### position: fixed

`position: fixed`  的元素是**相对于视口**定位的，这意味着即使滚动页面，它也始终位于同一位置。 top、right、bottom 和 left 属性用于定位此元素

固定定位的元素不会在页面中通常应放置的位置上留出空隙

例子：

```css
div.fixed {
  position: fixed;
  bottom: 0;
  right: 0;
  width: 300px;
  border: 3px solid #73AD21;
}
```

### position: absolute

`position: absolute`  的元素相对于最近的定位祖先元素进行定位（而不是相对于视口定位，如 fixed）

如果绝对定位的元素没有祖先，它将使用文档主体（`body`），并随页面滚动一起移动

> [!attention] 注意
> “被定位的”元素是其位置除  `static`  以外的任何元素。

```html
<!DOCTYPE html>
<html>
<head>
<style>
div.relative {
  position: relative;
  width: 400px;
  height: 200px;
  border: 3px solid #73AD21;
}

div.absolute {
  position: absolute;
  top: 80px;
  right: 0;
  width: 200px;
  height: 100px;
  border: 3px solid #73AD21;
}
</style>
</head>
<body>

<div class="relative">这个 div 元素设置 position: relative;
  <div class="absolute">这个 div 元素设置 position: absolute;</div>
</div>

</body>
</html>
```

### position: sticky

`position: sticky`  的元素根据用户的滚动位置进行定位

粘性元素根据滚动位置在相对（`relative`）和固定（`fixed`）之间切换。起先它会被相对定位，直到在视口中遇到给定的偏移位置为止

然后将其“粘贴”在适当的位置（比如 `position:fixed`）

<iframe src="https://www.w3school.com.cn/tiy/t.asp?f=css_position_sticky" style="width: 100%; height: 350px; border:4px solid #e5e5e5;transform: scale(0.9);"></iframe>
源码：

```html
<!DOCTYPE html>
<html>
<head>
<style>
div.sticky {
  position: -webkit-sticky; /* Safari */
  position: sticky;
  top: 0;
  padding: 5px;
  background-color: #cae8ca;
  border: 2px solid #4CAF50;
}
</style>
</head>
<body>

<p>请试着在这个框架内<b>滚动</b>页面，以理解粘性定位的原理。</p>

<div class="sticky">我是有粘性的！</div>

<div style="padding-bottom:2000px">
  <p>在此例中，当您到达元素的滚动位置时，粘性元素将停留在页面顶部（top: 0）。</p>
  <p>向上滚动以消除粘性。</p>
  <p>一些启用滚动的文本.. Lorem ipsum dolor sit amet, illum definitiones no quo, maluisset concludaturque et eum, altera fabulas ut quo. Atqui causae gloriatur ius te, id agam omnis evertitur eum. Affert laboramus repudiandae nec et. Inciderint efficiantur his ad. Eum no molestiae voluptatibus.</p>
  <p>一些启用滚动的文本.. Lorem ipsum dolor sit amet, illum definitiones no quo, maluisset concludaturque et eum, altera fabulas ut quo. Atqui causae gloriatur ius te, id agam omnis evertitur eum. Affert laboramus repudiandae nec et. Inciderint efficiantur his ad. Eum no molestiae voluptatibus.</p>
</div>

</body>
</html>
```

### 重叠元素

在对元素进行定位时，它们可以与其他元素重叠

`z-index`  指定元素的堆栈顺序（哪个元素应放置在其他元素的前面或后面）,元素可以设置正或负的堆叠顺序

例子：

```html
<!DOCTYPE html>
<html>
<head>
<style>
img {
  position: absolute;
  left: 0px;
  top: 0px;
  z-index: -1;
}
</style>
</head>
<body>

<h1>这是标题</h1>
<img src="/i/logo/w3logo-1.png" width="188" height="267">
<p>由于图像的 z-index 为 -1，它将被置于文本之后。</p>

</body>
</html>
```

### 定位图像中的文本

例子：

- [Centered](https://www.w3school.com.cn/tiy/t.asp?f=css_image_text_center)
- [Top Left](https://www.w3school.com.cn/tiy/t.asp?f=css_image_text_top_left)
- [Top Right](https://www.w3school.com.cn/tiy/t.asp?f=css_image_text_top_right)
- [Bottom Left](https://www.w3school.com.cn/tiy/t.asp?f=css_image_text_bottom_left)
- [Bottom Right](https://www.w3school.com.cn/tiy/t.asp?f=css_image_text_bottom_right)

### 剪裁绝对定位元素

当一幅图像的尺寸大于包含它的元素时，使用 `clip` 修剪图像并显示为这个形状

[例子](https://www.w3school.com.cn/tiy/t.asp?f=eg_csse_clip)

## 溢出 | overflow

`overflow`  指定在元素的内容太大而无法放入指定区域时，是剪裁内容还是添加滚动条

允许以下值：

- _visible_：**默认值**，溢出没有被剪裁，内容在元素框外渲染
- _hidden_：溢出被剪裁，其余内容将不可见
- _scroll_：溢出被剪裁，总会添加滚动条以查看其余内容，即使滚动条不被需要
- _auto_：与 _scroll_ 类似，但仅在必要时添加滚动条

> [!tip] 提示
>
> - `overflow`  属性仅适用于具有指定高度的块元素
> - 在 OS X Lion（在 Mac 上）中，滚动条默认情况下是隐藏的，并且仅在使用时显示（即使设置了 `"overflow:scroll"`）

### overflow-x 和 overflow-y

`overflow-x`  和  `overflow-y`  规定是仅水平还是垂直地（或同时）更改内容的溢出：

- `overflow-x`：指定如何处理内容的左/右边缘
- `overflow-y`：指定如何处理内容的上/下边缘

例子：

```css
div {
  overflow-x: hidden; /* 隐藏水平滚动栏 */
  overflow-y: scroll; /* 添加垂直滚动栏 */
}
```

## 浮动 | float

`float`  规定元素如何浮动

最简单的用法是，`float`  可实现（报纸上）文字包围图片的效果

允许以下值：

- _left_：元素浮动到其容器的左侧
- _right_：元素浮动在其容器的右侧
- _none_：**默认值**，元素不会浮动（将显示在文本中刚出现的位置）
- _inherit_：元素继承其父级的 `float` 值

## 清除浮动 | clear & clearfix

### clear

`clear`  指定哪些元素可以浮动于被清除元素的旁边以及哪一侧

允许以下值：

- _none_：**默认值**，允许两侧都有浮动元素
- _left_：左侧不允许浮动元素
- _right_：右侧不允许浮动元素
- _both_：左侧或右侧均不允许浮动元素
- _inherit_：元素继承其父级的 `clear` 值

使用  `clear`  属性的最常见用法是在元素上使用了  `float`  属性之后

在清除浮动时，应该对清除与浮动进行匹配：如果某个元素浮动到左侧，则应清除左侧。您的浮动元素会继续浮动，但是被清除的元素将显示在其下方

[例子](https://www.w3school.com.cn/tiy/t.asp?f=css_layout_clear)

### clearfix Hack

如果一个元素比包含它的元素高，并且它是浮动的，它将“溢出”到其容器之外：

然后我们可以向包含元素添加 `overflow: auto;`，[来解决此问题](https://www.w3school.com.cn/tiy/t.asp?f=css_layout_clearfix_1)

新的 `clearfix hack` 技术使用起来更安全，以下代码被应用于多数网站：

```css
.clearfix::after {
  content: "";
  clear: both;
  display: table;
}
```

[例子](https://www.w3school.com.cn/tiy/t.asp?f=css_layout_clearfix_2)

## 浮动实例

### 网格 / 等宽的框

```css
* {
  box-sizing: border-box;
}

.box {
  float: left;
  width: 33.33%; /* 三个框（四框使用 25%，两框使用 50%，以此类推） */
  padding: 50px; /* 如果需要在图片间增加间距 */
}
```

### box-sizing

`box-sizing`  允许我们在框的总宽度（和高度）中包括内边距和边框，确保内边距留在框内而不会破裂

假如不指定 `box-sizing` 属性，元素的宽度和高度是这样计算的：

- `width` + `padding` + `border` = 元素的实际宽度
- `height` + `padding` + `border` = 元素的实际高度

下面的代码能够确保以这种更直观的方式调整所有元素的大小：

```css
* {box-sizing: border-box;}
```

### 图像并排

[例子](https://www.w3school.com.cn/tiy/t.asp?f=css_float_images_side)

```css
.img-container {
  float: left;
  width: 33.33%; /* 三个框（四框使用 25%，两框使用 50%，以此类推） */
  padding: 5px; /* 如果需要在图片间增加间距 */
}
```

### 等宽等高的框

创建具有相同高度的浮动框并不容易，快速解决方案是设置一个[固定的高度](https://www.w3school.com.cn/tiy/t.asp?f=css_float_boxes_height)

但是，这么做就失去了弹性。如果两个框的内容数量不一样，那么有一个框的内容会溢出

这时可以使用 **[Flexbox](https://www.w3school.com.cn/css/css3_flexbox.asp)** 创建[弹性框](https://www.w3school.com.cn/tiy/t.asp?f=css_float_boxes_flex)

```css
.flex-container {
  display: flex;  /* flex 容器将可伸缩 */
  flex-wrap: nowrap; /* 规定 flex 项目不换行 */
  background-color: DodgerBlue;
}
```

## Flexbox

[CSS Flexbox](https://www.w3school.com.cn/css/css3_flexbox.asp)

### 完美的居中

[例子](https://www.w3school.com.cn/tiy/t.asp?f=css3_flexbox_perfect_center)

```css
.flex-container {
  display: flex;
  height: 300px;
  justify-content: center;
  align-items: center;
}
```
