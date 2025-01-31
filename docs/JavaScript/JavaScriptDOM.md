# JavaScript DOM

## DOM 简介

### **一、简介**

​		DOM（Document Object Model）是一套对文档的内容进行抽象和概念化的方法。 当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。HTML DOM 模型被构造为对象的树。

### **二、DOM树**

DOM 标准规定HTML文档中的每个成分都是一个节点（node）：

- 文档节点(document对象)：代表整个文档
- 元素节点(element 对象)：代表一个元素（标签）
- 文本节点(text对象)：代表元素（标签）中的文本
- 属性节点(attribute对象)：代表一个属性，元素（标签）才有属性
- 注释是注释节点(comment对象)　

JavaScript 可以通过 DOM 创建动态的 HTML：

- JavaScript 能够改变页面中的所有 HTML 元素
- JavaScript 能够改变页面中的所有 HTML 属性
- JavaScript 能够改变页面中的所有 CSS 样式
- JavaScript 能够对页面中的所有事件做出反应

<img src="/static/img/JavaScript专题/DOM树.png" style="zoom:60%;" /> 

**注意：**使用 DOM 时，必须以关键字 `document` 开头，`document` 指代的是整个浏览器窗口。

## 查找标签

​		想要通过 DOM 操作标签，第一步就是要先找到要操作的标签。可以通过 `直接查找` 和 `间接查找` 两种方式查找到对应的标签。

### **一、直接查找**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div id="d1">div
        <div>div>div</div>
        <p class="c1">div>p
            <span>div>p>span</span>
        </p>
        <p>div>p</p>
    </div>
    <div>div+div</div>
</body>
</html>
```

直接查找主要有以下几种方式：

#### 1.1 通过 id 查找

```javascript
// 返回值是一个标签对象，该对象的id值为查询的值
document.getElementById("d1");
```

<img src="/static/img/JavaScript专题/查找标签1.png" style="zoom:70%;" />  

#### 1.2 通过类查找

```javascript
// 返回值是一个数组，数组元素是一个个标签对象，且都含有查询的类名。可通过索引取值
document.getElementsByClassName("c1");
```

<img src="/static/img/JavaScript专题/查找标签2.png" style="zoom:70%;" /> 

#### 1.3 通过标签名查找

```javascript
// 返回值是一个数组，数组元素是一个个标签对象，且为要查找的标签。可通过索引取值
document.getElementsByTagName("div");
```

<img src="/static/img/JavaScript专题/查找标签3.png" style="zoom:70%;" /> 

如果想要在后续使用查找到的标签，可以提前用一个变量来接收标签：

```javascript
let divEle = document.getElementById("d1");
console.log(divEle);
```

<img src="/static/img/JavaScript专题/查找标签4.png" style="zoom:70%;" /> 

### **二、间接查找**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div>div上面的div</div>
    <div>div上面的div</div>
    <div id="d1">div
        <div>div>div</div>
        <p class="c1">div>p
            <span>div>p>span</span>
        </p>
        <p>div>p</p>
    </div>
    <div>div下面的div</div>
    <div>div下面的div</div>
</body>
</html>
```

#### 2.1 父节点标签元素

```javascript
// 查找到 p 标签
let pEle = document.getElementsByClassName("c1")[0];
pEle.parentElement  // p 标签的父级标签，返回值是一个标签对象，即 div
pEle.parentElement.parentElement  // body
pEle.parentElement.parentElement.parentElement  // html
pEle.parentElement.parentElement.parentElement.parentElement  // null
```

<img src="/static/img/JavaScript专题/查找标签5.png" style="zoom:60%;" />  

#### 2.2 所有子标签

```javascript
// 找到 id 值为 d1 的 div 标签
let divEle = document.getElementById("d1");
// 查找 div 标签的子标签，返回值是一个数组
divEle.children
```

<img src="/static/img/JavaScript专题/查找标签6.png" style="zoom:60%;" /> 

#### 2.3 第一个和最后一个子标签

```javascript
let divEle = document.getElementById("d1");  // 找到 id 值为 d1 的 div 标签
divEle.firstElementChild  // div 标签的第一个子标签
divEle.lastElementChild  // div 标签的最后一个子标签
```

#### 2.4 兄弟标签

```javascript
let divEle = document.getElementById("d1");  // 找到 id 值为 d1 的 div 标签
divEle.nextElementSibling  // 同级别下面第一个兄弟标签
divEle.previousElementSibling  // 同级别上面第一个兄弟标签
```

## 节点操作

### **一、创建节点**

```javascript
//  创建一个 a 标签
let aEle = document.createElement("a");
console.log(aEle);
```

<img src="/static/img/JavaScript专题/节点操作1.png" style="zoom:70%;" /> 

### **二、添加节点**

先创建一个文档树，原始结构如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div id="d1">div
        <div>div>div</div>
        <p class="c1">div>p</p>
        <p>div>p</p>
    </div>
</body>
</html>
```

#### 2.1 在节点内部增加子节点

```javascript
let divEle = document.getElementById("d1");
let aEle = document.createElement("a");
divEle.appendChild(aEle);
```

<img src="/static/img/JavaScript专题/节点操作2.png" style="zoom:60%;" /> 

#### 2.2 在某个子节点前增加新节点

```javascript
// 查找到最外侧的 div 标签
let divEle = document.getElementById("d1");
// 查找到外层 div 标签的一个子标签 p 标签
let pEle = document.getElementsByClassName("c1")[0];
// 创建一个 a 标签
let aEle = document.createElement("a");
// 将 a 标签插到 p 标签前面
divEle.insertBefore(aEle, pEle)
```

<img src="/static/img/JavaScript专题/节点操作3.png" style="zoom:60%;" /> 

#### 2.3 删除子节点

获得要删除的元素，通过父元素调用该方法删除。

```javascript
// 获取第二个 p 标签对象
let pEle = document.getElementsByTagName("p")[1];
// 获取最外层 div 对象
let divEle = document.getElementById("d1");
// 删除子节点 p 标签
divEle.removeChild(pEle)
```

<img src="/static/img/JavaScript专题/节点操作4.png" style="zoom:60%;" /> 

#### 2.4 替换子节点

获得要替换的元素，通过父元素调用该方法替换。

```javascript
// 获取第二个 p 标签对象
let pEle = document.getElementsByTagName("p")[1];
// 获取最外层 div 对象
let divEle = document.getElementById("d1");
// 创建一个 a 标签
let aEle = document.createElement("a");
// 替换 p 标签对象
divEle.replaceChild(aEle, pEle);
```

<img src="/static/img/JavaScript专题/节点操作5.png" style="zoom:60%;" /> 

## 节点属性操作

### **一、获取节点的文本值**

- `innerText` 方法

```javascript
// 获取最外层 div 对象
let divEle = document.getElementById("d1");
console.log(divEle.innerText)
```

<img src="/static/img/JavaScript专题/节点操作6.png" style="zoom:70%;" />  

- `innerHTML` 方法

```javascript
// 获取最外层 div 对象
let divEle = document.getElementById("d1");
console.log(divEle.innerHTML)
```

<img src="/static/img/JavaScript专题/节点操作7.png" style="zoom:70%;" /> 

**区别：** `innerText` 只返回指定标签对象内部的文本内容（包括子孙标签）；`innerHTML` 除了会返回文本内容，还会返回内部的标签内容

### **二、设置节点的文本值**

- `innerText` 方法

```javascript
// 获取最外层 div 对象
let divEle = document.getElementById("d1");
divEle.innerText = "hahaha"
```

<img src="/static/img/JavaScript专题/节点操作8.png" style="zoom:70%;" /> 

- `innerHTML` 方法

```javascript
// 获取最外层 div 对象
let divEle = document.getElementById("d1");
divEle.innerHTML = "hahaha"
```

<img src="/static/img/JavaScript专题/节点操作8.png" style="zoom:70%;" /> 

最外层的 div 标签内部所有内容都被替换成了 "hahaha"

**区别：**针对普通文本设值，两种方法没有区别。但是 `innerHTML` 方法可以识别文本中的 HTML 标签并进行对应的转换，`innerText` 无法识别 HTML 标签，会将标签当中普通文本展示，如：

```javascript
let divEle = document.getElementById("d1");
divEle.innerHTML = '<h1 style="color:red">hahaha<h1>'
```

<img src="/static/img/JavaScript专题/节点操作10.png" style="zoom:70%;" /> 

```javascript
let divEle = document.getElementById("d1");
divEle.innerText = '<h1 style="color:red">hahaha<h1>'
```

<img src="/static/img/JavaScript专题/节点操作9.png" style="zoom:70%;" /> 

### **三、获取节点属性**

现有一个 HTML 文档树，结构如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <a href="https://www.baidu.com" id="d1" name="baidu">百度一下......</a>
</body>
</html>
```

```javascript
// 获取 a 标签对象
let aEle = document.getElementById("d1");
// 获取 a 标签的 href 属性值
console.log(aEle.getAttribute("href"))
```

<img src="/static/img/JavaScript专题/节点操作11.png" style="zoom:70%;" /> 

### **四、设置节点属性**

```javascript
// 获取 a 标签对象
let aEle = document.getElementById("d1");
// 设置一个age属性
aEle.setAttribute("age", 18)
```

<img src="/static/img/JavaScript专题/节点操作12.png" style="zoom:70%;" /> 

**注意：**标签 `自带的属性` 可以直接通过 `标签对象.属性名` 方式来获取和设置属性值，如：

```javascript
// 获取 a 标签对象
let aEle = document.getElementById("d1");
// 获取 href 属性
console.log(aEle.href)
// 修改 href 属性
aEle.href = "https://www.jingdong.com"
```

<img src="/static/img/JavaScript专题/节点操作13.png" style="zoom:70%;" /> 

### **五、删除节点属性**

```javascript
// 获取 a 标签对象
let aEle = document.getElementById("d1");
// 删除 name 属性
aEle.removeAttribute("name")
```

<img src="/static/img/JavaScript专题/节点操作14.png" style="zoom:70%;" /> 

## 获取值操作

用户输入的内容可以通过 `value` 方法获取，该方法主要适用于以下标签：

- input
- select
- textarea 

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<input type="text" id="d1">
</body>
</html>
```

```javascript
let inputEle = document.getElementById("d1")
console.log(inputEle.value)  // "sdada"

// 也可以通过 value 方法给输入框赋值
inputEle.value = "hahaha"  // 此时输入框内显示内容为 hahaha
```

**补充：** 对于用户上传的文件类型，可以通过 `files` 方法获取文件内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<input type="file" id="d1">
</body>
</html>
```

<img src="/static/img/JavaScript专题/节点操作15.png" style="zoom:100%;" /> 

## class 操作

现有一个 HTML 文档树，结构如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .c1 {
            height: 200px;
            width: 200px;
            border-radius: 50%;
        }

        .bg_red {
            background-color: red;
        }

        .bg_green {
            background-color: green;
        }
    </style>
</head>
<body>
    <div id="d1" class="c1 bg_green bg_red"></div>
</body>
</html>
```

### **一、获取标签对象的类**

```javascript
let divEle = document.getElementById("d1");
console.log(divEle.classList)
```

<img src="/static/img/JavaScript专题/节点操作16.png" style="zoom:80%;" /> 

### **二、删除类属性**

```javascript
let divEle = document.getElementById("d1");
divEle.classList.remove("bg_green")
```

### **三、添加类属性**

```javascript
let divEle = document.getElementById("d1");
divEle.classList.add("bg_green")
```

### **四、判断类属性是否存在**

```javascript
let divEle = document.getElementById("d1");

divEle.classList.contains("c1");  // true
divEle.classList.contains("c1");  // false
```

### **五、切换类属性**

如果有该类属性，就移除；否则就添加该类属性。

```javascript
let divEle = document.getElementById("d1");
divEle.classList.toggle("bg_green");
```

## 设置 CSS 样式

**语法格式：**`obj.style.属性="值"`

**JS操作CSS属性的规律：**

- 对于没有中横线的 CSS 属性一般直接使用 style.属性名 即可，如：

```javascript
obj.style.margin
obj.style.width
obj.style.left
obj.style.position
```

- 对含有中横线的 CSS 属性，将中横线后面的第一个字母换成大写，再去掉中横线即可，如：

```javascript
obj.style.marginTop
obj.style.borderLeftWidth
obj.style.zIndex
obj.style.fontFamily
```

**操作示例：**

```html
<p id="p1">hello, my name is cdc</p>
```

```javascript
let pEle = document.getElementById("p1");
pEle.style.color = "red"
pEle.style.fontSize = "28px"
pEle.style.background = "yellow"
pEle.style.border = "5px solid black"
```

## 事件

当达到某个事先设定的条件触发的动作称为事件。

### **一、常用事件**

```javascript
onclick        当用户点击某个对象时调用的事件句柄。
ondblclick     当用户双击某个对象时调用的事件句柄。

onfocus        元素获得焦点。应用场景：输入框
onblur         元素失去焦点。应用场景：用于表单验证,用户离开某个输入框时,代表已经输入完了,我们可                以对它进行验证.
onchange       域的内容被改变。应用场景：通常用于表单元素,当元素内容被改变时触发.（select联动）

onkeydown      某个键盘按键被按下。应用场景: 当用户在最后一个输入框按下回车按键时,表单提交.
onkeypress     某个键盘按键被按下并松开。
onkeyup        某个键盘按键被松开。
onload         一张页面或一幅图像完成加载。
onmousedown    鼠标按钮被按下。
onmousemove    鼠标被移动。
onmouseout     鼠标从某元素移开。
onmouseover    鼠标移到某元素之上。

onselect      在文本框中的文本被选中时发生。
onsubmit      确认按钮被点击，使用的对象是form。
```

### **二、事件绑定方式**

#### 2.1 方式一

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!--this是实参，表示触发事件的当前元素-->
    <div id="d1" onclick="changeColor(this)">hello</div> 
    
    <script>
        function changeColor(ths) {  // 函数定义过程中的ths为形参
            ths.style.color = "red"
        }
    </script>
</body>
</html>
```

#### 2.2 方式二（推荐）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div id="d1">hello</div>

    <script>
       let divEle = document.getElementById("d1");
       divEle.onclick = function () {
           this.style.color = "red"
       }
    </script>
</body>
</html>
```

**注意：**一般给标签对象绑定事件的 js 代码，都书写在 body 标签的最下面。如果将对应的代码写在 head 标签中可能会出错：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
       let divEle = document.getElementById("d1");
       divEle.onclick = function () {
           this.style.color = "red"
       }
    </script>
</head>
<body>
    <div id="d1">hello</div>
</body>
</html>
```

<img src="/static/img/JavaScript专题/事件1.png" style="zoom:60%;" />  

用于 HTML 文档是从上向下解析的，解析到事件绑定代码时，对应的标签还未被加载出来，所以报错。我们可以通过预加载方式解决这个问题：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        // 等待浏览器窗口加载完毕之后，再执行里面的代码
        window.onload = function () {
            let divEle = document.getElementById("d1");
            divEle.onclick = function () {
                this.style.color = "red"
            }
        }
    </script>
</head>
<body>
    <div id="d1">hello</div>
</body>
</html>
```

一般不常用此方法，最简单有效的方法就是直接将 js 代码书写在 body 标签的最下面即可。