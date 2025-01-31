# CSS 属性

## **标签的高度和宽度**

​		在 HTML 的学习中，我们知道标签实际上可以分为 `块级标签`、`行内标签` 以及 `行内块标签` 三种，对于不同类型的标签，我们对其设置宽高属性也会得到不一样的效果：

- 块级标签独占一行，可以对其设置宽高、内外边距等属性，如果不设置值，高度默认是内容的高度，宽度默认是浏览器的宽度（换句话就是如果块标签既没有内容，也不设置高度，那么在页面上就看不到）

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>标签的宽度和高度</title>
    <style>
        /*方便贴图演示，将浏览器背景改为黑色*/
        body {
            background: #000;
        }

        .d1 {
            background-color: red;
            color: white;
        }

        .d2 {
            background-color: red;
            height: 100px;
            width: 300px;
            color: white;
        }
    </style>
</head>
<body>
    <!--块标签-->
    <div class="d1">
        我是div1 使用默认的宽高
    </div>
    <hr>
    <div class="d2">
        我是div2 宽度300px 高度100px
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/标签的宽高1.png" style="zoom:80%;" /> 

- 行内标签，可以一行显示多个，无法设置其宽高以及内外边距的值，宽度和高度都有内容决定（换句话就是如果行内标签没有内容，那么在页面上就看不到）

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>标签的宽度和高度</title>
    <style>
        /*方便贴图演示，将浏览器背景改为黑色*/
        body {
            background-color: #000;
        }
        .s1{
            background-color: red;
            height: 1000px;
            width: 500px;
            color: white;
        }
    </style>
</head>
<body>
    <span class="s1">
        我是span标签 我设置了高度和宽度，但是不生效
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/标签的宽高2.png" style="zoom:80%;" /> 

- 行内块标签既可以设置宽度高度以及内外边距，也可以在一行显示多个

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>标签的宽度和高度</title>
    <style>
        /*方便贴图演示，将浏览器背景改为黑色*/
        body {
            background-color: #000;
        }
        .s2{
            /*转换成行内块标签*/
            display: inline-block;
            height: 100px;
            width: 300px;
            color: white;
            background-color: red;
        }
    </style>
</head>
<body>
    <span class="s2">
        我是span标签 我转成了行内块标签
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/标签的宽高3.png" style="zoom:80%;" /> 

## **文字属性**

### 一、设置文字的字体

​		我们可以通过 `font-family` 属性来设置文字的字体，属性的值可以有多个，浏览器在渲染时会按照设置的值的顺序从前往后去渲染，即如果浏览器无法使用第一个值的字体来渲染，就选择后一个，以此类推，如果所有设置的字体样式浏览器都不包含，那么浏览器就会使用自带的默认字体来渲染。

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .d1{
            font-family: "Microsoft Yahei", "微软雅黑", "Arial", sans-serif
        }
    </style>
</head>
<body>
    <!--字体大小-->
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

### 二、设置文字样式

通过 `font-style` 属性来设置文字的样式，取值和对应的效果如下：

- normal：正常显示文字
- italic：斜体显示文字

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1 {
            font-style: normal;
            color: white;
        }
        .d2 {
            font-style: italic;
            color: white;
        }
    </style>
</head>
<body>
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d2">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文字属性1.png" style="zoom:80%;" /> 

### 三、设置文字大小

通过 `font-size` 属性来设置文字的大小，常用单位是 `px` 和 `em`，其中 `px` 就是像素的大小，一旦设置不可改变；`em` 是默认字体大小的倍数，会随着默认字体大小的改变而改变；还可以设置值为 `inherit`，表示继承父标签文字的大小。

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1
            color: white;
            font-size: 20px;
        }
        .d2 {
            color: red;
            /*大小是默认字体大小的一倍，即就是默认字体的大小*/
            font-size: 1em;
        }
    </style>
</head>
<body>
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d2">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文字属性2.png" style="zoom:80%;" /> 

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
            /*修改全局默认字体大小为30px*/
            font-size: 30px;
        }
        .d1
            color: white;
            font-size: 20px;
        }
        .d2 {
            color: red;
            /*大小是默认字体大小的一倍，即30px*/
            font-size: 1em;
        }
    </style>
</head>
<body>
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d2">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文字属性3.png" style="zoom:80%;" /> 

### 四、设置文字的颜色

通过 `color` 属性来设置文字的大小，有四种设置值的方式：

- 十六进制：如 `#4d4d4d`
- RGB值：如 `rgb(255,255,255)`
- RGBA值：如 `rgba(255,255,255,0.2)`   最后一个参数用于控制透明度，取值范围为0.0~1.0
- 颜色的名字：如 `red`

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1 {
            color: white;
        }
        .d2 {
            color: #eeeeee;
        }
        .d3 {
            color: rgb(255, 255, 255);
        }
        .d4 {
            color: rgba(255, 255, 255, 0.5);
        }
    </style>
</head>
<body>
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d2">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d3">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d4">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文字属性4.png" style="zoom:80%;" /> 

### 五、设置文字的粗细

通过 `color` 属性来设置文字的粗细，取值和效果如下：

|   值    |                      描述                      |
| :-----: | :--------------------------------------------: |
| normal  |                默认值，标准粗细                |
|  bold   |                      粗体                      |
| bolder  |                      更粗                      |
| lighter |                      更细                      |
| 100~900 | 设置具体粗细，400等同于normal，而700等同于bold |
| inherit |             继承父元素字体的粗细值             |

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1 {
            color: white;
            font-weight: normal;
        }
        .d2 {
             color: white;
            font-weight: bold;
        }
        .d3 {
             color: white;
            font-weight: lighter;
        }
        .d4 {
             color: white;
            font-weight: 800;
        }
    </style>
</head>
<body>
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d2">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d3">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d4">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文字属性5.png" style="zoom:80%;" /> 

## **文本属性**

### 一、文本装饰

通过 `text-decoration` 属性可以实现在文本不同的地方加上一条线的效果：

|      值      |                描述                 |
| :----------: | :---------------------------------: |
|     none     |        默认，定义标准的文本         |
|  underline   |         定义文本下的一条线          |
|   overline   |         定义文本上的一条线          |
| line-through |       定义穿过文本下的一条线        |
|   inherit    | 继承父元素的text-decoration属性的值 |

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1 {
            color: white;
            text-decoration: overline;
        }
        .d2 {
            color: white;
            text-decoration: line-through;
        }
        .d3 {
            color: white;
            text-decoration: underline;
        }
        .d4 {
            color: white;
            text-decoration: none;
        }
    </style>
</head>
<body>
    <div class="d1">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d2">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d3">
        一个不愿透露姓名的帅小伙
    </div>
    <div class="d4">
        一个不愿透露姓名的帅小伙
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性1.png" style="zoom:80%;" /> 

**补充：**该属性常常用于清楚 `<a></a>`标签自带的样式

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .a1{
            color: white;
        }
        .a2{
            color: white;
            /*可以去除a标签自带的下划线样式*/
            text-decoration: none;
        }
    </style>
</head>
<body>
    <a href="" class="a1">点我</a>
    <a href="" class="a2">点我</a>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性2.png" style="zoom:80%;" /> 

### 二、设置文本缩进

​        通过 `text-indent` 属性可以设置文本段落首行缩进的效果，浏览器种文字默认大小是 `16px`，所以理论上我们只要给属性赋值 `32px` 就可以达到首行缩进2个文字的效果，例如：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        p{
            color: white;
            text-indent: 32px;
        }
    </style>
</head>
<body>
    <p>
    	依据所学知识使民主共和的观念深入人心，实现了近代中国第一次历史巨变的事件为辛亥革命，辛亥革		 命的意义为推翻了清王朝，结束了统治中国两千多年的封建君主专制制度；新文化运动后期宣传的主要		  内容为宣传马克思主义，为中共的成立奠定了思想基础
    </p>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性3.png" style="zoom:80%;" /> 

​       但是并不是所有页面的默认字体大小都是固定的，如果某个页面所有字体默认大小是 `20px` ，那么此时应该设置属性值为 `40px` 才能实现首行缩进的效果，显然设置固定值是不合理的，因此我们通常使用单位 `em` ，这样就能做到根据不同的页面动态的去适配。

```css
p{
    color: white;
    text-indent: 2em;
}
```

### 三、设置文本间距

#### **3.1 设置行高**

通过设置`line-height` 属性可以设置文本行高，从而设置文本之间的距离

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
		/*最原始的样式*/
        .d1{
            color: white;
            background-color: red;
        }
        .d2{
            color: white;
            background-color: green;
        }
    </style>
</head>
<body>
    <div class="d1">
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
    <div class="d2">
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性4.png" style="zoom:80%;" /> 

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
		/*将第一个div的行高设置成50px*/
        .d1{
            color: white;
            background-color: red;
            line-height: 50px;
        }
        .d2{
            color: white;
            background-color: green;
        }
    </style>
</head>
<body>
    <div class="d1">
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
    <div class="d2">
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性5.png" style="zoom:80%;" /> 

#### **3.2 设置文字之间的宽度** 

​       通过 `letter-spacing` 属性可以设置中英文文字之间的距离，通过 `word-spacing` 属性可以设置英文单词之间的距离：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1{
            color: white;
            background-color: red;
            /*设置文字之间的距离，对中英文都有效果*/
            letter-spacing: 5px;
            /*设置英文单词之间的距离*/
            word-spacing: 10px;
        }
    </style>
</head>
<body>
    <div class="d1">
        在人们腐烂的欲望下，巢穴一层层叠加 hello world
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性6.png" style="zoom:80%;" /> 

**补充：**通过上述示例我们可以观察到，当标签不设置高度时，每次设置行高，标签内容都会水平居中显示，那么如果当标签本身就有一定的高度，再设置行高会有什么效果呢：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1{
            color: white;
            background-color: red;
            /*设置标签高度为100px*/
            height: 100px;
            /*设置行高为50px*/
            line-height: 50px;
        }
    </style>
</head>
<body>
    <div class="d1">
        在人们腐烂的欲望下，巢穴一层层叠加 hello world
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性7.png" style="zoom:80%;" /> 

​       可以看到，蓝色框出来的部分就是行高渲染的效果，而标签整体高度远远大于行高，所以整个内容会浮在上部，那么在标签高度大于行高的情况下，怎么样才能让内容继续居中显示呢？我们只需要将行高设为标签的高度即可：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1{
            color: white;
            background-color: red;
            /*设置标签高度为100px*/
            height: 100px;
            /*设置行高也为100px*/
            line-height: 100px;
        }
    </style>
</head>
<body>
    <div class="d1">
        在人们腐烂的欲望下，巢穴一层层叠加 hello world
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性8.png" style="zoom:80%;" />  

### 四、设置文本对齐方式

通过 `text-align` 属性可以设置文本对齐的方式，属性的取值和效果分别如下：

|   值    |      描述       |
| :-----: | :-------------: |
|  left   | 左边对齐 默认值 |
|  right  |     右对齐      |
| center  |    居中对齐     |
| justify |    两端对齐     |

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        .d1{
            color: white;
            text-align: left;
            background-color: red;
        }
        .d2{
            color: white;
            text-align: center;
            background-color: green;
        }
        .d3{
            color: white;
            text-align: right;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div class="d1">
        在人们腐烂的欲望下，巢穴一层层叠加 hello world
    </div>
    <div class="d2">
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
    <div class="d3">
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性9.png" style="zoom:80%;" /> 

**补充：**我们通常会结合使用 `text-align` 和 `line-height` 属性，来实现内容垂直水平居中的效果，即让内容在整个标签的最中间：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        body {
            background-color: black;
        }
        div{
            height: 50px;
            text-align: center;
            line-height: 50px;
            color: white;
            background-color: red;
        }
    </style>
</head>
<body>
    <div>
        在人们腐烂的欲望下，巢穴一层层叠加
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/文本属性10.png" style="zoom:80%;" /> 

## 背景属性

### 一、背景颜色

通过 `background-color` 可以设置标签的背景，和设置文字颜色一样，有四种写法：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 100px;
            width: 100px;

            /*方式一：直接写颜色*/
            background-color: red;

            /*方式二：十六进制*/
            /*background-color: #ff0000;*/

            /*方式三：rgb方式*/
            /*background-color: rgb(255, 0, 0);*/

            /*方式四：rgba方式 最后一位表示透明度，取值范围0-1*/
            /*background-color: rgba(255, 0, 0, 0.5);*/
        }
    </style>
</head>
<body>
    <div>
        我是div
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/背景属性1.png" style="zoom:80%;" /> 

### 二、背景图片

#### **2.1 设置背景图片**

​       通过 `background-color` 可以设置标签的背景为图片，`url` 种可以写本地图片的路径，也可以写图片的网址，例如以下示例，给大家展示下我的自拍：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 400px;
            width: 283px;
            background-image: url("avatar.jpg");
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

<img src="/static/img/CSS专题/背景属性2.png" style="zoom:80%;" /> 

#### **2.2 设置背景图片重复**

需要注意的是，如果标签的大小大于图片的尺寸大小，那么图片会默认平铺满整个标签：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 1000px;
            width: 1000px;
            background-image: url("avatar.jpg");
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

<img src="/static/img/CSS专题/背景属性3.png" style="zoom:80%;" /> 

我们可以通过 `background-repeat` 来控制是否需要重复平铺

|    值     |              描述              |
| :-------: | :----------------------------: |
|  repeat   | 默认，背景图片平铺排满整个页面 |
| repeat-x  |    背景图片只在水平方向平铺    |
| repeat-y  |    背景图片只在垂直方向平铺    |
| no-repeat |         背景图片不平铺         |

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 1000px;
            width: 1000px;
            background-color: red;
            background-image: url("avatar.jpg");
            background-repeat: no-repeat;
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

<img src="/static/img/CSS专题/背景属性4.png" style="zoom:60%;" />  

#### **2.3 设置背景图片位置**

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div {
            height: 1000px;
            width: 1000px;    
            background-image: url("avatar.jpg")
            /*在标签最中间显示图片*/
            background-position: center;
            /*在标签左上方显示*/
            background-position: top left;
            /*图片左上角顶点距离窗口上侧200像素，左侧200像素位置显示*/
            background-position: 200px 200px;
        }
    </style>
</head>
<body>
    <div></div>
</body>
</html>
```

**补充：**背景属性支持简写赋值

```css
div{
    background:#ffffff url('1.png') no-repeat right top;
}
```

## **盒子模型**

### 一、盒子模型简介

​       标签主要由内容、边框、内边距、外边距几个内容组成，我们可以在前端页面通过检查（F12）查看标签的详细组成，如：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            height: 300px;
            width: 300px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div>
        我是div标签
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/盒子模型1.png" style="zoom:60%;" />      <img src="/static/img/CSS专题/盒子模型.png" style="zoom:80%;" />      

​     上述示例中的 `div` 标签实际上的结构是右图中所示，`我是div标签` 文本内容的位置是中间的蓝色区域，由于边框、外边距、内边距都没有设置值，所以在网页中看不出什么效果。

### 二、内边距属性

内边距是指盒子边框到盒子内容之间的距离，例如：

- 随便设置一个盒子，此时还未设置内边距

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            height: 200px;
            width: 200px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div>
        我是cdc
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/内边距属性1.png" style="zoom:80%;" />                 <img src="/static/img/CSS专题/内边距属性2.png" style="zoom:90%;" /> 

此时元素在页面中所占的大小就是原始内容的大小，即 `200px * 200px`

- 给盒子设置一个内边距

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            height: 200px;
            width: 200px;
            background-color: red;
            padding: 50px;
        }
    </style>
</head>
<body>
    <div>
        我是cdc
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/内边距属性3.png" style="zoom:70%;" />                     <img src="/static/img/CSS专题/内边距属性4.png" style="zoom:90%;" /> 

​       设置内边距的值为`50px`，此时元素内容距离四周都会多出了`50px`的距离，整个元素在页面中所占的大小为 `(50+200+50)px *(50+200+50)px`，即`300px * 300px`，与未设置内边距相比，整个元素大了一圈。这种现象在实际应用中会造成一些影响，因为内边距的使用改变了元素原始的大小，有可能会影响其他元素的一些布局。因此在设置内边距的同时，最好要保证元素大小不变，这就需要通过计算来减少元素内容的高度或宽度了。例如上述示例中，要是还想保持添加内边距后的元素的大小为原来的 `200px * 200px`，就需要将内容的高度和宽度都减去 `100px`

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            /*height: 200px;*/
            height: 100px;
            /*height: 200px;*/
            width: 100px;
            background-color: red;
            padding: 50px;
        }
    </style>
</head>
<body>
    <div>
        我是cdc
    </div>
</body>
</html>
```

#### **2.1 根据方向来设置内边距**

```css
   <style>
        div {
            /*设置上方的内边距*/
            padding-top: 10px;
            /*设置下方的内边距*/
            padding-bottom: 10px;
            /*设置左侧的内边距*/
            padding-left: 20px;
            /*设置右侧的内边距*/
            padding-right: 20px;

            /*同时设置上下左右的值*/
            padding: 20px;

            /*上下   左右*/
            padding: 10px 20px;

            /*上   左右   下*/
            padding: 10px 20px 30px;

            /*上   右   下   左*/
            padding: 10px 20px 30px 40px;
        }
    </style>
```

### 三、边框属性

通过 `border` 属性，我们可以来给标签设置边框属性，如给上述示例中的 `div` 标签套一层黑色的边框：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            height: 300px;
            width: 300px;
            background-color: red;
            border: 3px solid black;
        }
    </style>
</head>
<body>
    <div>
        我是div标签
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框属性1.png" style="zoom:60%;" />  

#### **3.1 按照三要素来设置边框属性**

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            height: 300px;
            width: 300px;
       
            /*按照三要素来设置边框属性*/
            border-width: 3px;
            border-style: solid dashed dotted double;
            border-color: green red purple black;
        }
    </style>
</head>
<body>
    <div>
        我是div标签
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框属性2.png" style="zoom:60%;" />  

#### **3.2 按照方向来设置边框属性**

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        div{
            height: 300px;
            width: 300px;
      
            /*按照方向来设置边框属性*/
            border-top-color: red;
            border-top-style: solid;
            border-top-width: 7px;

            border-bottom-color: black;
            border-bottom-style: double;
            border-bottom-width: 3px;
        }
    </style>
</head>
<body>
    <div>
        我是div标签
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框属性3.png" style="zoom:60%;" /> 

**注意：**设置了边框属性以后，元素在页面所占的真实大小为 `内容+内边距+边框`

#### **3.3 设置输入框样式**

通常我们使用标签时，标签都会有一些自身默认的样式，例如输入框：

<img src="/static/img/CSS专题/边框属性4.gif" style="zoom:80%;" /> 

修改默认样式，为输入框设置新的样式：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        input{
            /*清除浏览器默认的边框样式*/
            border: none;
            /*清除外线样式，即光标聚焦时输入框的样式*/
            outline: none;
        }

        .username{
            width: 190px;
            height: 40px;
            font-size: 20px;
            border: 1px solid #666;
            padding-left: 10px;
        }

        .username:hover{
            border: 1px solid orange;
        }
    </style>
</head>
<body>
    <input type="text" class="username">
</body>
</html>
```

<img src="/static/img/CSS专题/边框属性5.gif" style="zoom:80%;" />  

### 四、外边距属性

外边距是指一个盒子到另一个盒子的距离，例如：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            background-color: red;
            margin-right: 100px;
            margin-left: 50px;
        }

        .box2 {
            background-color: green;
        }
    </style>
</head>
<body>
    <span class="box1">
        cdc
    </span>
    <span class="box2">
        tr
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/外边距属性1.png" style="zoom:100%;" /> 

​       给第一个盒子设置了外边框属性，距离左侧`50px`，距离右侧`100px`。外边距和内边距一样，也可以通过方向去设置每一侧的距离，也支持简写。 

#### **4.1 外边距塌陷**

在水平方向给元素设置外边距，外边距会有一个叠加效果，元素之间的距离为元素之间外边距的和，例如：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            background-color: red;
            margin-right: 100px;
        }

        .box2 {
            background-color: green;
            margin-left: 200px;
        }
    </style>
</head>
<body>
    <span class="box1">
        cdc
    </span>
    <span class="box2">
        tr
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/外边距属性2.png" style="zoom:100%;" /> 

但是在竖直方向，元素之间的距离并不会叠加，会出现距离的合并，如：

```css
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box3 {
            height: 100px;
            width: 100px;
            background-color: red;
            margin-bottom: 100px;

        }

        .box4 {
            height: 100px;
            width: 100px;
            background-color: green;
            margin-top: 30px;
        }
    </style>
</head>
<body>
    <div class="box3"></div>
    <div class="box4"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/外边距属性3.png" style="zoom:80%;" />             <img src="/static/img/CSS专题/外边距属性4.png" style="zoom:100%;" /> 

​       两个元素之间的距离并不是想象中的 `130px`，而是`100px`，这种现象称为 `外边距塌陷` 现象，竖直方向较大的外边距会合并数值较小的外边距。

#### **4.2 设置元素水平居中**

我们可以通过使用外边距属性，使一个 HTML 元素实现水平居中的效果，例如当前有两个嵌套的`div`标签

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .top-bar {
            height: 40px;
            width: 100%;
            background-color: black;
        }

        .container {
            height: 100%;
            width: 1200px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div class="top-bar">
        <div class="container">
    
        </div>
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/水平居中1.png" style="zoom:100%;" />

 想让红色的元素在黑色的元素内部居中显示，我们可以给内部的元素左右各设置一个自动的外边距值：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="reset.css">
    <style>
        .top-bar {
            height: 40px;
            width: 100%;
            background-color: black;
        }

        .container {
            height: 100%;
            width: 1200px;
            background-color: red;

            /*左右各设置一个自动的外边距值*/
            /*margin-left: auto;*/
            /*margin-right: auto;*/

            /*上述代码可以简写成*/
            margin: 0 auto;
        }
    </style>
</head>
<body>
    <div class="top-bar">
        <div class="container">

        </div>
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/水平居中2.png" style="zoom:100%;" /> 

### 五、补充

如果一个元素设置了 `内边距`、`边框`、`外边距`，那么元素在页面中实际的大小为：

- 实际宽 =左右margin + 左右border + 左右padding + 内容width
- 实际高 =上下margin + 上下border + 上下padding + 内容height

## 清除默认样式

​       HTML 标签都有自己默认的一些样式，在实际应用中，这些标签的默认样式对于整个页面排版的影响是十分大的，因此通常我们都要清除 HTML 的默认样式。许多前端大神已经帮我们把样式清除的规则编写好了，我们只需要导入使用即可，我们也可以根据实际需求去新增或修改清除样式的代码。

- 在工程种新建一个 `CSS` 文件，用于保存清除样式的 CSS 代码

```css
/*reset.css  用于清除HTML标签默认的样式*/
/*网址连接：https://meyerweb.com/eric/tools/css/reset/*/
/* http://meyerweb.com/eric/tools/css/reset/ 
   v2.0 | 20110126
   License: none (public domain)
*/

html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure, 
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
```

- 在 HTML 文件种引入该 CSS 文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="reset.css">
</head>
<body>

</body>
</html>
```

## 浮动

### 一、标准文档流和浮动

​       在页面布局种，所有的 HTML 的标签都遵循 `从左往右、从上往下` 的顺序流式的进行排列，我们称这种现象为 `标准文档流`。一旦元素被设置了浮动属性，该元素就会脱离文档流，形象的来理解，现在有一幅人物画，我们把画中的人从画布上扣了出来，乍一看这个人物还是在画布上的，实际已经脱离了画布，因此我们可以把它移动到画布上的任意位置。

​		在 `CSS` 中，任何元素都可以浮动。浮动元素会生成一个块级框，而不论它本身是何种元素。浮动的框可以向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。由于浮动框脱离了文档流，所以文档的普通流中的块框表现得就像浮动框不存在一样，因此原占据的空间不存在，但会占据文本空间。早期浮动属性的提出就是为了实现文字环绕的页面效果，后期多用于页面的多栏样式布局。

### 二、浮动属性的取值

| 属性取值 | 效果描述               |
| -------- | ---------------------- |
| none     | 默认取值，表示不浮动。 |
| left     | 向左浮动。             |
| right    | 向右浮动。             |
| inherit  | 继承父元素的浮动属性。 |

### 三、浮动元素的特点

#### **3.1 文字环绕**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        img {
            width: 100px;
        }
    </style>
</head>
<body>
<img src="avatar.jpg" alt="" class="avatar">
<p>简单点说话的方式简单点 ,递进的情绪请省略......分开就平淡些。
</p>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动1.png" style="zoom:100%;" /> 

给图片设置向左浮动属性：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        img {
            width: 100px;
            float: left;
        }
    </style>
</head>
<body>
<img src="avatar.jpg" alt="" class="avatar">
<p>简单点说话的方式简单点 ,递进的情绪请省略......分开就平淡些。
</p>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动2.png" style="zoom:100%;" /> 

#### **3.2 脱离标准文档流**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            width: 200px;
            height: 200px;
            background-color: red;
        }

        .box2 {
            width: 300px;
            height: 300px;
            background-color: green;
        }
    </style>
</head>
<body>
	<div class="box1"></div>
	<div class="box2"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动3.png" style="zoom:40%;" />   

​		给上面红色的盒子设置一个浮动的属性，此时红色的盒子会脱离文档流，不再是页面上的第一个元素。绿色的盒子成为第一个元素，会替代红色盒子原来的位置：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            width: 200px;
            height: 200px;
            background-color: red;
            float: left;
        }

        .box2 {
            width: 300px;
            height: 300px;
            background-color: green;
        }
    </style>
</head>
<body>
	<div class="box1"></div>
	<div class="box2"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动4.png" style="zoom:60%;" /> 

#### **3.3 浮动元素相互贴靠**

如果有多个浮动元素向一个方向进行浮动，第一个元素会贴靠边缘，后面的元素会贴靠之前浮动的元素

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            width: 200px;
            height: 200px;
            background-color: red;
            float: left;
        }

        .box2 {
            width: 200px;
            height: 200px;
            background-color: green;
            float: left;
        }

        .box3 {
            width: 200px;
            height: 200px;
            background-color: orange;
            float: left;
        }
    </style>
</head>
<body>
    <div class="box1"></div>
    <div class="box2"></div>
    <div class="box3"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动5.png" style="zoom:80%;" /> 

#### **3.4 浮动元素有收缩现象**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {   
            height: 200px;
            background-color: red;
        }

        .box2 {
            height: 200px;
            background-color: green;
        }
    </style>
</head>
<body>
    <div class="box1">box1</div>
    <div class="box2">box2</div>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动6.png" style="zoom:100%;" />  

如果不给浮动元素设置宽度或者高度，当元素浮动后，元素会进行收缩，收缩后的大小以元素的内容大小一致：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {   
            height: 200px;
            background-color: red;
            float: left;
        }

        .box2 {
            height: 200px;
            background-color: green;
            float: right;
        }
    </style>
</head>
<body>
    <div class="box1">box1</div>
    <div class="box2">box2</div>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动7.png" style="zoom:100%;" /> 

### 四、浮动元素带来的破坏性

#### **4.1 高度塌陷问题**

​		当父级元素中包含的元素都进行浮动后，父级元素内部就没有可以支撑起自身大小的内容，因而父级元素整体会产生一个塌陷，如：

- 内部元素没有浮动前：

<img src="/static/img/CSS专题/浮动8.png" style="zoom:100%;" /> 

- 内部元素浮动后：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            border: 2px solid red;
        }

        .child1 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .child2 {
            height: 200px;
            width: 200px;
            background-color: green;
            float: right;
        }
    </style>
</head>
<body>
<div class="father">
    <div class="child1"></div>
    <div class="child2"></div>
</div>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动9.gif" style="zoom:100%;" /> 

由于没有内容的填充，父标签高度塌陷成了一条线

### 五、清除浮动

#### **5.1 固定高度**

​		给父元素设置一个固定的高度，这样即使内部元素进行浮动，父元素也不会出现高度塌陷。但是该方法使用不灵活，后期维护不方便，一旦内部元素高度改变，父元素也要跟着修改。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            border: 2px solid red;
            height: 200px;
        }

        .child1 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .child2 {
            height: 200px;
            width: 200px;
            background-color: green;
            float: right;
        }
    </style>
</head>
<body>
<div class="father">
    <div class="child1"></div>
    <div class="child2"></div>
</div>
</body>
</html>
```

#### **5.2 内墙法**

​		在最后一个浮动的元素后面加一个空的块级元素，并设置属性 `clear:both`，使用该方式有结构冗余的缺点，需要在所有设置浮动的地方都添加该操作。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            border: 2px solid red;
        }

        .child1 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .child2 {
            height: 200px;
            width: 200px;
            background-color: green;
            float: right;
        }

        .clear {
            clear: both;
        }
    </style>
</head>
<body>
<div class="father">
    <div class="child1"></div>
    <div class="child2"></div>
    <div class="clear"></div>
</div>
</body>
</html>
```

#### **5.3 为元素清除法**

借鉴内墙法的思路，我们可以结合为元素选择器，解决清除浮动影响所产生的代码冗余问题：

- 在父级元素的所有子元素最后面添加一个空的块级内容

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            border: 2px solid red; 
        }

        .child1 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .child2 {
            height: 200px;
            width: 200px;
            background-color: green;
            float: right;
        }

        .clearfix::after {
            content: "";
            display: block;
            
        }
    </style>
</head>
<body>
<div class="father clearfix">
    <div class="child1"></div>
    <div class="child2"></div>
</div>
</body>
</html>
```

- 在 `clearfix` 内使用内墙发清除浮动

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            border: 2px solid red; 
        }

        .child1 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .child2 {
            height: 200px;
            width: 200px;
            background-color: green;
            float: right;
        }

        .clearfix::after {
            content: "";
            display: block;
            clear: both;
        }
    </style>
</head>
<body>
<div class="father clearfix">
    <div class="child1"></div>
    <div class="child2"></div>
</div>
</body>
</html>
```

这样只要给元素设置 `clearfix` 属性，就可以实现清除浮动的效果。

#### **5.4 overflow 清除**

当一个标签内的内容过多超出标签大小范围时，可以使用 `overflow` 进行效果控制：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        p {
            height: 100px;
            width: 300px;
            border: 1px solid black;
        }
    </style>
</head>
<body>
<p>
    什么时候我们开始收起了底线 ,顺应时代的改变看那些拙劣的表演 ,可你曾经那么爱我干嘛演出细节 ,我该变成什么样子才能延缓厌倦 ,原来当爱放下防备后的这些那些 ,才是考验 ,没意见你想怎样我都随便 ,你演技也有限 ,又不用说感言
    ,分开就平淡些。简单点说话的方式简单点 ,递进的情绪请省略 ,你又不是个演员 ,别设计那些情节 ,没意见我只想看看你怎么圆 ,你难过的太表面 像没天赋的演员 ,观众一眼能看见 ,该配合你演出的我演视而不见
    ,在逼一个最爱你的人即兴表演
</p>
</body>
</html>
```

<img src="/static/img/CSS专题/隐藏属性1.png" style="zoom:80%;" />  

| 取值    | 效果描述                       |
| ------- | ------------------------------ |
| visible | 属性的默认值，不隐藏超出的内容 |
| scroll  | 隐藏超出部分，并带有一个滚动条 |
| auto    | 由浏览器自动处理超出的部分     |
| hidden  | 隐藏超出的部分                 |
| inherit | 继承父级元素的隐藏方式         |

在父级元素添加 `overflow:hidden` 属性清除浮动影响

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .father {
            border: 2px solid red;
            overflow: hidden;
        }

        .child1 {
            height: 200px;
            width: 200px;
            background-color: red;
            float: left;
        }

        .child2 {
            height: 200px;
            width: 200px;
            background-color: green;
            float: right;
        }
    </style>
</head>
<body>
<div class="father">
    <div class="child1"></div>
    <div class="child2"></div>
</div>
</body>
</html>
```

### 六、BFC

#### **6.1 Box和Formatting Context**

​		Box，即盒子，是构成页面的基本元素，分为 `block`、`inline`、`inline-block` 三种类型；

​		Formatting Context，是 `W3C` 的规范中的一种概念。它是页面中的一块渲染区域，并且有一套渲染规则，它决定了其子元素将如何定位，以及和其他元素的关系和相互作用。常见的 Formatting Context 有 `Block Fomatting Context` (简称 BFC) 和  `Inline Formatting Context`  (简称 IFC)

#### **6.2 BFC 定义**

​		BFC (Block formatting context) 直译为"块级格式化上下文"。它是一个独立的渲染区域，只有Block-level box参与， 它规定了内部的Block-level Box如何布局，并且与这个区域外部毫不相干，即多个 BFC 区域之间互不干涉。

#### **6.3 BFC 布局规则**

1. 内部的Box会在垂直方向，一个接一个地放置；
2. Box 垂直方向的距离由 margin 决定。属于同一个 BFC 的两个相邻 Box 的 margin 会发生重叠；
3. 每个元素的 margin box 的左边，与包含块 border box 的左边相接触 (对于从左往右的格式化，否则相反)，即使存在浮动也是如此；
4. BFC 的区域不会与float 元素重叠；
5. BFC 就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素，反之也如此；
6. 计算 BFC 的高度时，浮动元素也参与计算。

#### **6.4 BFC 区域的形成条件**

1. 根元素，即 `html` 标签就是一个 BFC 区域；
2. float 属性不为 none；
3. position 为 absolute 或 fixed；
4. display 为 inline-block；
5. overflow 不为 visible

因此，只要对受浮动影响的元素进行 overflow 属性值的相关设置，就能形成一个 BFC 区域，根据布局规则的最后一条，浮动元素也参与计算，就可以消除浮动元素带来的影响了。

## 定位

### 一、静态定位

`position: static`，定位属性默认值，所有元素在页面中默认的都是静态定位。

### 二、相对定位

`position: relative`

​		对元素进行相对定位属性设置，其实和原来的静态定位没有什么区别，但是可以使用相对定位的一些属性来移动元素的位置。**相对定位是不脱离文档流的，元素移动的方向和距离，都是依照元素原来的位置来计算的。**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            height: 200px;
            width: 200px;
            background-color: green;
            position: relative;
            left: 50px;
            top: 100px;
        }

        .box2 {
            height: 200px;
            width: 200px;
            background-color: orange;
        }
    </style>
</head>
<body>
    <div class="box1">
        One
    </div>
    <div class="box2">
        Two
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/相对定位.gif" style="zoom:80%;" /> 

​		上述示例中，绿色的盒子移动轨迹如图所示，相对于自己原来的位置先向右移动 `50px`，在向下移动 `100px`，即距离原来位置的左边50像素，距离原来位置的顶部100像素。

### 三、绝对定位

#### **3.1 绝对定位的特点**

- **设置了绝对定位的元素会脱离文档流**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            height: 200px;
            width: 200px;
            background-color: red;
        }

        .box2 {
            height: 200px;
            width: 400px;
            background-color: green;
        }

        .box3 {
            height: 200px;
            width: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
<div class="box1">
    One
</div>
<div class="box2">
    Two
</div>
<div class="box3">
    Three
</div>
</body>
</html>
```

<img src="/static/img/CSS专题/绝对定位1.png" style="zoom:60%;" />  

给第一个红色的盒子设置绝对定位的属性：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            height: 200px;
            width: 200px;
            background-color: red;
            position: absolute;
        }

        .box2 {
            height: 200px;
            width: 400px;
            background-color: green;
        }

        .box3 {
            height: 200px;
            width: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
<div class="box1">
    One
</div>
<div class="box2">
    Two
</div>
<div class="box3">
    Three
</div>
</body>
</html>
```

<img src="/static/img/CSS专题/绝对定位2.png" style="zoom:60%;" /> 

​		观察可得，绿色的盒子挤到了第一个元素的位置，红色的盒子覆盖在了绿色的上面，此时红色的盒子不再是页面上的第一个元素，所以设置了绝对定位的元素会脱离文档流。

- **设置了绝对定位的元素的层级会得到提高，且后设置的元素会压盖之前设置的元素**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box1 {
            height: 200px;
            width: 200px;
            background-color: red;
            position: absolute;
        }

        .box2 {
            height: 200px;
            width: 400px;
            background-color: green;
            position: absolute;
        }

        .box3 {
            height: 200px;
            width: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
<div class="box1">
    One
</div>
<div class="box2">
    Two
</div>
<div class="box3">
    Three
</div>
</body>
</html>
```

在上述示例的基础上，给绿色的盒子也设置一个绝对定位属性：

<img src="/static/img/CSS专题/绝对定位3.png" style="zoom:60%;" /> 

​		首先，绿色和红色的盒子都已经因为设置了绝对定位而脱离了文档流，蓝色的盒子成为了页面上的第一个元素，移动到了最上面；接着，由于设置了绝对定位的元素层级提高，会发生覆盖，且绿色的盒子晚于红色的盒子设置了绝对定位属性，所以最后绿色的盒子覆盖了所有。

#### **3.2 绝对元素的参考点**

​		设置了绝对定位的元素会相对于最近的非 `static` 的祖先元素定位，如果没有非 `static` 的祖先元素，那么就以页面根元素左上角进行定位。例如：定义了以下嵌套结构的盒子：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .grandfather{
            border: 3px black solid;
            margin: 20px;
        }

        .father{
            border: 3px purple solid;
            margin: 20px;
        }

        .box1 {
            height: 200px;
            width: 200px;
            background-color: red;
        }

        .box2 {
            height: 200px;
            width: 400px;
            background-color: green;
        }

        .box3 {
            height: 200px;
            width: 200px;
            background-color: blue;
        }
    </style>
</head>
<body>
    <div class="grandfather">
        <div class="father">
            <div class="box1">One</div>
            <div class="box2">Two</div>
            <div class="box3">Three</div>
        </div>
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/绝对定位4.png" style="zoom:60%;" />  

先给 .grandfather  和 .father 设置一个相对定位属性，给 .box3 设置一个绝对定位后先向右移动 100px，再向下移动 100px：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .grandfather{
            border: 3px black solid;
            margin: 20px;
            position: relative;
        }

        .father{
            border: 3px purple solid;
            margin: 20px;
            position: relative;
        }

        .box1 {
            height: 200px;
            width: 200px;
            background-color: red;
        }

        .box2 {
            height: 200px;
            width: 400px;
            background-color: green;
        }

        .box3 {
            height: 200px;
            width: 200px;
            background-color: blue;
            position: absolute;
            top: 100px;
            left: 100px;
        }
    </style>
</head>
<body>
    <div class="grandfather">
        <div class="father">
            <div class="box1">One</div>
            <div class="box2">Two</div>
            <div class="box3">Three</div>
        </div>
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/绝对定位5.png" style="zoom:60%;" /> 

此时 .box3 是以 .father 的左上角作为参考点的，接着我们将 .father 中的定位属性去除，只保留 .grandfather 中的定位属性：

```html
<style>
        .grandfather{
            border: 3px black solid;
            margin: 20px;
            position: relative;
        }

        .father{
            border: 3px purple solid;
            margin: 20px;
            /*position: relative;*/
        }
</style>
```

<img src="/static/img/CSS专题/绝对定位6.png" style="zoom:60%;" /> 

此时 .box3 是以 .grandfather 的左上角作为参考点的，最后我们将 .grandfather 中的定位属性也去除

```html
 <style>
        .grandfather{
            border: 3px black solid;
            margin: 20px;
            /*position: relative;*/
        }

        .father{
            border: 3px purple solid;
            margin: 20px;
            /*position: relative;*/
        }
</style>
```

<img src="/static/img/CSS专题/绝对定位7.png" style="zoom:60%;" /> 

此时 .box3 找不到最近的非 `static` 的祖先元素了，直接以整个浏览器页面的左上角为参考点。

**补充：**在实际的网页布局中，我们常常会将父级元素设置为`相对定位`，将子元素设置为`绝对定位` ，即 `子绝父相` ，这样的结构有利于我们对元素进行整体的排版。

### 四、固定定位

给元素设置固定定位属性后，元素具备以下特点：

- 元素脱离文档流；
- 一旦设置固定定位，滚动页面时元素保持原有位置不动；
- 以页面根元素左上角为参考点。

固定定位在实际的应用中有很多，如导航栏、返回顶部按钮等等都可以通过固定定位实现。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box {
            width: 400px;
            height: 500px;
            border: 2px black solid;
            overflow-y: scroll;
            padding-left: 200px;
        }

        .pic{
            position: fixed;
            left: 50px;
            top: 100px;
        }
    </style>
</head>
<body>
<div class="box">
    屏幕尺寸：6.53英寸*
    屏幕色彩：1670万色，DCI-P3广色域
    屏幕类型：OLED
	......
</div>

<div class="pic">
    <img src="固定定位1.png" alt="" width="150">
</div>

</body>
</html>
```

<img src="/static/img/CSS专题/固定定位1.gif" style="zoom:60%;" /> 

上述示例中，将图片设置为固定定位后，滚动产品介绍，图片的位置始终可以保持不变。

## 浮动和定位给行内元素带来的影响

​		我们都知道，行内元素的大小是由元素的内容决定的，通过设置元素的宽高属性并不能改变元素原有的大小：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .s1{
            background-color: red;
            height: 200px;
            width: 200px;
        }
    </style>
</head>
<body>
    <span class="s1">
        我是行内元素
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动定位影响1.png" style="zoom:100%;" /> 

我们可以通过设置 `display` 属性将行内元素转换为一个块级元素，此时就可以通过设置宽高属性去改变元素的大小了。除此之外，如果`行内元素被设置了浮动属性`，设置宽高属性也是可以直接生效的。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .s1{
            float: left;
            background-color: red;
            height: 200px;
            width: 200px;
        }
    </style>
</head>
<body>
    <span class="s1">
        我是行内元素
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动定位影响2.png" style="zoom:80%;" />  

这是因为设置了浮动的元素已经脱离了标准文档流，不用再受到文档流的规则约束，同样，通过给行内元素设置定位属性使其脱离文档流后也能直接改变元素的大小：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .s1{
            position: absolute;
            background-color: red;
            height: 200px;
            width: 200px;
        }
    </style>
</head>
<body>
    <span class="s1">
        我是行内元素
    </span>
</body>
</html>
```

<img src="/static/img/CSS专题/浮动定位影响2.png" style="zoom:80%;" /> 

**注意：**只有给行内元素设置`绝对定位`和`固定定位`才能实现上述效果，因为只有这两种方式才能将元素脱离文档流。

**总结：**

- 行内元素除了通过 `display:block` 转变为块级元素的方式，通过设置浮动和定位属性后，也可以直接设置宽高属性控制大小；
- `display` 和设置浮动、定位的区别在于，前者元素是没有脱离文档流的，而后面两种方式会使元素脱标，所以要结合具体的需求使用最合适的方式。 

## z-index

元素在页面上会存在压盖的现象，后设置的元素会压盖之前设置的元素，如：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .a{
            height: 200px;
            width: 400px;
            background-color: red;
            position: relative;
        }
        
        .b{
            height: 200px;
            width: 400px;
            background-color: blue;
            position: relative;
            top: -100px;
            left: 200px;
        }
    </style>
</head>
<body>
    <div class="a"></div>
    <div class="b"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/z-index_1.png" style="zoom:60%;" /> 

如果想改变元素间的压盖关系，可以通过设置 `z-index` 属性来实现。属性值为整数，数值越大则级别越高，会在最上面（也可以直接理解为元素的 x 轴坐标，值越大证明这个盒子越在上面），默认值为 `auto`，我们给红色的盒子的 `z-index`  设置一个属性值：

```css
.a{
            height: 200px;
            width: 400px;
            background-color: red;
            position: relative;
            z-index: 1;
        }
```

此时红色盒子的 `z-index` 值为1，大于蓝色盒子的默认值 auto，红色盒子会压盖在蓝色盒子之上：

<img src="/static/img/CSS专题/z-index_2.png" style="zoom:60%;" /> 

如果将蓝色盒子的 `z-index` 值设为100，蓝色盒子又会压盖在红色盒子之上：

```css
.a{
            height: 200px;
            width: 400px;
            background-color: red;
            position: relative;
            z-index:1;
        }

        .b{
            height: 200px;
            width: 400px;
            background-color: blue;
            position: relative;
            top: -100px;
            left: 200px;
            z-index: 100;
        }
```

<img src="/static/img/CSS专题/z-index_1.png" style="zoom:60%;" /> 

此外，`z-index` 属性还存在从父现象，即只要元素的父级元素的 `z-index`值大于其他元素，那么即使元素本身的值很小，也会压盖在其他元素之上：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .a{
            height: 200px;
            width: 400px;
            background-color: red;
            position: relative;
            z-index:1;
        }

        .b{
            height: 200px;
            width: 400px;
            background-color: blue;
            position: relative;
            top: -100px;
            left: 200px;
            z-index: 100;
        }
    </style>
</head>
<body>
    <div style="position:relative;z-index: 10000">
         <div class="a"></div>
    </div>
    <div class="b"></div>
</body>
</html>
```

 <img src="/static/img/CSS专题/z-index_2.png" style="zoom:60%;" />

## opacity

通过 `opacity` 属性，既可以改变背景色的透明度，也可以改变字体的透明度，而 `rgba` 只能改变背景色的透明度：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .a {
            background-color: rgba(0, 0, 0, 0.2);
            color: blue;
        }

        .b {
            background-color: rgb(0, 0, 0);
            opacity: 0.2;
            color: blue;
        }
    </style>
</head>
<body>
    <div class="a">
        aaa
    </div>
    <hr>
    <div class="b">
        bbb
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/透明度.png" style="zoom:60%;" /> 