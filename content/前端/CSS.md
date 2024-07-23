---
created: 2023-01-02 22:31
modified: 2023-08-27 23:40
tags:
  - 前端/CSS
---

## 什么是 CSS？

- CSS 指的是层叠样式表 (Cascading Style Sheets)
- CSS 描述了如何在屏幕、纸张或其他媒体上显示 HTML 元素
- CSS 节省了大量工作。它可以同时控制多张网页的布局
- 外部样式表存储在 CSS 文件中

## CSS 语法

CSS 规则集（rule-set）由选择器和声明块组成：

![[Pasted image 20240720142732.png]]

选择器指向需要设置样式的 HTML 元素。

声明块包含一条或多条用分号分隔的声明。

每条声明都包含一个 CSS 属性名称和一个值，以冒号分隔。

多条 CSS 声明用分号分隔，声明块用花括号括起来。

CSS 选择器用于查找要设置样式的 HTML 元素。

## CSS 选择器

![[CSS 选择器|seamless]]

## 使用

**外部样式表**

在 HTML 页面 `<head>` 部分内的 `<link>` 元素中进行定义

```html
<head>
	<link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

**内部样式表**

在 HTML 页面的 `<head>` 部分内的 `<style>` 元素中进行定义

```html
<head>
	<style type="text/css">
	body {background-color: red}
	p {margin-left: 20px}
	</style>
</head>
```

**行内样式**（**不建议使用**）

又叫**内联样式**，将 `style` 属性添加到相关元素，`style` 属性可包含任何 _CSS_ 属性

```html
<p style="color:white;font-size:16px;"></p>
```

**层叠顺序**

1.  行内样式
2.  外部和内部样式表
3.  浏览器默认样式

> 如果在不同样式表中为同一选择器（元素）定义了一些属性，则将使用最后读取的样式表中的值

## 注释

`<* 注释 *>`

## 颜色

### 颜色名

- CSS/HTML 支持 [140 种标准颜色名](https://htmlcolorcodes.com/color-names/)

### RGB

- _rgb(red, green, blue)_
- 每个参数定义了 0 到 255 之间的颜色强度

**RGBA**

- _rgba(red, green, blue, alpha)_
- *alpha*  参数是介于 0.0（完全透明）和 1.0（完全不透明）之间的数字

### HEX

- `#rrggbb`
- 其中 rr（红色）、gg（绿色）和 bb（蓝色）是介于 00 和 ff 之间的十六进制值（与十进制 0-255 相同）

### HSL

- _hsla(hue, saturation, lightness)_
- _色相_（_hue_）是色轮上从 0 到 360 的度数。0 是红色，120 是绿色，240 是蓝色
- _饱和度_（_saturation_）是一个百分比值，0％ 表示灰色阴影，而 100％ 是全色
- _亮度_（_lightness_）也是百分比，0％ 是黑色，50％ 是既不明也不暗，100％是白色

> [!tip] **颜色集**
>
> - [HTML Color Codes](https://htmlcolorcodes.com/)
> - [颜色表大全](https://www.toolscat.com/ui/color/yansebiao)

## 背景 | background

### 背景颜色 - color

`background-color`：指定背景颜色

`opacity`：指定透明度，值越小越透明（0.0 - 1.0）

也可以使用 _rgba(red, green, blue, alpha)_ 的 _alpha_ 值来设置透明度

### 背景图像 - image

`background-image: url("paper.jpg");`

### 背景重复 - repeat

默认情况下，`background-image`  属性在水平和垂直方向上都重复图像

这时可以使用 `background-repeat` 来调整重复方向

- _repeat_：默认
- _no-repeat_：不重复
- _repeat-x_：水平重复
- _repeat-y_：垂直重复
- _inherit_：规定应该从父元素继承 `background-repeat` 属性的设置

### 背景位置 - position

`background-position` 设置背景原图像（由 `background-image` 定义）的位置

> [!attention] 注意
> 所有浏览器都支持 `background-position` 属性（需要把 `background-attachment` 属性设置为 _"fixed"_，才能保证该属性在 Firefox 和 Opera 中正常工作）。

**数值**

- `background-positon: 12px 24px`：(x, y)~(12px, 24px)
- `background-positon: 10% 20%`：(x, y)~(10%, 20%)

**关键字**

|    属性值     | 说明     |
| :-----------: | -------- |
|   top left    | 左上     |
|  top center   | 靠上居中 |
|   top right   | 右上     |
|  left center  | 靠左居中 |
| center center | 正中     |
| right center  | 靠右居中 |
|  bottom left  | 左下     |
| bottom center | 靠下居中 |
| bottom right  | 右下     |

**相对于内容框来定位背景图像**

`background-origin: padding-box|border-box|content-box;`

- _padding-box_：背景图像相对于内边距框来定位
- _border-box_：背景图像相对于边框盒来定位
- _content-box_：背景图像相对于内容框来定位

> [!attention] 注意
> 如果背景图像的 `background-attachment` 属性为 _"fixed"_，则该属性没有效果

### 背景固定 - attachment

`background-attachment` 设置背景图像是否固定或者随着页面的其余部分滚动

- _scroll_：默认值。背景图像会随着页面其余部分的滚动而移动
- _fixed_：当页面的其余部分滚动时，背景图像不会移动
- _inherit_：规定应该从父元素继承 `background-attachment` 属性的设置

### 背景大小 - size

`background-size: length|percentage|cover|contain;`

| 值         | 描述                                                                                                                                      |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| length     | 设置背景图像的高度和宽度。<br/>第一个值设置宽度，第二个值设置高度。<br/>如果只设置一个值，则第二个值会被设置为 "auto"。                   |
| percentage | 以父元素的百分比来设置背景图像的宽度和高度。<br/>第一个值设置宽度，第二个值设置高度。<br/>如果只设置一个值，则第二个值会被设置为 "auto"。 |
| cover      | 把背景图像扩展至足够大，以使背景图像完全覆盖背景区域。<br/>背景图像的某些部分也许无法显示在背景定位区域中。                               |
| contain    | 把图像图像扩展至最大尺寸，以使其宽度和高度完全适应内容区域。                                                                              |

### 背景绘制区域 - clip

`background-clip: border-box|padding-box|content-box;`

- _border-box_：背景被裁剪到边框盒
- _padding-box_：背景被裁剪到内边距框
- _content-box_：背景被裁剪到内容框

### 简写属性

```css
body {
  background: #ffffff url("tree.png") no-repeat right top;
}
```

在使用简写属性时，属性值的顺序为：

- `background-color`
- `background-image`
- `background-repeat`
- `background-attachment`
- `background-position`

属性值之一缺失并不要紧，只要按照此顺序设置其他值即可

## 边框 | border

`border-style` 指定要显示的边框类型。

允许以下值：

- _dotted_：定义点线边框
- _dashed_：定义虚线边框
- _solid_：定义实线边框
- _double_：定义双边框
- _groove_：定义 3D 坡口边框。效果取决于 border-color 值
- _ridge_：定义 3D 脊线边框。效果取决于 border-color 值
- _inset_：定义 3D inset 边框。效果取决于 border-color 值
- _outset_：定义 3D outset 边框。效果取决于 border-color 值
- _none_：定义无边框
- _hidden_：定义隐藏边框

> `border-style` 可以设置一到四个值（用于上边框、右边框、下边框和左边框）`border-style: dotted dashed solid double;`

> [!attention] 注意
> 除非设置了  `border-style`  属性，否则其他 CSS 边框属性都不会有任何作用

### 边框宽度 - width

`border-width`  指定四个边框的宽度

可以将宽度设置为特定大小（以 px、pt、cm、em 计），也可以使用以下三个预定义值之一：

- _thin_
- _medium_
- _thick_

> `border-width`  可以设置一到四个值（用于上边框、右边框、下边框和左边框）

### 边框颜色 - color

`border-color`  用于设置四个边框的颜色

可以通过以下方式设置颜色：

- _name_：指定颜色名，比如 _"red"_
- _HEX_：指定十六进制值，比如 _"#ff0000"_
- _RGB_：指定 RGB 值，比如 _"rgb(255,0,0)"_
- _HSL_：指定 HSL 值，比如 _"hsl(0, 100%, 50%)"_
- _transparent_

> `border-color`  可以设置一到四个值（用于上边框、右边框、下边框和左边框）

> [!attention] 注意
> 如果未设置  `border-color`，则它将继承元素的颜色

### 单独的边

**边框**：

- `border-top-style`
- `border-right-style`
- `border-bottom-style`
- `border-left-style`

**工作原理**：

- 4 个值：上，右，下，左
- 3 个值：上，左右，下
- 2 个值：上下，左右
- 1 个值：所有边框

**颜色**：

- `border-top-color`
- `border-right-color`
- `border-bottom-color`
- `border-left-color`

**宽度**：

- `border-top-width`
- `border-right-width`
- `border-bottom-width`
- `border-left-width`

### 简写属性

`border`  是以下各个边框属性的简写属性：

- `border-width`
- `border-style`（必需）
- `border-color`

例子：

- 所有边框：`border: 5px solid red;`
- 单边框：`border-left: 6px solid red;`

### 圆角边框 - radius

`border-radius`  用于向元素添加圆角边框

语法：

```css
border-radius: 1-4 length|% / 1-4 length|%;
```

> `border-radius` 可以设置一到四个值（用于上边框、右边框、下边框和左边框）

## 外边距 | margin

`margin`  用于在任何定义的边框之外，为元素周围创建空间

### 单独的边

- `margin-top`
- `margin-right`
- `margin-bottom`
- `margin-left`

所有外边距属性都可以设置以下值：

- _auto_：浏览器来计算外边距，使元素在其容器中水平居中
- *length* ：以 px、pt、cm 等单位指定外边距
- _%_：指定以包含元素宽度的百分比计的外边距
- _inherit_：指定应从父元素继承外边距

> [!tip] 提示：允许负值

### 简写属性

`margin`  是以下各外边距属性的简写属性：

- `margin-top`
- `margin-right`
- `margin-bottom`
- `margin-left`

> `margin` 属性可以设置一到四个值（用于上边框、右边框、下边框和左边框）

### 外边距合并

外边距合并指的是，当两个垂直外边距相遇时，它们将形成一个外边距

合并后的外边距的高度等于两个发生合并的外边距的高度中的较大者

只有普通文档流中块框的垂直外边距才会发生外边距合并。行内框、浮动框或绝对定位之间的外边距不会合并

![[Pasted image 20240720142932.png]]

## 内边距 | padding

`padding`  属性用于在任何定义的边界内的元素内容周围生成空间

### 单独的边

- `padding-top`
- `padding-right`
- `padding-bottom`
- `padding-left`

所有内边距属性都可以设置以下值：

- _length_：以 px、pt、cm 等单位指定内边距
- _%_：指定以包含元素宽度的百分比计的内边距
- _inherit_：指定应从父元素继承内边距

### 简写属性

`padding`  是以下各内边距属性的简写属性：

- `padding-top`
- `padding-right`
- `padding-bottom`
- `padding-left`

> `padding` 可以设置一到四个值（用于上边框、右边框、下边框和左边框）

### 内边距和元素宽度

下面的例子中，`<div>` 元素的宽度为 300px。但是，`<div>` 元素的实际宽度将是 350px（300px + 左内边距 25px + 右内边距 25px）：

```css
div {
  width: 300px;
  padding: 25px;
}
```

若要将宽度保持为 300px，无论填充量如何，那么您可以使用  `box-sizing`  属性。这将导致元素保持其宽度。如果增加内边距，则可用的内容空间会减少。

使用 box-sizing 属性将宽度保持为 300px，无论填充量如何：

```css
div {
  width: 300px;
  padding: 25px;
  box-sizing: border-box;
}
```

## 高度和宽度 | height&width

`height`  和  `width`  用于设置元素的高度和宽度。

height 和 width 不包括内边距、边框或外边距。它设置的是元素内边距、边框以及外边距内的区域的高度或宽度。

`height`  和  `width`  可设置如下值：

- _auto_：默认。浏览器计算高度和宽度
- _length_：以 px、cm 等定义高度/宽度
- _%_：以包含块的百分比定义高度/宽度
- _initial_：将高度/宽度设置为默认值
- _inherit_：从其父值继承高度/宽度

> [!attention] 注意
> `height`  和  `width`  属性不包括内边距、边框或外边距。它们设置的是元素的内边距、边框和外边距内的区域的高度/宽度

### 最大宽度 - max-width

`max-width`  用于设置元素的最大宽度

窗口宽度超过 `max-width` 就按照 `max-width` 宽度，如果窗口宽度小于 `max-width`，就按照窗口宽度

允许以下值：

- _none_：默认。定义对元素的最大宽度没有限制，取决于浏览器
- _length_：定义元素的最大宽度值
- _%_：定义基于包含它的块级对象的百分比最大宽度
- _inherit_：规定应该从父元素继承 `max-width` 属性的值

> [!tip] `width` 与 `max-width` 的区别
> `width` 是固定的，`max-width` 是根据窗口大小自适应的，所以一般尽量使用 `max-width`

### 最小宽度

`min-width`  用于设置元素的最大宽度

### 最大高度

`max-height`  用于设置元素的最大宽度

### 最小高度

`min-height`  用于设置元素的最大宽度

## 框模型

所有 HTML 元素都可以视为方框。在 CSS 中，在谈论设计和布局时，会使用术语“盒模型”或“框模型”。

CSS 框模型实质上是一个包围每个 HTML 元素的框。它包括：外边距、边框、内边距以及实际的内容。

下图展示了框模型：

![[Pasted image 20240720143011.png]]

对不同部分的说明：

- _内容_：框的内容，其中显示文本和图像
- _内边距_：清除内容周围的区域。内边距是透明的
- _边框_：围绕内边距和内容的边框
- _外边距_：清除边界外的区域。外边距是透明的

框模型允许我们在元素周围添加边框，并定义元素之间的空间

元素框的最内部分是实际的内容，直接包围内容的是内边距。内边距呈现了元素的背景。内边距的边缘是边框。边框以外是外边距，外边距默认是透明的，因此不会遮挡其后的任何元素。

> [!tip] 提示
> 背景应用于由内容和内边距、边框组成的区域。

内边距、边框和外边距都是可选的，默认值是零。

但是，许多元素将由用户代理样式表设置外边距和内边距。可以通过将元素的 margin 和 padding 设置为零来覆盖这些浏览器样式。这可以分别进行，也可以使用通用选择器对所有元素进行设置：

```css
* {
  margin: 0;
  padding: 0;
}
```

在 CSS 中，`width` 和 `height` 指的是内容区域的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。

假设框的每个边上有 10 个像素的外边距和 5 个像素的内边距。如果希望这个元素框达到 100 个像素，就需要将内容的宽度设置为 70 像素，请看下图：

![[Pasted image 20240720143038.png]]

> [!tip] 提示
>
> - 内边距、边框和外边距可以应用于一个元素的所有边，也可以应用于单独的边
> - 外边距可以是负值，而且在很多情况下都要使用负值的外边距

### 元素的总宽度和总高度

元素总宽度 = 宽度 + 左内边距 + 右内边距 + 左边框 + 右边框 + 左外边距 + 右外边距

元素总高度 = 高度 + 上内边距 + 下内边距 + 上边框 + 下边框 + 上外边距 + 下外边距

## 轮廓 | outline

轮廓是在元素周围绘制的一条线，在边框之外，以凸显元素

与边框不同，轮廓是在元素边框之外绘制的，并且可能与其他内容重叠。同样，轮廓也不是元素尺寸的一部分；元素的总宽度和高度不受轮廓线宽度的影响。

CSS 拥有如下轮廓属性：

- `outline-style`
- `outline-color`
- `outline-width`
- `outline-offset`
- `outline`

### 轮廓样式 - style

`outline-style` 指定轮廓的样式，并可设置如下值：

- `dotted`：定义点状的轮廓
- `dashed`：定义虚线的轮廓
- `solid`：定义实线的轮廓
- `double`：定义双线的轮廓
- `groove`：定义 3D 凹槽轮廓
- `ridge`：定义 3D 凸槽轮廓
- `inset`：定义 3D 凹边轮廓
- `outset`：定义 3D 凸边轮廓
- `none`：定义无轮廓
- `hidden`：定义隐藏的轮廓

> [!attention] 注意
> 除非设置了  `outline-style`  属性，否则其他轮廓属性都不会有任何作用

### 轮廓宽度 - width

`outline-width`  指定轮廓的宽度，并可设置如下值之一：

- _thin_：通常为 1px
- _medium_：通常为 3px
- _thick_ ：通常为 5px
- _特定尺寸_：以 px、pt、cm、em 计

### 轮廓颜色 - color

`outline-color`  用于设置轮廓的颜色

可以通过以下方式设置颜色：

- _name_：指定颜色名，比如 _"red"_
- _HEX_：指定十六进制值，比如 _"#ff0000"_
- _RGB_：指定 RGB 值，比如 _"rgb(255,0,0)"_
- _HSL_：指定 HSL 值，比如 _"hsl(0, 100%, 50%)"_
- _invert_：执行颜色反转（确保轮廓可见，无论是什么颜色背景）

### 简写属性

`outline`  是以下各个轮廓属性的简写属性：

- `outline-width`
- `outline-style`（必需）
- `outline-color`

`outline`  可指定一个、两个或三个值。值的顺序无关紧要

### 轮廓偏移 - offset

`outline-offset`  指定轮廓与元素的边缘或边框之间的空间。元素及其轮廓之间的空间是透明的

轮廓在两方面与边框不同：

- 轮廓不占用空间
- 轮廓可能是非矩形

允许以下值：

- _length_：轮廓与边框边缘的距离
- _inherit_：规定应从父元素继承 `outline-offset` 属性的值

## 文本 | text

### 文本颜色 - color

`color`  用于设置文本的颜色。颜色由以下值指定：

- _name_：指定颜色名，比如 _"red"_
- _HEX_：指定十六进制值，比如 _"#ff0000"_
- _RGB_：指定 RGB 值，比如 _"rgb(255,0,0)"_
- _HSL_：指定 HSL 值，比如 _"hsl(0, 100%, 50%)"_

> [!tip] 提示
> 对于 W3C compliant CSS，如果您定义了  `color`  属性，则还必须定义  `background-color`  属性

### 文本对齐 - align

`text-align`  用于设置文本的水平对齐方式

允许以下值：

- _left_：把文本排列到左边。默认值，由浏览器决定
- _right_：把文本排列到右边
- _center_：把文本排列到中间
- _justify_：实现两端对齐文本效果
- _inherit_：规定应该从父元素继承 `text-align` 属性的值

### 垂直对齐 - vertical-align

`vertical-align` 设置一个元素的垂直对齐

语法：

```css
vertical-align：baseline | sub | super | top | text-top | middle | bottom | text-bottom |inherit
```

允许以下值：

- _baseline_：将支持 valign 特性的对象的内容与基线对齐
- _sub_：垂直对齐文本的下标
- _super_：垂直对齐文本的上标
- _top_：将支持 valign 特性的对象的内容与对象顶端对齐
- _text-top_：将支持 valign 特性的对象的文本与对象顶端对齐
- _middle_：将支持 valign 特性的对象的内容与对象中部对齐
- _bottom_：将支持 valign 特性的对象的文本与对象底端对齐
- _text-bottom_：将支持 valign 特性的对象的文本与对象底端对齐
- _inherit_：规定应该从父元素继承 `vertical-align` 属性的值
- 百分比：用百分比指定由基线算起的偏移量。可以为负值。基线对于百分数来说就是 0%
- 长度值：用长度值指定由基线算起的偏移量。可以为负值。基线对于数值来说为 0

### 文本方向 - direction

`direction` 规定文本的方向 / 书写方向

允许以下值：

- _ltr_：默认。文本方向从左到右
- _rtl_：文本方向从右到左
- _inherit_：规定应该从父元素继承 `direction` 属性的值

`unicode-bidi` 与 `direction` 属性一起使用，设置或返回是否应重写文本以支持同一文档中的多种语言

允许以下值：

- _normal_：默认值。元素不会打开额外的嵌入级别
- _embed_：对于行内元素，此值将打开额外的嵌入级别
- _bidi-override_：对于行内元素，该值会创建一个覆盖；对于块容器元素，该值将为不在另一个块容器元素内的行内级别的后代创建一个覆盖
- _isolate_：该元素与其同胞隔离
- _isolate-override_
- _plaintext_
- _initial_：将此属性设置为其默认值。参阅  [initial](https://www.w3school.com.cn/cssref/css_initial.asp "CSS initial 关键字")
- _inherit_：从其父元素继承此属性。参阅  [inherit](https://www.w3school.com.cn/cssref/css_inherit.asp "CSS inherit 关键字")

### 文字装饰 - decoration

`text-decoration`  用于设置或删除文本装饰

允许以下值：

- _none_：默认。定义标准的文本
- _underline_：定义文本下的一条线
- _overline_：定义文本上的一条线
- _line-through_：定义穿过文本下的一条线
- _blink_：定义闪烁的文本
- _inherit_：规定应该从父元素继承 text-decoration 属性的值

### 文本转换 - transform

`text-transform`  用于将所有内容转换为大写或小写字母，或将每个单词的首字母大写

允许以下值：

- _none_：无转换发生
- _capitalize_：将每个单词的第一个字母转换成大写，其余无转换发生
- _uppercase_：转换成大写
- _lowercase_：转换成小写

### 文字间距

#### 文字缩进 - indent

`text-indent`  用于指定文本第一行的缩进

允许以下值：

- _length_：定义固定的缩进，默认值 0
- _%_：定义基于父元素宽度的百分比的缩进
- _inherit_：规定应该从父元素继承 `text-indent` 属性的值

#### 字母间距 - letter-spacing

`letter-spacing`  用于指定文本中字符之间的间距

允许以下值：

- _normal_：默认间距
- _length_：由浮点数字和单位标识符组成的长度值，允许为负值

#### 行高 - line-height

`line-height`  用于指定行之间的间距

允许以下值：

- _normal_：默认，设置合理的行间距
- _number_：设置数字，此数字会与当前的字体尺寸相乘来设置行间距
- _length_：设置固定的行间距
- _%_：基于当前字体尺寸的百分比行间距
- _inherit_：规定应该从父元素继承 `line-height` 属性的值

#### 字间距 - word-spacing

`word-spacing`  用于指定文本中单词之间的间距

允许以下值：

- _normal_：默认间距
- _length_：由浮点数字和单位标识符组成的长度值，允许为负值

#### 空白 - white-space

`white-space`  指定元素内部空白的处理方式

允许以下值：

- _normal_：默认值，文本自动处理换行。假如抵达容器边界内容会转到下一行
- _pre_：空白和换行会被浏览器保留。其行为方式类似 HTML 中的 `<pre>` 标签。这个值需要 IE6+或者`!DOCTYPE` 声明为 `standards-compliant mode` 支持
- _nowrap_：强制在同一行内显示所有文本，直到文本结束或者遇到 `<br/>` 标签
- pre-wrap：保留空白符序列，但是正常地进行换行
- pre-line：合并空白符序列，但是保留换行符
- inherit：规定应该从父元素继承 `white-space` 属性的值

### 文本阴影 - shadow

`text-shadow`  为文本添加阴影效果

语法：

```css
text-shadow: h-shadow v-shadow blur color;
```

- _h-shadow_：必需。水平阴影的位置，允许负值
- _v-shadow_：必需。垂直阴影的位置，允许负值
- _blur_：可选。模糊的距离
- _color_：可选。阴影的颜色

## 字体 | font

### 通用字体族

在 CSS 中，有五个通用字体族：

- 衬线字体（Serif）- 在每个字母的边缘都有一个小的笔触。它们营造出一种形式感和优雅感。
- 无衬线字体（Sans-serif）- 字体线条简洁（没有小笔画）。它们营造出现代而简约的外观。
- 等宽字体（Monospace）- 所有字母都有相同的固定宽度。它们创造出机械式的外观。
- 草书字体（Cursive）- 模仿了人类的笔迹。
- 幻想字体（Fantasy）- 装饰性/俏皮的字体。

所有不同的字体名称都属于这五个通用字体系列之一

### font-family

`font-family` 用于规定文本的字体

- `font-family` 应包含多个字体名称作为“后备”系统，以确保浏览器/操作系统之间的最大兼容性
- 请以需要的字体开始，并以通用系列结束（如果没有其他可用字体，则让浏览器选择通用系列中的相似字体）
- 字体名称应以逗号分隔

### 字体样式 - style

`font-style`  用于指定斜体文本

允许以下值：

- normal：默认值。浏览器显示一个标准的字体样式
- italic：浏览器会显示一个斜体的字体样式
- oblique：浏览器会显示一个倾斜的字体样式
- inherit：规定应该从父元素继承字体样式

### 字体粗细 - weight

`font-weight`  指定字体的粗细

允许以下值：

- _normal_：默认值，定义标准的字符
- _bold_：定义粗体字符
- _bolder_：定义更粗的字符
- _lighter_：定义更细的字符
- _100, 200, ... , 800, 900_：定义由粗到细的字符。400 等同于 _normal_，而 700 等同于 _bold_
- _inherit_：规定应该从父元素继承字体的粗细

### 字体变体 - variant

`font-variant`  指定是否以 _small-caps_ 字体（小型大写字母）显示文本

在 _small-caps_ 字体中，所有小写字母都将转换为大写字母。但是，转换后的大写字母的字体大小,小于文本中原始大写字母的字体大小，例如：

<p>This is a paragraph</p>
<p style="font-variant: small-caps">This is a paragraph</p>

允许以下值：

- _normal_：默认值，浏览器会显示一个标准的字体
- _small-caps_：浏览器会显示小型大写字母的字体
- _inherit_：规定应该从父元素继承 `font-variant` 属性的值

### 字体大小 - size

`font-size`  设置文本的大小

在网页设计中，能够管理文本大小很重要。但是，不应使用调整字体大小来使段落看起来像标题，或是使标题看起来像段落

请始终使用正确的 _HTML_ 标签，例如 `<h1>` - `<h6>` 用于标题，而 `<p>` 仅用于段落

`font-size` 值可以是绝对或相对大小

绝对尺寸：

- 将文本设置为指定大小
- 不允许用户在所有浏览器中更改文本大小（可访问性不佳）
- 当输出的物理尺寸已知时，绝对尺寸很有用

相对尺寸：

- 设置相对于周围元素的大小
- 允许用户在浏览器中更改文本大小

> [!tip] 提示
> 如果没有指定字体大小，则普通文本（如段落）的默认大小为 16px（16px = 1em）

#### 用 em 设置字体大小

为了允许用户调整文本大小（在浏览器菜单中），许多开发人员使用 _em_ 而不是像素

_W3C_ 建议使用 _em_ 尺寸单位

_1em_ 等于当前字体大小。浏览器中的默认文本大小为 _16px_。因此，默认大小 _1em_ 为 _16px_

可以使用这个公式从像素到 _em_ 来计算大小：`pixels/16=em`

#### 响应式字体大小

可以使用  *vw*  单位设置文本大小，它的意思是“视口宽度”（"viewport width"）

这样，文本大小将遵循浏览器窗口的大小，_1vw_ = 视口宽度的 1％

如果视口为 50 厘米宽，则 _1vw_ 为 0.5 厘米

### 谷歌字体

如果您不想使用 _HTML_ 中的任何标准字体，则可以使用 _Google Fonts API_ 向页面添加数百种其他字体。

只需添加一个样式表链接并引用您选择的字体系列：

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Sofia">
```

### 简写属性

`font`  属性是以下属性的简写属性：

- `font-style`
- `font-variant`
- `font-weight`
- `font-size/line-height`
- `font-family`

> [!attention] 注意
> `font-size`  和  `font-family`  的值是必需的。如果缺少其他值之一，则会使用其默认值

## 图标

### Font Awesome 图标

```html
<head>
  <script src="https://kit.fontawesome.com/yourcode.js"></script>
</head>
```

### Bootstrap 图标

```html
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
```

### Google 图标

```html
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
```

## 链接

链接可以使用任何 CSS 属性（例如  `color`、`font-family`、`background`、`text-decoration`  等）来设置样式

此外，可以根据链接处于什么状态来设置链接的不同样式

四种链接状态分别是：

- `a:link`：正常的，未访问的链接
- `a:visited`：用户访问过的链接
- `a:hover`：用户将鼠标悬停在链接上时
- `a:active`：链接被点击时

> [!attention] 注意
> 如果为多个链接状态设置样式，请遵循如下顺序规则：
>
> - `a:hover` 必须 `a:link` 和 `a:visited` 之后
> - `a:active` 必须在 `a:hover` 之后

## 光标 | cursor

`cursor` 指定要显示的光标类型，对链接有用

```html
<span style="cursor:auto">auto</span><br>
<span style="cursor:crosshair">crosshair</span><br>
<span style="cursor:default">default</span><br>
<span style="cursor:e-resize">e-resize</span><br>
<span style="cursor:help">help</span><br>
<span style="cursor:move">move</span><br>
<span style="cursor:n-resize">n-resize</span><br>
<span style="cursor:ne-resize">ne-resize</span><br>
<span style="cursor:nw-resize">nw-resize</span><br>
<span style="cursor:pointer">pointer</span><br>
<span style="cursor:progress">progress</span><br>
<span style="cursor:s-resize">s-resize</span><br>
<span style="cursor:se-resize">se-resize</span><br>
<span style="cursor:sw-resize">sw-resize</span><br>
<span style="cursor:text">text</span><br>
<span style="cursor:w-resize">w-resize</span><br>
<span style="cursor:wait">wait</span><br>
```

## 列表 | list-style

CSS 列表属性可以：

- 为有序列表设置不同的列表项标记
- 为无序列表设置不同的列表项标记
- 将图像设置为列表项标记
- 为列表和列表项添加背景色

### 不同的列表项目标记 - type

`list-style-type`  指定列表项标记的类型

例子：

```css
ul.a {
  list-style-type: circle;
}

ul.b {
  list-style-type: square;
}

ol.c {
  list-style-type: upper-roman;
}

ol.d {
  list-style-type: lower-alpha;
}
```

### 图像作为列表项标记 - image

`list-style-image`  将图像指定为列表项标记

例子：

```css
ul {
  list-style-image: url('sqpurple.gif');
}
```

### 定位列表项标记 - position

`list-style-position`  指定列表项标记（项目符号）的位置

_"list-style-position: outside;"_ 表示项目符号点将在列表项之外。列表项每行的开头将垂直对齐，这是默认的

_"list-style-position: inside;"_ 表示项目符号将在列表项内。由于它是列表项的一部分，因此它将成为文本的一部分，并在开头推开文本

### 删除默认设置

`list-style-type:none`  也可以用于删除标记/项目符号

> [!attention] 注意
> 列表还拥有默认的外边距和内边距。要删除此内容，请在 `<ul>` 或 `<ol>` 中添加  `margin:0`  和  `padding:0`

### 简写属性

`list-style`  是一种简写属性

它用于在一条声明中设置所有列表属性：

```css
ul {
  list-style: square inside url("sqpurple.gif");
}
```

在使用简写属性时，属性值的顺序为：

- `list-style-type`（如果指定了 list-style-image，那么在由于某种原因而无法显示图像时，会显示这个属性的值）
- `list-style-position`（指定列表项标记应显示在内容流的内部还是外部）
- `list-style-image`（将图像指定为列表项标记）

如果缺少上述属性值之一，则将插入缺失属性的默认值（如果有）

### 设置列表的颜色样式

添加到 `<ol>` 或 `<ul>` 标记的任何样式都会影响整个列表，而添加到 `<li>` 标记的属性将影响各个列表项：

```html
<!DOCTYPE html>
<html>
<head>
<style>
ol {
  background: #ff9999;
  padding: 20px;
}

ul {
  background: #3399ff;
  padding: 20px;
}

ol li {
  background: #ffe5e5;
  padding: 5px;
  margin-left: 35px;
}

ul li {
  background: #cce5ff;
  margin: 5px;
}
</style>
</head>
<body>

<ol>
  <li>Coffee</li>
  <li>Tea</li>
  <li>Coca Cola</li>
</ol>

<ul>
  <li>Coffee</li>
  <li>Tea</li>
  <li>Coca Cola</li>
</ul>

</body>
</html>
```

## 表格 | table

### 表格边框 - border

```css
table, th, td {border: 1px solid black;}
```

### 全宽表格 - width

可以覆盖整个屏幕（全宽）的表格

```css
table {width: 100%;}
```

### 合并表格边框 - border-collapse

`border-collapse`  设置是否将表格边框折叠为单一边框

允许以下值：

- _separate_：默认值。边框会被分开。不会忽略 `border-spacing` 和 `empty-cells` 属性
- _collapse_：如果可能，边框会合并为一个单一的边框。会忽略 `border-spacing` 和 `empty-cells` 属性
- _inherit_：规定从父元素继承 `border-collapse` 属性的值

### 单元格边框的距离 - border-spacing

`border-spacing` 设置相邻单元格的边框间的距离（仅当 `border-collapse=separate` 的时候生效）

允许以下值：

- _length_

  - 规定相邻单元的边框之间的距离。使用 _px_、_cm_ 等单位。不允许使用负值
  - 如果定义一个 length 参数，那么定义的是水平和垂直间距
  - 如果定义两个 length 参数，那么第一个设置水平间距，而第二个设置垂直间距

- _inherit_
  - 规定从父元素继承 `border-spacing` 属性的值

### 表格高度和宽度 - height&width

表格的高度和宽度由  `height`  和  `width`  定义

### 水平对齐 - text-align

`text-align`  设置 `<th>` 或 `<td>` 中内容的水平对齐方式

默认情况下，`<th>` 元素的内容居中对齐，而 `<td>` 元素的内容左对齐

### 垂直对齐 - vertical-align

`vertical-align`  属性设置 `<th>` 或 `<td>` 中内容的垂直对齐方式（上、下或居中）

默认情况下，表中内容的垂直对齐是居中（`<th>` 和 `<td>` 元素都是）

### 表格内边距 - padding

如需控制边框和表格内容之间的间距，请在 `<td>` 和 `<th>` 元素上使用  `padding`  属性

### 水平分隔线 - border-bottom

向 `<th>` 和 `<td>` 添加  `border-bottom`  属性，以实现水平分隔线

```css
th, td {border-bottom: 1px solid #ddd;}
```

### 可悬停表格 - :hover

在 `<tr>` 元素上使用  `:hover`  选择器，以突出显示鼠标悬停时的表格行：

```css
tr:hover {background-color: #f5f5f5;}
```

### 条状表格 - nth-child()

为了实现斑马纹表格效果，请使用  `nth-child()`  选择器，并为所有偶数（或奇数）表行添加  `background-color`：

```html
<!DOCTYPE html>
<html>
<head>
<style>
table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {background-color: #f2f2f2;}
</style>
</head>
<body>

<table>
  <tr>
    <th>Firstname</th>
    <th>Lastname</th>
    <th>Savings</th>
  </tr>
  <tr>
    <td>Bill</td>
    <td>Gates</td>
    <td>$100</td>
  </tr>
  <tr>
    <td>Steve</td>
    <td>Jobs</td>
    <td>$150</td>
  </tr>
  <tr>
    <td>Elon</td>
    <td>Musk</td>
    <td>$300</td>
  </tr>
  <tr>
    <td>Mark</td>
    <td>Zuckerberg</td>
    <td>$250</td>
  </tr>
</table>

</body>
</html>
```

### 表格颜色 - color

```css
th {
  background-color: #4CAF50;
  color: white; /* 文本颜色 */
}
```

### 响应式表格

如果屏幕太小而无法显示全部内容，则响应式表格会显示水平滚动条

在 `<table>` 元素周围添加带有  `overflow-x:auto`  的容器元素（例如 `<div>`），以实现响应式效果：

```html
<!DOCTYPE html>
<html>
<head>
<style>
table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {background-color: #f2f2f2;}
</style>
</head>
<body>

<div style="overflow-x:auto;">
  <table>
    <tr>
      <th>First Name</th>
      <th>Last Name</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
      <th>Points</th>
    </tr>
    <tr>
      <td>Bill</td>
      <td>Gates</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
      <td>50</td>
    </tr>
    <tr>
      <td>Steve</td>
      <td>Jobs</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
      <td>94</td>
    </tr>
    <tr>
      <td>Elon</td>
      <td>Musk</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
      <td>67</td>
    </tr>
  </table>
</div>

</body>
</html>

```

### 设置表格标题的位置 - caption-side

```css
caption {caption-side: top|bottom|inherit;}
```

### 隐藏空单元格的边框和背景 - empty-cells

`empty-cells` 设置是否显示表格中的空单元格（仅当 `border-collapse=separate` 的时候生效）

允许以下值：

- _hide_：不在空单元格周围绘制边框
- _show_：在空单元格周围绘制边框。默认
- _inherit_：规定应该从父元素继承 `empty-cells` 属性的值

### 表格布局 - table-layout

`table-layout` 用来显示表格单元格、行、列的算法规则

允许以下值：

- _automatic_：默认。列宽度由单元格内容设定
- _fixed_：列宽由表格宽度和列宽度设定
- _inherit_：规定应该从父元素继承 `table-layout` 属性的值

## 布局

![[CSS 布局|seamless]]

## 尾巴

> [!link] Reference 
>
> - [CSS 教程 - W3school](https://www.w3school.com.cn/css/index.asp)
