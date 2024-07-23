---
created: 2023-01-04 13:28
modified: 2023-08-27 23:39
tags:
  - 前端/HTML
---

### `<form>`

- `action`：定义在提交表单时执行的动作。如果省略 action 属性，则 action 会被设置为当前页面
- `method`：定义在提交表单时所用的 HTTP 方法（*GET*  或  *POST*）
- `name`：定义表单的名称（对于 DOM 使用：document.forms.name）
- `<fieldset>`：组合表单数据
- `<legend>`：为 `<fieldset>` 元素定义标题
- `target`：定义提交表单后在何处显示响应
- `autocomplete`：是否打开自动完成表单功能（on|off），默认开启。启用后，浏览器会根据用户之前输入的值自动填写值
- `novalidate`：当提交表单时不对其进行验证
- `accept-charset`：定义服务器用哪种字符集处理表单数据
- `enctype`：定义在发送到服务器之前，应该如何对表单数据进行编码
  - `application/x-www-form-urlencoded`：在发送前编码所有字符（默认）
  - `multipart/form-data`：不对字符编码。在包含文件上传控件的表单时，必须使用该值
  - `text/plain`：空格转换为 "+" 加号，但不对特殊字符编码
- `rel`：定义当前文档和被链接文档之间的关系

### `<input>`

- `type`：
  - **text**：文本输入框
  - **password**：密码输入框
  - **radio**：单选框
  - **checkbox**：复选框
  - **submit**：提交按钮
- `checked`：配合 `type="radio"` 使用，默认勾选
- `value`：按钮显示的名称，或输入框填充的文本
- `name`：如果要正确地被提交，每个输入字段必须设置一个 `name` 属性

![[HTML 5#HTML 5 新增的 input type]]

### `<select>`

- `<option>`：定义待选择的选项。使用 `selected` 来定义预定义选项，默认为第一个

```html
<select name="mobile">
	<option value="xiaomi" selected>Xiaomi</option>
	<option value="huawei">Huawei</option>
	<option value="vivo">Vivo</option>
	<option value="oppo">Oppo</option>
</select>
```

`<textarea>`：定义多行输入字段（文本域）

```html
<textarea name="message" rows="10" cols="30">
The cat was playing in the garden.
</textarea>
```

`<button>`：定义可点击的按钮
```html
<button type="button" onclick="alert('Hello World!')">点击</button>
```

![[HTML 5#HTML 5 新增的表单元素]]

### 输入限制

| 属性      | 描述                             |
| --------- | -------------------------------- |
| disabled  | 规定输入字段应该被禁用           |
| max       | 规定输入字段的最大值             |
| maxlength | 规定输入字段的最大字符数         |
| min       | 规定输入字段的最小值             |
| pattern   | 规定通过其检查输入值的正则表达式 |
| readonly  | 规定输入字段为只读（无法修改）   |
| required  | 规定输入字段是必需的（必需填写） |
| size      | 规定输入字段的宽度（以字符计）   |
| step      | 规定输入字段的合法数字间隔       |
| value     | 规定输入字段的默认值             |

