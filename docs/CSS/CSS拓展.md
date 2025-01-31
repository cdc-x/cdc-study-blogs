# CSS拓展

## **雪碧图技术**

### 一、前戏：CSS背景定位属性

在之前的 CSS 属性中介绍过如何设置背景图片的位置，这里再回顾一下。先设置如下背景：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .bg {
            height: 300px;
            width: 600px;
            background-color: red;
            /*设置背景图片*/
            background-image: url("avatar.jpg");
            /*设置图片不平铺*/
            background-repeat: no-repeat;
            /*设置背景图片大小*/
            background-size: 70px 100px;
        }
    </style>
</head>
<body>
<div class="bg"></div>
</body>
</html>
```

![](/static/img/CSS专题/CSS定位1.png) 

控制背景图片位置可以通过 `background-position` 属性实现，属性值可以有以下三种形式：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .bg {
            height: 300px;
            width: 600px;
            background-color: red;
            /*设置背景图片*/
            background-image: url("avatar.jpg");
            /*设置图片不平铺*/
            background-repeat: no-repeat;
            /*设置背景图片大小*/
            background-size: 70px 100px;

            /*像素*/
            /*和容器左边的距离*/
            background-position-x: 100px;
            /*和容器上边的距离*/
            background-position-y: 100px;
            /*第一个值表示和容器左边的距离，第二个值表示和容器上边的距离*/
            /*background-position: 100px 200px;*/

            /*关键字*/
            /*显示在容器左上角*/
            background-position: top left;
             /*显示在容器右上角*/
            background-position: top right;
             /*显示在容器左下角*/
            background-position: bottom left;
             /*显示在容器右下角*/
            background-position: bottom right;

            /*百分比*/
            /*百分比计算的值：
                - 水平方向 = (背景容器的宽度 - 背景图片的宽度) * 百分比
                - 数值方向 = (背景容器的高度 - 背景图片的高度) * 百分比
            */
            background-position: 10% 20%;
        }
    </style>
</head>
<body>
<div class="bg"></div>
</body>
</html>
```

### 二、雪碧图技术介绍

​       雪碧图技术（Sprite）是一种图像拼合技术，它将多个小图标和背景图像合并到一张图片上，再利用 CSS 的背景定位来显示需要显示的图片部分。

#### **2.1 使用场景**

- 显示的图片为静态图片，不随用户信息的变化而变化
- 比较小的图片（2~3kb）
- 加载量比较大

**注意：**一些大图，例如轮播图等不建议使用雪碧图技术去实现

#### **2.2 使用的好处**

​		我们每请求一次加载图片，就会和服务器链接一次，建立链接需要额外的时间开销，而使用雪碧图技术可以有效减少 HTTP 请求数量，加速了内容的显示。

#### **2.3 使用实例**

很多网上商城项目中都频繁使用到了雪碧图技术，例如淘宝的分类导航：

![](/static/img/CSS专题/雪碧图技术1.png) 

右侧标注的地方就是应用雪碧图技术实现的，它实际的背景图片并不是页面上显示的排版样式，而是如下排列：

​                                                                    <img src="/static/img/CSS专题/雪碧图技术2.png" style="zoom:30%;" />  

### 三、雪碧图实现

雪碧图的实现主要就是依靠背景定位属性 `background-position`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .bg{
            height: 24px;
            width: 24px;
            background-image: url("taobao.png");
            background-size: 24px 597px;
        }
    </style>
</head>
<body>
    <div class="bg"></div>
</body>
</html>
```

​       先设置一个固定大小的背景容器，由于背景图片大小超出了我们设置容器大小，因此只会有一部分的图片能显示在容器中。如图：

​                                                           <img src="/static/img/CSS专题/雪碧图技术3.png"/> 

此时在个背景设置一个定位属性，并尝试改变定位属性的值，我们可以看到以下情形：

<img src="/static/img/CSS专题/雪碧图技术4.gif" style="zoom: 67%;" /> 

当 y 轴大小改变时，定位到的图片也发生改变，所以只要计算好距离，我们可以通过定位属性来找到并显示想展示的小图片：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .bg{
            height: 24px;
            width: 24px;
            background-image: url("taobao.png");
            background-size: 24px 597px;
            display: inline-block;
        }
        .pic1{
            background-position: 0 0;
        }
        .pic2{
            background-position: 0 -44px;
        }
        .pic3{
            background-position: 0 -88px;
        }
        .pic4{
            background-position: 0 -132px;
        }
    </style>
</head>
<body>
    <div class="bg pic1"></div>
    <div class="bg pic2"></div>
    <div class="bg pic3"></div>
    <div class="bg pic4"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/雪碧图技术5.png" style="zoom:67%;" /> 

**补重**：雪碧图在线生成网站 https://www.toptal.com/developers/css/sprite-generator

## **圆形边框**

 通过边框圆角属性 `border-radius` 可以绘制出圆角的边框

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            width: 200px;
            height: 200px;
            background-color: red;
            border-radius: 20px;
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框圆角1.png" style="zoom:40%;" /> 

其原理是边框每个角落从顶点开始取对应的长度作为半径画出相切的圆形，保留圆形边框的部分，舍弃盒子原来对应部分的边框，如图（黑色的框是原来盒子的边框）：

<img src="/static/img/CSS专题/边框圆角2.png" style="zoom:40%;" /> 

也可以针对四个角设置不同的半径长度：

```css
/*四个角落都是20px为半径画圆*/
border-radius: 20px;

/*左上和右下取10px为半径 右上和左下取30px为半径*/
border-radius: 10px 30px;

/*左上取10px为半径 右上和左下取30px为半径 右下取50px为半径*/
border-radius: 10px 30px 50px;

/*左上取10px为半径 右上取30px为半径 右下取50px为半径 左下取90px为半径*/
border-radius: 10px 30px 50px 90px;
```

也可以针对一个角落在横纵上取不同的半径长度：

```css
border-top-left-radius: 15px 30px;
```

<img src="/static/img/CSS专题/边框圆角3.png" style="zoom:40%;" /> 

知道了边框圆角属性的原理，下面我们就可以绘制一些常见的圆角边框了：

#### **圆形**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            width: 200px;
            height: 200px;
            background-color: red;
            border-radius: 50%;
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框圆角4.png" style="zoom:40%;" /> 

#### **圆环**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            width: 100px;
            height: 100px;
            border-radius: 50%;
            border: 50px solid purple;
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框圆角5.png" style="zoom:40%;" /> 

#### **椭圆**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            width: 200px;
            height: 100px;
            background-color: red;
            border-radius: 50%;
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框圆角6.png" style="zoom:40%;" /> 

#### **半圆**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            width: 200px;
            height: 100px;
            background-color: red;
            border-top-left-radius: 100px;
            border-top-right-radius: 100px;
        }
    </style>
</head>
<body>
    <div class="box"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/边框圆角7.png" style="zoom:40%;" /> 

## **阴影属性**

通过 `box-shadow` 属性可以为盒子添加阴影效果：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .d1{
            height: 100px;
            width: 100px;
            background-color: red;
            box-shadow: 10px 10px 20px black;
        }
    </style>
</head>
<body>
    <div class="d1"></div>
</body>
</html>
```

四个值分别表示 阴影水平的方向、阴影竖直的方向、阴影的模糊程度、阴影的颜色，上述示例具体效果如下：

<img src="/static/img/CSS专题/阴影1.png" style="zoom:80%;" /> 

利用阴影属性，我们可以简单实现一个鼠标悬浮盒子移动的效果：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .d1{
            height: 100px;
            width: 100px;
            background-color: red;
            position: relative;
        }

        .d1:hover{
            top: -5px;
            left: -5px;
            box-shadow: 10px 10px 10px green;
        }
    </style>
</head>
<body>
    <div class="d1"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/阴影2.gif" style="zoom:80%;" /> 

**补充：**为上述示例添加延时效果

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .d1{
            height: 100px;
            width: 100px;
            background-color: red;
            position: relative;
        }

        .d1:hover{
            box-shadow: 10px 10px 10px green;
            transform: translate3d(0, -3px, 0); /*和定位一样，操纵盒子移动*/
            transition: all 3s; /*延时时间*/
        }
    </style>
</head>
<body>
    <div class="d1"></div>
</body>
</html>
```

<img src="/static/img/CSS专题/阴影3.gif" style="zoom:80%;" /> 

## **各种居中**

### 一、行内元素水平垂直居中

#### **1.1 通过 text-align 和 行高属性**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            height: 100px;
            width: 100px;
            background-color: red;
            color: #fff;
            text-align: center;
            line-height: 100px;
        }
    </style>
</head>
<body>
    <div class="box">
        <span>CDC</span>
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/居中1.png" style="zoom:70%;" /> 

#### **1.2 通过单元格方式** 

​      单元格 `<td></td>` 标签的 `vertical-align` 属性可以控制单元格内容的垂直位置，按照这个思路，我们可以将盒子变成一个单元格，再对盒子里的内容进行位置的设置

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .box{
            height: 100px;
            width: 100px;
            background-color: red;
            color: #fff;
            text-align: center;
            display: table-cell;
            vertical-align: center;
        }
    </style>
</head>
<body>
    <div class="box">
        <span>CDC</span>
    </div>
</body>
</html>
```

### 二、块级元素水平垂直居中

#### **2.1 定位+margin**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .fa{
            height: 200px;
            width: 200px;
            background-color: red;
            position: relative;
        }

        .ch{
            height: 100px;
            width: 100px;
            background-color: green;
            position: absolute;
            margin: auto;
            top: 0;
            bottom: 0;
            left: 0;
            right: 0;
        }
    </style>
</head>
<body>
    <div class="fa">
        <div class="ch"></div>
    </div>
</body>
</html>
```

#### **2.2 通过单元格方式**

​       由于 `text-align` 属性只能作用于行内元素或文本内容，所以想要通过该方式使块级元素居中，就必须把块级子元素转换为行内块元素。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .fa{
            height: 200px;
            width: 200px;
            background-color: red;
            display: table-cell;
            vertical-align: middle;
            text-align: center;
        }
        
        .ch{
            height: 100px;
            width: 100px;
            background-color: green;
            display: inline-block;

        }
    </style>
</head>
<body>
    <div class="fa">
        <div class="ch"></div>
    </div>
</body>
</html>
```

#### **2.3  通过定位**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .fa{
            height: 200px;
            width: 200px;
            background-color: red;
            position: relative;
        }

        .ch{
            height: 100px;
            width: 100px;
            background-color: green;
            position: absolute;
            
            /*50%是父级元素的50%，即100px，但实际上需要移动的是50px，所以移动过多*/
            top: 50%;
            left: 50%;
            
            /*通过 margin 再反向移动弥补多移动的50px*/
            margin-top: -50px;
            margin-left: -50px;
        }
    </style>
</head>
<body>
    <div class="fa">
        <div class="ch"></div>
    </div>
</body>
</html>
```

<img src="/static/img/CSS专题/居中2.png" style="zoom:50%;" /> 

## 媒体查询

页面最理想的显示效果就是针对浏览器不同尺寸的窗口可以做到适应。针对上述需要，我们可以使用媒体查询来实现：

```html
<!--在不同尺寸的窗口下显示不同的body样式示例-->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        /*窗口最小宽度为400px，即大于等于400px*/
        @media screen and (min-width: 1000px ) {
            body {
                background-color: red;
            }

        }
        /*窗口宽度为200px到400px之间*/
        @media screen and (min-width: 800px ) and (max-width: 1000px){
            body {
                background-color: green;
            }

        }
        /*窗口最大宽度为200px，即小于等于200px*/
        @media screen and (max-width: 800px){
            body {
                background-color: blue;
            }

        }
    </style>
</head>
<body>
</body>
</html>
```

<img src="/static/img/CSS专题/媒体查询.gif" style="zoom:50%;" /> 