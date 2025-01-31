# CSS基础

## 一、CSS简介

### **1. 什么是CSS** 

Cascading Style Sheet（层叠样式表）简称 CSS，用于对 HTML 标签样式进行各种加工，当浏览器读到一个样式表，它就会按照这个样式表来对HTML文档进行渲染。

### **2. CSS的引入方式** 

- **方式一 行内式引入(内联式)**

直接在标签的内部，通过使用标签的 `style` 属性来进行 CSS 设置

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
</head>
<body>
    <p style="font-size: 20px">
        我是P标签
    </p>
</body>
</html>
```

- **方式二 嵌入式引入**

将CSS集中写在 `<head></head>` 层的 `<style></style>` 标签对中

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        p {
            font-size: 20px;
        }
    </style>
</head>
<body>
    <p>我是P标签</p>
</body>
</html>
```

- **方式三 外部式引入**

将 CSS 样式代码写在单独的一个 CSS 文件中，再通过 `<link></link>` 标签在 HTML 文档的 `<head></head>` 层引入（推荐使用该方式）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <link rel="stylesheet" href="mystyle.css">
</head>
<body>
    <p>我是P标签</p>
</body>
</html>
```

mystyle.css 文件中存放 css 内容

```css
p {
    font-size: 20px;
}
```

## 二、CSS选择器

### 1. 选择器简介

CSS 的语法格式为：

```css
选择器 {
	样式；
}
```

通过选择器，我们可以在 HTML 文档中准确的找到我们想要渲染的标签。CSS 中的选择器可以分为以下几种：

**基本选择器**：

- 标签选择器
- ID 选择器
- 类选择器
- 通用选择器

**组合选择器**：

- 后代选择器
- 子代选择器
- 弟弟选择器
- 毗邻选择器
- 分组选择器

**属性选择器**

**伪类选择器**

**伪元素选择器**

### **2. 标签选择器**

直接通过标签名找到对应的标签，渲染对象为所有的该类标签。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        /*所有的p标签都会被渲染，其他的标签不会*/
        p {
            background-color: red;
        }
    </style>

</head>
<body>
    <div>
        <p>
            我是p标签1
        </p>
    </div>

    <div>
        我是div标签
    </div>

    <p>我是p标签2</p>
</body>
</html>
```

<img src="../../static/img/CSS/标签选择器.png" style="zoom:80%;" /> 

### **3. ID 选择器**

通过元素的 id 属性值找到对应的标签。在 HTML 文档中，每个元素的 id 值唯一，所以只有一个元素可以生效。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        /*只有id属性值为p1的标签会被渲染*/
        #p1 {
            background-color: red;
        }
    </style>

</head>
<body>
    <div>
        <p id="p1">
            我是p标签1
        </p>
    </div>

    <div>
        我是div标签
    </div>

    <p id="p2">我是p标签2</p>
</body>
</html>
```

<img src="../../static/img/CSS/ID选择器.png" style="zoom:80%;" /> 

### **三、类选择器**

通过元素的 class 属性值找到对应的标签，只要该元素的 class 属性包含指定的值，就都会被渲染

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        /*所有class中含有aaa的标签都会被渲染*/
        .aaa {
            background-color: red;
        }
    </style>
</head>
<body>
    <div>
        <p>
            我是p标签1
        </p>
    </div>

    <div class="aaa bbb">
        我是div标签
    </div>

    <p class="aaa">我是p标签2</p>
</body>
</html>
```

<img src="../../static/img/CSS/类选择器.png" style="zoom:80%;" /> 

### **四、通用选择器**

对 HTML 文档中所有标签进行渲染。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        /*HTML所有元素都会被影响*/
        * {
            background-color: red;
        }
    </style>
</head>
<body>
    <div>
        <p>
            我是p标签1
        </p>
    </div>
    <div class="aaa bbb">
        我是div标签
    </div>
    <p class="aaa">我是p标签2</p>
</body>
</html>
```

<img src="../../static/img/CSS/通用选择器.png" style="zoom:80%;" /> 

### **五、后代选择器**

找到指定标签所包含的所有内部标签。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
       div p{
           background-color: red;
       }
    </style>

</head>
<body>
    <div>
        <p>
            我是p标签1
        </p>
        <div>
            <p>我是p标签3</p>
        </div>
    </div>
    <div class="aaa bbb">
        我是div标签
    </div>
    <p class="aaa">我是p标签2</p>
</body>
</html>
```

渲染 `<div></div>` 内部所有层级的 `<p></p>` 标签。

<img src="../../static/img/CSS/后代选择器.png" style="zoom:80%;" /> 

### **六、子代选择器**

找到所有父类是指定标签的元素进行渲染。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        div > a {
            background-color: red;
        }
    </style>

</head>
<body>
    <div>
        <a>我是a标签1</a><br>
        <span>
            <a>我是a标签2</a>
        </span>
    </div>
    
    <div class="aaa bbb">
        我是div标签
    </div>
    
    <p class="aaa">我是p标签</p>
</body>
</html>
```

只对 `<>div</div>` 的第一层的 `<a></a>` 标签（即子代标签）有效果，对更深层次的 `<a></a>` 标签（即孙子标签）没有效果。

<img src="../../static/img/CSS/子代选择器.png" style="zoom:80%;" /> 

### **七、弟弟选择器**

找到指定元素同级的所有指定标签进行渲染。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        div ~ p {
            background-color: red;
        }
    </style>

</head>
    <body>
    <p>我是p标签1</p>
    <div>我是div标签</div>
    <p>我是p标签2</p>
    <p>我是p标签3</p>
</body>
</html>
```

对 `<div></div>` 标签后的所有 `<p></p>` 标签进行渲染。

<img src="../../static/img/CSS/弟弟选择器.png" style="zoom: 80%;" /> 

### **八、毗邻选择器**

选择紧接着指定元素之后的标签进行渲染。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        div + p {
            background-color: red;
        }
    </style>

</head>
    <body>
    <p>我是p标签1</p>
    <div>我是div标签</div>
    <p>我是p标签2</p>
    <p>我是p标签3</p>
</body>
</html>
```

毗邻选择器和弟弟选择器的区别是，弟弟选择器能渲染多个，毗邻选择器只能渲染一个。

<img src="../../static/img/CSS/毗邻选择器.png" style="zoom:80%;" /> 

### **九、分组选择器**

当有多个元素要设置相同的样式时，可以用逗号将这些元素隔开，一起进行样式设置

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        div, h3 {
            background-color: red;
        }
    </style>

</head>
    <body>
    <p>我是p标签1</p>
    <div>我是div标签</div>
    <p>我是p标签2</p>
    <p>我是p标签3</p>
    <h3>标题三</h3>
</body>
</html>
```

<img src="../../static/img/CSS/分组选择器.png" style="zoom:80%;" /> 

**补充：**

上述高级选择器的规则可以适用于所有的基本选择器，不光光只是标签选择器，如：

```css
<!--找到所有class属性值为active的后代p标签进行渲染>
.active p{
	background-color: red;
}

<!--找到所有id属性值为top的子代p标签进行渲染>
#top p{
	background-color: red;
}
```

标签选择器、类选择器、ID 选择器等都可以通过组合来进行对元素的定位。

### **十、属性选择器**

用于找到含有`特定属性`或者`特性属性和值`的元素

```css
/*用于选取带有title属性的p元素。*/
p[title] {
  color: red;
}

/*用于选取带有title属性且属性值为123的p元素。*/
p[title="123"] {
  color: green;
}

/*找到所有title属性以hello开头的元素*/
[title^="hello"] {
  color: red;
}

/*找到所有title属性以hello结尾的元素*/
[title$="hello"] {
  color: yellow;
}

/*找到所有title属性中包含（字符串包含）hello的元素*/
[title*="hello"] {
  color: red;
}

/*找到所有title属性(有多个值或值以空格分割)中有一个值为hello的元素：*/
[title~="hello"] {
  color: green;
}
```

### **十一、伪类选择器**

```css
/* 为未访问过的链接设置样式 */
a:link {
  color: #FF0000
}

/* 为已访问的链接设置样式 */
a:visited {
  color: #00FF00
} 

/* 当鼠标移动到链接上时，使用该样式 */
a:hover {
  color: #FF00FF
} 

/* 为选定的链接（鼠标点击时）设置样式 */ 
a:active {
  color: #0000FF
}

/*input输入框获取焦点时(光标定位到输入框内)样式*/
input:focus {
  outline: none;
  background-color: #eee;
}
```

示例：设置一个`<a></a>` 标签链接，没有访问过时，链接显示的内容是蓝色的，当鼠标移到链接上时链接内容变为红色，当鼠标点击链接时链接内容变为黑色，当该链接已经被访问过时链接内容变为紫色。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
       a:link{
           color: blue;
       }

        a:hover{
            color: red;
        }

        a:active{
            color: black;
        }

        a:visited{
            color: purple;
        }
    </style>

</head>
<body>
    <a href="http://www.jingdong.com">618</a>
</body>
</html>
```

<img src="../../static/img/CSS/伪类选择器.gif" style="zoom:80%;" /> 

### **十二、伪元素选择器**

#### **12.1 first-letter：给首字母设置特殊样式**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        p::first-letter{
            color: red;
            font-size: 30px;
        }
    </style>

</head>
<body>
    <p>我是p标签</p>
</body>
</html>
```

<img src="../../static/img/CSS/伪元素选择器1.png" style="zoom:80%;" /> 

#### **12.2 first-line：给第一行设置特殊样式**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        ul::first-line{
            color: red;
            font-size: 30px;
        }
    </style>

</head>
<body>
    <ul>
        <li>aaa</li>
        <li>bbb</li>
        <li>ccc</li>
    </ul>
</body>
</html>
```

<img src="../../static/img/CSS/伪元素选择器2.png" style="zoom:80%;" /> 

#### **12.3 before：在指定元素之前插入内容**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        p::before{
            content: "aaaaa";
            color: red;
        }
    </style>

</head>
<body>
    <p>我是p标签</p>
</body>
</html>
```

<img src="../../static/img/CSS/伪元素选择器3.png" style="zoom:80%;" /> 

#### **12.4 after：在指定元素之后插入内容**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>CSS样式</title>
    <style>
        p::after{
            content: "aaaaa";
            color: red;
        }
    </style>

</head>
<body>
    <p>我是p标签</p>
</body>
</html>
```

<img src=".../../static/img/CSS/伪元素选择器4.png" style="zoom:80%;" />  

## CSS 选择器补充

### **一、CSS 继承性**

在 CSS 中，父级标签的某些属性是可以被后代标签所继承，例如字体大小样式颜色等。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            font-size: 30px;
            color: red;
        }
    </style>
</head>
<body>
    <div>
        <ul>
            <li>
                <span>sssss</span>
            </li>
        </ul>
    </div>
</body>
</html>
```

<img src="../../static/img/CSS/css继承.png" style="zoom:80%;" /> 

对父级 `<div></div>` 标签设置的属性可以被后代的 `<span></span>` 标签所继承。当然，并不是所有的属性都可以被继承，例如边框属性等就无法被继承。

### **二、CSS 选择器权重**

​		对于一个标签，如果通过多种选择器都可以定位到，且每种选择器对于该标签的渲染效果也不一样，那么这个标签最终的样式应该由哪个选择器决定呢？

在 CSS 中，选择器之间是有优先级，优先级权重如下：

- 内联样式权重：1000
- Id选择器权重：100
- 类选择器权重：10
- 元素选择器权重：1

多种选择器嵌套使用的权重为对应选择器权重之和，且`权重计算永不进位`。我们可以去数选择器的数量来计算出组合选择器的权重，一般都是按照 ID选择器、类选择器、标签选择器的顺序去计数，例如：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*选择器1*/
        p {
            color: red;
        }
        
        /*选择器2*/
        div p{
            color: blue;
        }
        
        /*选择器3*/
        .active{
            color: purple;
        }
        
        /*选择器4*/
        #wrap3 p{
            color: black;
        }
        
        /*选择器5*/
        .box2 #wrap3 p{
            color: aqua;
        }
        
        /*选择器6*/
        #wrap1 .box2 .box3 p{
            color: chartreuse;
        }
        
    </style>
</head>
<body>
<div class="box1" id="wrap1">
    <div class="box2" id="wrap2">
        <div class="box3" id="wrap3">
            <p class="active">
                我是什么颜色的？
            </p>
        </div>
    </div>
</div>
</body>
</html>
```

从上至下的六中选择器，他们的权重分别是：

- 选择器1：ID选择器个数为0，类选择器个数为0，标签选择器个数为1，所以权重为 001
- 选择器2：ID选择器个数为0，类选择器个数为0，标签选择器个数为2，所以权重为 002
- 选择器3：ID选择器个数为0，类选择器个数为1，标签选择器个数为0，所以权重为 010
- 选择器4：ID选择器个数为1，类选择器个数为0，标签选择器个数为1，所以权重为 101
- 选择器5：ID选择器个数为1，类选择器个数为1，标签选择器个数为1，所以权重为 111
- 选择器6：ID选择器个数为1，类选择器个数为2，标签选择器个数为1，所以权重为 121

所以最终 `<p></p>` 标签会按照 选择器6 的样式进行渲染。那既然按照这种计算规则，那如果现在有一个选择器7：

```css
 #wrap1 #wrap2 #wrap3{
            color: darkgreen;
        }
```

​		它的权重计算结果为 300，那 `<p></p>` 标签是否最终会按照这个选择器的样式去渲染呢？实际测试发现并不是按照我们想象的那样，`<p></p>` 最终还是会按照选择器6的样式去渲染，这是因为选择器7和上述的六种选择器示例相比，并没有直接操作到 `<p></p>` 标签，它最后可以操作到的其实是 ID 值为 wrap3 的 `<div></div>` 标签， `<p></p>` 标签会继承该 `<div></div>` 标签的样式，但是继承的权重是最低的，几乎接近于 0，所以对于 `<p></p>` 标签而言，选择器7 真正的权重结果其实是0。

​		对于权重计算永不进位，可以形象的理解为，权重计算的数值的每一个位上的最大值永远是9，例如：

```css
 div div ... div p{
            color: darkgreen;
        }
```

假设上述选择器中省略了6个 div，那么该选择器的权重应该是0 0 10，但是由于永不进位的原因，所以最大值只能是9，不能是10，超过9的值直接变成9，因此上述选择器的实际权重是 009。

### **三、强制样式生效**

在设置样式时，使用 !important 可以忽略选择器优先级，让样式强制生效（不推荐使用，容易引起HTML文档的混乱）

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*选择器1*/
        p {
            color: red !important;
        }
    </style>
</head>
<body>
    <p style="color: purple">
        我是什么颜色的？
    </p>
</body>
</html>
```

由于 `<p></p>` 标签使用了内联样式，权重为 1000；标签选择器的权重为 001，所以理论上应该按照内联样式进行渲染，但是我们可以使用 `!important`，让标签强制按照指定的样式进行渲染，这里是强制标签按照标签选择器的样式进行渲染。