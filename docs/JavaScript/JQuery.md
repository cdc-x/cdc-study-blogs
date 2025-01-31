# JQuery

## JQuery 简介

### **一、JQuery介绍**

​		JQuery 是一个轻量级的、兼容多浏览器的 JavaScript 库，它使用户能够更方便地处理 HTML Document、Events、实现动画效果、方便地进行 Ajax 交互，能够极大地简化 JavaScript 编程。（JQuery 的宗旨就是**Write less，do more**）。与 JavaScript 编程相比，JQuery 主要有以下优势：

- 一款轻量级的 JS 框架。JQuery 核心 JS 文件才几十kb，不会影响页面加载速度。
- 丰富的 DOM 选择器。JQuery 的选择器用起来很方便，比如要找到某个 DOM 对象的相邻元素，JS 可能要写好几行代码，而 JQuery 一行代码就搞定了，再比如要将一个表格的隔行变色，JQuery 也是一行代码搞定。
- 链式表达式。JQuery 的链式操作可以把多个操作写在一行代码里，更加简洁。
- 事件、样式、动画支持。JQuery 还简化了 JS 操作 CSS 的代码，并且代码的可读性也比 JS 要强。
- Ajax操作支持。JQuery 简化了 AJAX 操作，后端只需返回一个 JSON 格式的字符串就能完成与前端的通信。
- 跨浏览器兼容。JQuery 基本兼容了现在主流的浏览器，不用再为浏览器的兼容问题而伤透脑筋。
- 插件扩展开发。JQuery 有着丰富的第三方的插件，例如：树形菜单、日期控件、图片切换插件、弹出窗口等等基本前端页面上的组件都有对应插件，并且用 JQuery 插件做出来的效果很炫，并且可以根据自己需要去改写和封装插件，简单实用。

###  **二、JQuery 导入方式**

#### 2.1 下载文件本地导入

**JQuery官网：**`https://jquery.com/`

**JQuery API 中文文档：**`https://jquery.cuishifeng.cn/`

- 访问官网，选择对应版本进行下载

![](/static/img/JavaScript专题/jquery1.png) 

![](/static/img/JavaScript专题/jquery2.png) 

- 先在本地新建一个名为 `JQuery-3.5.1.js` 的空文件，点击压缩版链接，将打开的代码内容直接拷贝到本地新建的 JS 文件中即可；
- 在 HTML 文件中通过 `script` 标签导入本地文件使用

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>  <!--导入本地的 jquery 文件-->
</head>
```

#### 2.2 通过 CDN 导入

**常用免费CDN：** `https://www.bootcdn.cn/`

- 访问 CDN 地址，选择对应的 JQuery 版本

![](/static/img/JavaScript专题/jquery3.png) 

![](/static/img/JavaScript专题/jquery4.png) 

- 在 HTML 文件中通过 `script` 标签导入网址链接使用

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.js"></script>
</head>
```

### **三、JQuery 基础语法**

`$(selector).action()`

- **selector：**选择器，用于确定操作对象
- **action：**对应的操作

### **四、JQuery对象和DOM对象**

​		JQuery 对象是通过 JQuery 包装 DOM 对象后产生的对象，是 JQuery 独有的，它就可以使用 JQuery 里特有的方法。虽然 JQuery 对象是包装 DOM 对象后产生的，但是 JQuery对象无法使用 DOM 对象的任何方法，同理 DOM对象也没不能使用 JQuery 里的方法。在使用 JQuery 时，一定要注意区分操作的对象的类型。

<img src="/static/img/JavaScript专题/jquery5.png" style="zoom:67%;" /> 

JQuery 对象和 DOM 对象可以互相转换：

<img src="/static/img/JavaScript专题/jquery6.png" style="zoom:70%;" />  

## 查找标签

### **一、选择器**

现有一个 HTML 文档，其机构如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>
</head>
<body>
    <div id="d1">div
        <span>div>span</span>
        <p>div>p
            <span>div>p>span</span>
        </p>
    </div>
    <p class="c1">p</p>
    <span>span</span>
</body>
</html>
```

#### 1.1 标签选择器

```javascript
// 根据标签名查找对应的标签
$("span")
```

<img src="/static/img/JavaScript专题/jquery7.png" style="zoom:70%;" /> 

#### 1.2 id选择器

```javascript
// 根据 id 值查找标签
$("#d1")
```

<img src="/static/img/JavaScript专题/jquery8.png" style="zoom:70%;" /> 

#### 1.3 class选择器

```javascript
// 查找 class 中包含指定值的标签
$(".c1")
```

<img src="/static/img/JavaScript专题/jquery9.png" style="zoom:70%;" /> 

#### 1.4 选择器混用

```javascript
// 查找包含 c1 类的 div 标签
$("div.c1")

// 查找 id 是 d1 的 div 标签
$("div#d1")
```

#### 1.5 组合选择器

```javascript
// 查找满足下列条件之一的标签
$("div,.c1,#d1,span")
```

#### 1.6 层级选择器

```javascript
// 查找条件不止针对标签名，也可以针对其他选择器
$("div p")  // 查找 div 标签内部的所有标签（子子孙孙），即后代选择器
$("div>p")  // 查找 div 标签所有的子标签 p（只查第一层），即子代选择器
$("div+p")  // 查找紧挨在 div 标签后面的 p 标签，即毗邻选择器
$("div~p")  // 查找 div 后面的所有 p 标签，即弟弟选择器
```

#### 1.7 属性选择器

```html
<body>
    <input type="text" username="aaa">
    <input type="text" username="bbb">
    <p username="ccc"></p>
</body>
```

```javascript
// 查找含有 username 属性的标签
$('[username]')

// 查找username属性值为aaa的标签
$('[username="aaa"]')

// 查找username属性值为ccc的p标签
$('p[username="ccc"]')

// 也可以根据标签自带的属性查找
$('[type="text"]')
```

### **二、筛选器**

现有一个 HTML 文档，其机构如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>  <!--导入本地的 jquery 文件-->
</head>
<body>
    <ul>
        <li>101</li>
        <li>102</li>
        <li>103</li>
        <li>104</li>
        <li>105</li>
        <li id="d1">106</li>
        <li>107</li>
        <li>108</li>
        <li class="c1">109</li>
        <li>110</li>
    </ul>
</body>
</html>
```

```javascript
// 筛选 ul 下第一个 li
$("ul li:first")

// 筛选 ul 下最后一个 li
$("ul li:last")

// 筛选 ul 下索引等于2的那个元素
$("ul li:eq(2)")

// 匹配所有索引值为偶数的元素，从 0 开始计数
$("ul li:even")

// 匹配所有索引值为奇数的元素，从 0 开始计数
$("ul li:odd")

// 匹配所有大于3的元素
$("ul li:gt(3)")

// 匹配所有小于3的元素
$("ul li:lt(3)")

// 筛选移除所有满足条件后的标签
$("ul li:not('#d1')")

// 选取所有包含一个或多个标签在其内的标签(指的是从后代元素找)
$("ul:has('#d1')")  // 后代中有元素 id 是 d1 的 ul 标签
$("li:not(:has(a))") // 找到所有后代中不含 a 标签的 li 标签
```

### **三、表单筛选器**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>
</head>
<body>
    <p>用户名：<input type="text"></p>
    <p>密 码：<input type="password"></p>
    <input type="button" value="登录">
</body>
</html>
```

对于表单类标签，我们也可以通过属性选择器定位到想要操作的标签：

```javascript
// 查找用户名输入框
$('input[type="text"]')

// 查询结果
// S.fn.init [input, prevObject: S.fn.init(1)]
```

但是这样写查询语句太长了，显然违背了 JQuery 的宗旨。因此对于表单类标签，JQuery 提供了一套专门的表单筛选器用于定位元素：

```javascript
// 查询用户名输入框
$(":text")

// 查询结果
// S.fn.init [input, prevObject: S.fn.init(1)]
```

表单筛选器还支持筛选以下 `input` 类型：

```javascript
:text
:password
:file
:radio
:checkbox
:submit
:reset
:button
```

 除此以外，表单筛选器还可以通过表单对象属性来定位元素：

```javascript
:enabled
:disabled
:checked
:selected
```

**示例1：**

```html
<body>
    <input type="checkbox">111
    <input type="checkbox" checked>222  <!--222默认选中-->
    <input type="checkbox">333
</body>
```

```javascript
$(":checked")  // 返回值为默认选中的那个标签，即 222
```

此时在页面上再选中 333，再次查询将查到两个标签

```javascript
$(":checked")  // 返回 S.fn.init(2) [input, input, prevObject: S.fn.init(1)] 
```

**示例2：**

```html
<body>
    <select name="" id="">
        <option value="111" selected>111</option> <!--默认选中111-->
        <option value="222">222</option>
        <option value="333">333</option>
    </select>
</body>
```

```javascript
$(":selected")  // 返回 111 对应的 option
```

**注意：**当表单中既有 `selected` 属性，又有 `checked` 属性时，会存在以下现象：

```html
<body>
    <input type="checkbox">111
    <input type="checkbox" checked>222
    <input type="checkbox">333
    <select name="" id="">
        <option value="111" selected>111</option>
        <option value="222">222</option>
        <option value="333">333</option>
    </select>
</body>
```

```javascript
$(":checked")  // S.fn.init(2) [input, option, prevObject: S.fn.init(1)]

$(":selected") // S.fn.init [option, prevObject: S.fn.init(1)]
```

通过 `checked` 属性去查找，会将被 `selected` 的 `option` 标签也查找到，但是反过来通过 `selected` 去查找，不会找到对应被 `checked` 的 `input` 标签。

### **四、筛选器方法**

```html
<body>
    <span id="d1">span</span>
    <span>span</span>
    <div id="d2">
        <span>div>span</span>
        <p>div>p
            <span id="d3">div>p>span</span>
        </p>
        <span>div>span</span>
    </div>
    <span>span</span>
    <span>span</span>
    <span class="c1">span</span>
</body>
```

#### 4.1 查找下一个元素

```javascript
// 查找 #d1 同层级的下一个元素
$("#d1").next()  // S.fn.init [span, prevObject: S.fn.init(1)]

// 查找 #d1 同层级后面的所有元素
$("#d1").nextAll()  // S.fn.init(5) [span, div#d2, span, span, span.c1]

// 查找 #d1 同层级后面的所有元素直到有 .c1 样式类出现为止，且不包含 .c1
$("#d1").nextUntil(".c1")  // S.fn.init(4) [span, div#d2, span, span]
```

#### 4.2 查找上一个元素

```javascript
// 查找 .c1 同层级的上一个元素
$(".c1").prev()  // S.fn.init [span, prevObject: S.fn.init(1)]

// 查找 .c1 同层级上面的所有元素
$(".c1").prevAll()  // S.fn.init(5) [span, span, div#d2, span, span#d1]

// 查找 .c1 同层级上面的所有元素直到有 #d2 样式类出现为止，且不包含 #d2
$(".c1").prevUntil("#d2")  // S.fn.init(2) [span, span, prevObject: S.fn.init(1)]
```

#### 4.3 查找父亲元素

```javascript
// 查找 #d3 元素的父元素
$("#d3").parent()  // S.fn.init [p, prevObject: S.fn.init(1)]
// 继续向上查找父元素
$("#d3").parent().parent()  // [div#d2, prevObject: S.fn.init(1)]
$("#d3").parent().parent().parent()  // [body, prevObject: S.fn.init(1)]
$("#d3").parent().parent().parent().parent()  // [html]
$("#d3").parent().parent().parent().parent().parent()  // [document]

// 直接查询 #d3 元素的所有祖先元素
$("#d3").parents()  // [p, div#d2, body, html, prevObject: S.fn.init(1)]

// 查询 #d3 祖先元素，直到出现 #d2 样式类为止，且不包括 #d2
$("#d3").parentsUntil("#d2")  // S.fn.init [p, prevObject: S.fn.init(1)]
```

#### 4.4 查找儿子和兄弟元素

```javascript
// 查找 #d2 的所有儿子元素
$("#d2").children()  // [span, p, span, prevObject: S.fn.init(1)]

// 查找 #d2 同层级的所有兄弟元素  
$("#d2").siblings()  // [span#d1, span, span, span, span.c1]
```

#### 4.5 其他常用方法

- `find`  查找，搜索所有与指定表达式匹配的元素，多个表达式之间用逗号隔开

```javascript
// 查找 div 元素内部的 p 元素
$("div").find("p")  // 等价于 $("div p")

// 查找 div 元素内部的 p 元素和 span 元素
$("div").find("p, span")  // 等价于 $("div p,div span")
```

- `filter` 筛选，筛选出与指定表达式匹配的元素集合，多个表达式之间用逗号隔开

```javascript
// 筛选含有 .c1 样式类的 div 元素
$("div").filter(".c1")  // 等价于 $("div.c1")

// 筛选含有 .c1 和 #d1 样式类的 span 元素
$("span").filter(".c1,#d1")  // 等价于 $("span.c1, span#d1")
```

- `first`  获取匹配的第一个元素

```javascript
// 查询 div 元素下第一个 span 元素
$("div span").first()  // 等价于 $("div span:first")
```

- `last`  获取匹配的最后一个元素

```javascript
// 查询 div 元素下最后一个 span 元素
$("div span").last()  // 等价于 $("div span:last")
```

- `not`  从匹配元素的集合中删除与指定表达式匹配的元素

```javascript
// 查找 div 元素中，不包含 #d3 样式的 span 元素
$("div span").not("#d3")  // 等价于 $("div span:not('#d3')")
```

- `has`  保留包含特定后代的元素，去掉那些不含有指定后代的元素

```javascript
// 查询后代元素中有 #d3 样式类的 div
$("div").has("#d3")  // 等价于 $("div:has('#d3')")
```

- `eq`  索引值等于指定值的元素

```javascript
// 在所有 div 元素的后代 span 元素中，查询索引值为 1 的 span 元素
$("div span").eq(1)
```

## 操作标签

### **一、样式类操作**

```html
<body>
    <p id="d1">hehehehe</p>
</body>
```

#### 1.1 添加样式类

```javascript
// 添加指定的CSS类名
$("#d1").addClass("c1")  // S.fn.init [p#d1.c1]
```

#### 1.2 移除样式类

```javascript
// 移除指定的CSS类名
$("#d1").removeClass("c1")  // S.fn.init [p#d1]
```

#### 1.3 判断样式类

```javascript
// 判断指定样式类是否存在
$("#d1").hasClass("c1")  // false
```

#### 1.4 切换样式类

```javascript
// 切换CSS类名，如果有就移除，如果没有就添加
$("#d1").toggleClass("c1")  // S.fn.init [p#d1.c1]
$("#d1").toggleClass("c1")  // S.fn.init [p#d1]
```

### **二、CSS 操作**

```html
<body>
    <p id="d1">hehehehe</p>
    <p>hahahaha</p>
</body>
```

可以通过 `css("key","value")` 直接给元素设置样式，等价于 DOM 操作中的 `tag.style.key=value`

```javascript
// 将第一个p元素的文本内容变为红色
$("#d1").css("color", "red")
```

在 JQuery 中，只要调用 JQuery 方法后返回的还是 JQuery 对象，那么就可以继续调用 JQuery 的方法，我们称这种操作为 `链式操作`，也正是因为这种机制，我们可以实现在一行代码中操作多个标签：

```javascript
// 一行代码完成将两个p标签的文本分别设置成红色和蓝色
$("#d1").css("color", "red").next().css("color", "blue")
```

该机制的核心就是 `调用 JQuery 方法后返回的还是 JQuery 对象`，我们可以通过一个 Python 小示例简单模仿一下链式操作的原理：

```python
class MyClass(object):

    def func1(self):
        print("hahaha, func1")
        return self  # 返回操作对象本身

    def func2(self):
        print("hehehe, func2")
        return self  # 返回操作对象本身

obj = MyClass()
obj.func1().func2()  # 执行完 func1 之后，返回值还是当前的 obj
```

### **三、位置操作**

#### 3.1 offset()

- 括号内不传值，用于获取匹配元素 `距离当前浏览器窗口` 的相对位置
- 括号内传值，用于设置元素到距离窗口左上角的偏移位置处

```html
<!--在页面上画一个红色的盒子-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>  <!--导入本地的 jquery 文件-->
    <style>
        body {
            margin: 0;
            padding: 0;
        }

        #d1 {
            height: 200px;
            width: 200px;
            background-color: red;
        }
    </style>
</head>
<body>
	<div id="d1"></div>
</body>
</html>
```

```javascript
$("#d1").offset()  // {top: 0, left: 0}，距离窗口左上角顶部为0，左侧为0
```

```javascript
$("#d1").offset({top: 300, left: 300})
$("#d1").offset()  // {top: 300, left: 300}，距离窗口左上角顶部为300px，左侧为300px
```

#### 3.2 position()

和 `.offset()` 方法功能类似，但是 `position()` 方法获取和设置的是匹配元素相对于父级元素的位移。

#### 3.3 scrollTop()

- 括号内不传值，获取匹配元素相对滚动条顶部的偏移
- 括号内传值，设置匹配元素到相对滚动条顶部的偏移位置处

<img src="/static/img/JavaScript专题/jquery10.png" style="zoom:67%;" /> 

```javascript
$(window).scrollTop()  // 950.8571166992188
```

```javascript
$(window).scrollTop(0)  // 滚动条直接返回页面顶部
```

#### 3.4 scrollLeft()

和 `scrollTop()` 用法类似，`scrollLeft()` 用于获取和操作水平方向滚动条。

### **四、尺寸操作**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        div {
            margin: 1px 2px 3px 4px;
            padding: 4px 3px 2px 1px;
            border: 1px solid black;
        }
    </style>
</head>
<body>
	<div>hahaha</div>
</body>
</html>
```

#### 4.1 height()

- 获取匹配标签文本内容的高度

```javascript
$("div").height()  // 21
```

#### 4.2 width()

- 获取匹配标签文本内容的宽度

```javascript
$("div").width()  // 2183
```

#### 4.3 innerHeight()

- 获取匹配标签文本内容和 `padding` 的总高度

```javascript
$("div").innerHeight()  // 27
```

#### 4.4 innerWidth()

- 获取匹配标签文本内容和 `padding` 的总宽度

```javascript
$("div").innerWidth()  // 2187
```

#### 4.5 outerHeight()

- 获取匹配标签文本内容、 `padding` 和 `border` 的总高度

```javascript
$("div").outerHeight()  // 29
```

#### 4.6 outerWidth()

- 获取匹配标签文本内容、 `padding` 和 `border` 的总宽度

```javascript
$("div").outerWidth()  // 2189
```

### **五、文本操作**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>
</head>
<body>
    <div>
        <p>hahahaha</p>
    </div>
</body>
</html>
```

#### 5.1 text()

`text()` 方法等价于 DOM 操作中的 `innerText()` 方法，可用于获取和设置元素的内容。获取内容时，只能获取文本内容，设置内容时无法识别 HTML 标签。

```javascript
// 获取元素内容
$("div").text()
```

<img src="/static/img/JavaScript专题/jquery11.png" style="zoom:80%;" /> 

```javascript
// 设置元素内容
$("div").text("hello")
```

```javascript
// 设置内容包含HTML标签
$("div").text("<h1 style='color:red'>hello</h1>")
```

<img src="/static/img/JavaScript专题/jquery12.png" style="zoom:80%;" /> 

#### 5.2 html()

`html()` 方法等价于 DOM 操作中的 `innerHTML()` 方法，可用于获取和设置元素的内容。获取内容时，既能获取文本内容，又可以获取标签信息；设置内容时支持识别 HTML 标签。

```javascript
// 获取元素内容
$("div").html()
```

<img src="/static/img/JavaScript专题/jquery13.png" style="zoom:80%;" /> 

```javascript
// 设置元素内容
$("div").html("hello")
```

```javascript
// 设置内容包含HTML标签
$("div").html("<h1 style='color:red'>hello</h1>")
```

<img src="/static/img/JavaScript专题/jquery14.png" style="zoom:80%;" /> 

#### 5.3 val()

`val()` 方法等价于 DOM 操作中的 `value()` 方法，可以获取和设置表单类型的元素内容。

```html
<body>
    <input type="text" id="d1">
    <input type="file" id="d2">
</body>
```

```javascript
// 获取输入框中的值
$("#d1").val()  // hahaha

// 设置输入框中的值
$("#d1").val("hehehehe")
```

对于获取文件对象的值时，JQuery 并没有直接提供类似 DOM 中 `files` 的方法，所以我们可以先将 JQuery 对象转换成 DOM 对象再进行取值操作：

```javascript
// 获取文件内容
$("#d2")[0].files[0]
```

<img src="/static/img/JavaScript专题/jquery15.png" style="zoom:80%;" /> 

对于 `checkbox` 和 `select ` 类型元素，`val()` 方法只能取到内部第一个元素的值，设置值时，会将内部所有的元素的 `value` 都设置成同一个值：

```html
<body>
    <input type="checkbox" value="basketball" name="hobby">篮球
    <input type="checkbox" value="football" name="hobby">足球

    <select id="s1">
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
    </select>
</body>
```

```javascript
// 获取值
$("[name='hobby']").val();  // basketball
$("option").val()  // 1
```

```javascript
// 设置值
$("[name='hobby']").val("111");
$("option").val("111")
```

<img src="/static/img/JavaScript专题/jquery16.png" style="zoom:70%;" /> 

### **六、属性操作**

```html
<body>
    <div id="d1" username="cdc"></div>

    <input id="d2" type="checkbox" value="111" checked>111
    <input id="d3" type="checkbox" value="222">222
    <input id="d4" type="checkbox" value="333">333
</body>
```

#### 6.1 attr()

- 括号内传一个参数，表示获取匹配元素对应的属性值
- 括号内传两个参数，表示为匹配元素设置对应的属性及属性值

```javascript
let $divEle = $("#d1")

// 获取指定的属性值
$divEle.attr("username")  // cdc

// 设置属性值
$divEle.attr("age", "18")

// 设置多个属性值
$divEle.attr({"name":"cdc", "address":"nanjing"})
```

#### 6.2 removeAttr()

删除指定的属性。

```javascript
$divEle.removeAttr("username")
```

#### 6.3 prop()

针对 `checkbox`、`radio` 等需要用户选择的表单元素，我们尝试使用 `attr()` 方法去操作他们的属性：

```javascript
$("#d2").attr("checked")  // "checked"
$("#d3").attr("checked")  // undefined
$("#d4").attr("checked")  // undefined
```

目前返回的结果是合理的，一开始只有 `#d2` 是默认选中的。此时我们将其他两个选项`全部选中`，再查询一下对应的属性：

```javascript
$("#d2").attr("checked")  // "checked"
$("#d3").attr("checked")  // undefined
$("#d4").attr("checked")  // undefined
```

返现结果还是和最开始一样，但是按理来说，返回的结果应该都是 `checked`，我们再将三个选项`全都取消选中`后再次查询：

```javascript
$("#d2").attr("checked")  // "checked"
$("#d3").attr("checked")  // undefined
$("#d4").attr("checked")  // undefined
```

返回的结果还是和最开始一样，但是理论上返回的应该都是 `undefined`。

对于 `checkbox`、`radio` 等需要用户选择的表单元素，`attr()` 方法是没法操作他们的属性的，需要用到专门的方法 `prop`：

```javascript
// prop 方法返回的是一个布尔值，如果查询的对象被选中，则返回true，否则返回false
// 最开始值默认选中 #d2
$("#d2").prop("checked")  // true
$("#d3").prop("checked")  // false
$("#d4").prop("checked")  // false

// 三个选项都选中
$("#d2").prop("checked")  // true
$("#d3").prop("checked")  // true
$("#d4").prop("checked")  // true

// 三个选项都取消选中
$("#d2").prop("checked")  // false
$("#d3").prop("checked")  // false
$("#d4").prop("checked")  // false
```

也可以通过 `prop()` 方法进行设置是否选中元素：

```javascript
$("#d2").prop("checked","true")  // 元素被选中
$("#d2").prop("checked","false")  // 元素取消选中
```

**补充：**`prop和attr的区别`

- attr 全称 attribute (属性) 

- prop 全称 property (属性)

虽然都是属性，但他们所指的属性并不相同，attr 所指的属性是 HTML 标签属性，而 prop 所指的是 DOM对象属性，可以认为 attr 是显式的，而 prop 是隐式的。

举个例子：

```html
<input type="checkbox" id="i1" value="1">
```

针对上面的代码，

```javascript
$("#i1").attr("checked")  // undefined
$("#i1").prop("checked")  // false
```

可以看到 attr 获取一个标签内没有的东西会得到 undefined，而 prop 获取的是这个 DOM 对象的属性，因此checked 为 false。

如果换成下面的代码：

```html
<input type="checkbox" checked id="i1" value="1">
```

再执行：

```javascript
$("#i1").attr("checked")   // checked
$("#i1").prop("checked")  // true
```

这已经可以证明 attr 的局限性了，它的作用范围只限于 HTML 标签内的属性，而 prop 获取的是这个DOM对象的属性，选中返回 true，没选中返回 false。

再看一下针对`自定义属性`，attr 和 prop又有什么区别：

```javascript
<input type="checkbox" checked id="i1" value="1" username="cdc">
```

执行以下代码：

```javascript
$("#i1").attr("username")   // "cdc"
$("#i1").prop("username")  // undefined
```

可以看到 prop 不支持获取标签的自定义属性。

**总结：**

- 对于标签上有的能看到的属性和自定义属性都用 `attr`
- 对于返回布尔值的，比如 `checkbox`、`radio` 和 `option` 是否被选中都用 `prop`

## 文档处理

```html
<body>
    <div id="d1">div
        <span>div>span</span>
        <p id="d2">div>p</p>
        <span>div>span</span>
    </div>
</body>
```

### **一、创建标签**

**语法：**`$("tag")`

```javascript
// 创建一个 p 标签
$("<p>")  // 等价于DOM操作 createElement("p")

// 创建一个 p 标签并为其添加相关的文本和样式
let $pEle = $("<p>")
$pEle.attr("class", "c1")
$pEle.text("hello啊")
```

### **二、内部尾部添加标签**

```javascript
// 方式一：在 #d1 内部最后面添加创建的p标签
$("#d1").append($pEle)  // 也可以直接添加DOM对象  $("#d1").append($pEle[0])

// 方式二：
$pEle.appendTo("#d1")

// 方式一和方式二效果一样，只是执行的对象不一样而已。方式一表示在#d1内部最后面添加创建的p标签，方式二表示把创建的p标签添加到#d1内部最后面
```

<img src="/static/img/JavaScript专题/jquery17.png" style="zoom:70%;" /> 

### **三、内部头部添加标签**

```javascript
// 方式一：
$("#d1").prepend($pEle)

// 方式二：
$pEle.prependTo("#d1")
```

<img src="/static/img/JavaScript专题/jquery18.png" style="zoom:70%;" /> 

### **四、外部尾部添加标签**

```javascript
// 方式一：
$("#d2").after($pEle)

// 方式二：
$pEle.insertAfter("#d2")
```

<img src="/static/img/JavaScript专题/jquery19.png" style="zoom:70%;" /> 

### **五、外部头部添加标签**

```javascript
// 方式一：
$("#d2").before($pEle)

// 方式二：
$pEle.insertBefore("#d2")
```

<img src="/static/img/JavaScript专题/jquery20.png" style="zoom:70%;" /> 

### **六、删除/清空标签**

```javascript
$("#d1").remove()  // 删除标签，包含标签本身和内部的所有内容
$("#d1").empty()  // 操作的标签还在，标签内部的内容都被删除了
```

## 事件

### **一、绑定事件**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>
</head>
<body>
    <button id="d1">点我</button>
    <button id="d2">Tap</button>

    <script>
        // 方式一：
        $("#d1").click(function () {
            console.log("你好")
        });

        // 方式二：功能更强大，支持事件委托
        $("#d2").on("click", function () {
            console.log("hello")
        })
    </script>
</body>
</html>
```

### **二、常用事件**

```javascript
click()  // 鼠标点击
dblclick()  // 鼠标双击
mousedown()  // 鼠标按下
mouseup()  // 鼠标抬起
mousemove()  // 鼠标移动
mouseover()/mouseout()  // 鼠标穿过元素或者被选元素的子元素
mouseenter()/mouseleave()  // 鼠标悬浮
hover()
blur()  // 失去焦点
focus()  // 聚焦
change()  // 内容变化
keyup()  // 键盘抬起
keydown()  // // 键盘按下
scroll()  // 监听窗口滚动

// 更多事件案例请访问：https://github.com/cdc-x/project/tree/master/JQueryDemo
```

### **三、阻止事件后续执行**

```html
<body>
    <form action="">
        <span style="color: red" id="d1"></span>
        <input type="submit" id="d2">
    </form>

    <script>
        $("#d2").click(function () {
            $("#d1").text("hehehe");
        })
    </script>
</body>
```

上述示例想要实现点击提交按钮时，显示 `span` 标签的内容，实际运行时效果如下：

<img src="/static/img/JavaScript专题/jquery21.gif" style="zoom:70%;" /> 

这是由于除了要执行我们绑定的事件外，`form` 本身也有默认的事件要执行，即提交数据刷新页面，所以虽然我们绑定的事件被执行了，但是效果可能和我们期望的差距比较大，没能固化显示 `span` 的内容。当然，我们是可以阻止事件的后续执行的，主要有以下方式：

```javascript
<script>
    // 方式一：
    $("#d2").click(function () {
        $("#d1").text("hehehe");
        return false
    });

    // 方式二：
    // $("#d2").click(function (e) {  // e为一个形参，实际上是一个内置的对象
    //     $("#d1").text("hehehe");
    //     e.preventDefault();  // 调用内置对象的方法阻止事件后续执行
    // })
</script>
```

<img src="/static/img/JavaScript专题/jquery22.gif" style="zoom:70%;" /> 

### **四、阻止事件冒泡**

​		对于层级嵌套的标签，若每一级标签都绑定了事件，当最里层的标签事件执行完后，会继续去执行上一层的事件，这种现象称为 `事件冒泡` 。

```html
<body>
    <div id="d1">div
        <p id="d2">div>p
            <span id="d3">div>p>span</span>
        </p>
    </div>

    <script>
        $("#d1").click(function () {
            alert("div")
        });

        $("#d2").click(function () {
            alert("p")
        });

        $("#d3").click(function () {
            alert("span")
        });
    </script>
</body>
```

<img src="/static/img/JavaScript专题/jquery23.gif" style="zoom:70%;" /> 

上述示例中，`span` 标签绑定的事件执行后，鼠标并未移动到上层的标签，但是对应的事件还是被执行了，这就是冒泡现象。显然这种现象对页面效果的影响是比较大的，我们可以通过以下方法阻止事件冒泡：

```javascript
$("#d3").click(function (e) {
        alert("span");

        // 方式一：直接在事件方法内部 return false
        // return false

        // 方式二：通过内置对象方法阻止冒泡
        e.stopPropagation()
    });
```

### **五、事件委托**

```html
<body>
    <button>点我点我</button>

    <script>
        $("button").click(function () {
            alert("我拒绝！！！")
        })
    </script>
</body>
```

现在有一个 HTML 文档树，页面中有一个按钮元素，且针对页面上所有的按钮都绑定了一个点击事件，效果如下：

<img src="/static/img/JavaScript专题/jquery24.gif" style="zoom:70%;" />  

我们再动态创建一个按钮，并添加在现有按钮的后面：

```html
<body>
    <button>点我点我</button>

    <script>
        $("button").click(function () {
            alert("我拒绝！！！")
        });
		
        // 动态添加一个按钮
        let $btnEle = $("<button>");
        $btnEle.text("再来一次");
        $("body").append($btnEle);
    </script>
</body>
```

分别点击两个按钮：

<img src="/static/img/JavaScript专题/jquery25.gif" style="zoom:70%;" /> 

观察效果可以发现，按钮点击事件对一开始存在的按钮元素是生效的，但是对于我们后面动态添加的元素并没有生效，这是因为事件并不会作用于动态产生的元素。我们可以通过事件委托来解决这个问题：

```html
<body>
    <button>点我点我</button>

    <script>
        let $btnEle = $("<button>");
        $btnEle.text("再来一次");
        $("body").append($btnEle);

        // 绑定事件时传三个参数，表示进行事件委托
        // 在一定范围内，将事件委托给某个标签，无论该标签是事先存在的还是后面动态创建的
        // 下述代码表示，只要在body内部的button都委托一个点击事件(范围不仅仅是body,也可以具体到            一个div或者其他元素内部)
        $("body").on("click", "button", function () {  
            alert("我拒绝！！！")
        })
    </script>
</body>
```

<img src="/static/img/JavaScript专题/jquery26.gif" style="zoom:70%;" />

### **六、页面加载**

在 DOM 中，可以通过 `window.onload` 方法等待页面元素加载结束后再执行对应的代码。同样的，在 JQuery 中也有等待页面加载的方法：

```javascript
<script>
    $(document).ready(function () {
        // 在这里写 JS 代码
    })

    // 可以简写
    $(function () {
        // 在这里写 JS 代码
    })
</script>
```

| 区别     | window.onload                                                | $(document).ready                                            |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 执行时机 | 必须等网页中所有的元素都加载完成后（包括图片等）才能执行被包裹的代码。如果有图片加载时间过长或者失败，包裹的代码都不会执行。 | 只要等待页面中的DOM结构加载完成，就可以执行被包裹的代码，不用等待图片等资源文件的加载。 |
| 执行次数 | 只能执行一次，如果执行多次，后一次会覆盖前一次的执行。       | 可以执行多次，且都不会覆盖上次的执行。                       |

### **七、动画效果**

```javascript
// 动画效果示例

show(5000)   // 5秒后显示
hide(5000)   // 5秒后消失
toggle(5000)  // 如果元素存在，5秒后消失；如果不存在，5秒后就显示。
slideDown(5000)  // 5秒后向下滑动显示
slideUp(5000)  // 5秒后向上滑动消失
slideToggle(5000)
fadeIn(5000)  // 5秒后淡入
fadeOut(5000) // 5秒后淡出
fadeTo(5000，0.4)  // 5秒后淡出到透明度0.4
fadeToggle()

// 自定义动画
animate()
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="JQuery-3.5.1.js"></script>
    <style>
        div {
            height: 100px;
            width: 100px;
            background-color: red;
        }
    </style>
</head>

<body>
<button id="d1">点我</button>
<div></div>

<script>
    $("#d1").click(function () {
        // 可以传三个参数，第一个为对象，第二个为时间，第三个为回调函数
        // 点击按钮，一秒后改变盒子的大小，再显示文本
        $("div").animate(
            {"height": 600, "width": 600},
            1000, function () {
                $("div").text("哈哈哈哈")
            }
        )
    })
</script>
</body>
</html>
```

<img src="/static/img/JavaScript专题/jquery27.gif" style="zoom:70%;" /> 

```html
<!--点赞效果-->
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <title>点赞动画示例</title>
  <script src="JQuery-3.5.1.js"></script>
  <style>
    div {
      position: relative;
      display: inline-block;
    }
    div>i {
      display: inline-block;
      color: red;
      position: absolute;
      right: -16px;
      top: -5px;
      opacity: 1;
    }
  </style>
</head>
<body>

<div id="d1">点赞</div>
<script src="jquery-3.2.1.min.js"></script>
<script>
  $("#d1").on("click", function () {
    var newI = document.createElement("i");
    newI.innerText = "+1";
    $(this).append(newI);
    $(this).children("i").animate({
      opacity: 0
    }, 1000)
  })
</script>
</body>
</html>
```

`animate` 方法还可以和延时方法 `delay` 一起使用：

```javascript
$("#d1").click(function(){
		$("div").animate({height:"300px"});  // 盒子的高度先变为300px
		$("div").animate({width:"600px"});  // 盒子的宽度再变为600px
    	// 延时 1200 毫秒后，盒子的高度变为900px
		$("div").delay(1200).animate({height:"900px"});
});
```

## 常用方法

### **一、each 方法**

**语法：**`jquery对象.each(function(){...})`

**作用：** 将 JQuery 对象中的每一个元素都放入 function 中去执行，即相当于一个简易版的 for 循环

```html
<body>
    <div>1</div>
    <div>2</div>
    <div>3</div>
    <div>4</div>
    <div>5</div>
    <div>6</div>
    <div>7</div>
    <div>8</div>
    <div>9</div>
    <div>10</div>

    <script>
       $("div").each(
           // 传一个参数
           // function (index) {
           //     console.log(index);  // 打印每个元素的索引
           // }

           // 传两个参数(最多只能传两个参数)
           function (index, obj) {
               console.log(index, obj);  // 打印每个元素的索引和元素内容
           }

       )
    </script>
</body>
```

还可以直接传入一个数组或 jquery 对象进行操作：

```javascript
<script>
    $.each([111,222,333], function (index, obj) {
    	console.log(index, obj)
	})
</script>
```

在遍历过程中可以使用  `return false` 提前结束 each 循环。

### **二、data 方法**

在给标签元素添加属性时，我们可以通过设置自定义属性来临时存储一些信息值，如：

```html
<body>
    <div id="d1" username="cdc" age="18"></div>
</body>
```

但是这样做显示是不保险的，容易将敏感信息暴露。此时我们就可以使用 data 方法来设置属性和值，并且通过该方法设置的属性和值在前端无法看到：

```javascript
$("#d1").data("info", "hehehe");
```

<img src="/static/img/JavaScript专题/jquery21.png" style="zoom:70%;" /> 

```javascript
// 获取data方法设置的属性值
$("#d1").data("info");  // "hehehe"

// 获取不存在的属性值
$("#d1").data("xxx");  // undefined

// 删除属性值
$("#d1").removeData("info");
```

