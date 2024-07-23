---
created: 2023-01-13 21:14
tags:
  - 前端/JavaScript
aliases:
---

### 获取 input 的值

```javaScript
document.getElementById("newTag").value;
```

### 改变 class 样式

```javaScript
let span = document.getElementById("newTag");
span.setAttribute("class", "p-3 mb-2 bg-primary text-white");
```

### 文件转二进制 / base64 / FileReader

- [前端将图片等文件变成二进制流再存入数据库，并实现二进制流显示及下载 - CSDN 博客](https://blog.csdn.net/IT_95/article/details/86611622)
- [HTML5 之 FileReader 的使用\_骆昊的博客-CSDN 博客](https://blog.csdn.net/jackfrued/article/details/8967667)

```javaScript
$("#fujian").change(function(){
	var reader = new FileReader();
        var AllowImgFileSize = 2100000; //上传图片最大值(单位字节)（ 2 M = 2097152 B ）超过2M上传失败
        var file = $("#fujian")[0].files[0];
        var imgUrlBase64;
        if (file) {
            //将文件以Data URL形式读入页面
            imgUrlBase64 = reader.readAsDataURL(file);
            console.log(imgUrlBase64);

            reader.onload = function (e) {
              //var ImgFileSize = reader.result.substring(reader.result.indexOf(",") + 1).length;//截取base64码部分（可选可不选，需要与后台沟通）
              if (AllowImgFileSize != 0 && AllowImgFileSize < reader.result.length) {
                    alert( '上传失败，请上传不大于2M的图片！');
                    return;
                }else{
                    //执行上传操作
                    console.log(reader.result);
                }
            }
         }
	}
)
```

### FormData

[JS - FormData 详解 - 简书](https://www.jianshu.com/p/29f45095d947)

### 计算文件的 MD5 值

[javascript - 前端使用 js 计算文件的 MD5 值 - 秦老爷子 - SegmentFault 思否](https://segmentfault.com/a/1190000022920399)

### 同步执行方法

[两个方式实现函数同步执行，比如先执行完 B 函数，再执行 A 函数——callback 和 async await](https://blog.csdn.net/life_wanghexu/article/details/112554460)

### js 文件引入 其他 js 文件

[js 文件中引入 js 的方法 - CSDN 博客](https://blog.csdn.net/qq_36138324/article/details/100075813)

```js
document.write("<script src='https://cdn.bootcdn.net/ajax/libs/blueimp-md5/2.19.0/js/md5.min.js'></script>");
```

### 读取 checkbox

[JS 读取 checkbox 的几种方法 - CSDN 博客](https://blog.csdn.net/widenstage/article/details/68948619)

### 类

- 在一个类中只能有一个名为 `constructor` 的特殊方法。一个类中出现多个构造函数将会抛出一个  *SyntaxError*  错误

- 在一个构造方法中可以使用 `super` 关键字来调用一个父类的构造方法

- 如果没有显式指定构造方法，则会添加默认的构造方法：`constructor() {}`

定义一个类

```js
class Car {
  constructor(name, year) {
    this.name = name;
    this.year = year;
  }
}
```

创建对象

```js
let myCar2 = new Car("Linkco", 2023);
```

### 数组

[数组（new Array）- CSDN 博客](https://blog.csdn.net/qq_41670147/article/details/103166207)

```js
let arr = [];
let obj = {};
let arr = [1, 2, 3];

let arr1 = new Array(); //创建了一个空的数组
let arr2 = new Array(2); //里面的2表示数组的长度为2，输出结果我2个空的数组元素
let arr3 = new Array(2, 3); //等价于[2,3]表示里面有2个数组元素为2,3
```

| 方法名            | 说明                                             | 返回值               |
| ----------------- | ------------------------------------------------ | -------------------- |
| push(args...)     | 末尾添加一个或多个元素，修改原数组               | 返回新的长度         |
| pop()             | 删除数组最后一个元素，把数组长度减 1，修改原数组 | 返回它删除的元素的值 |
| unshift(args....) | 向数组的开头添加一个或更多元素，修改原数组       | 返回新的长度         |
| shift()           | 删除数组的第一个元素，数组长度减 1，修改原数组   | 返回第一个元素的值   |

### jQuery AJAX

[jquery 中 ajax 参数及其返回值总结 - CSDN 博客](https://blog.csdn.net/xiongdaandxiaomi/article/details/80214567)

### 删除旧的子节点

```js
/**  
 * 删除旧的子节点  
 * @param e 父节点  
 */  
function empty(e) {  
    while (e.firstChild) {  
        e.removeChild(e.firstChild);  
    }  
}
```

### 获得当前时间的时间戳

```js
new Date().getTime();
```