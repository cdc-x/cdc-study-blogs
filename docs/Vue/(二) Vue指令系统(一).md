# 指令系统（一）

## 一、什么是指令

在 Vue 中提供了一些对于 **页面+数据** 的更为方便的输出，这些操作被称为指令，以 `v-xxx` 表示。指令中封装了一些DOM行为，结合属性作为一个暗号，暗号有对应的值，根据不同的值，框架会进行相关DOM操作的绑定。

## 二、插值绑定指令

### 2.1 `v-text` 

`v-text` 的作用和 `{{ }}` 插值表达式一样，都是插入值并直接渲染：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>

    <div id="app">
        <!-- 直接插入值渲染 -->
        <p v-text="'嘿嘿嘿嘿'"></p>
        <!-- 通过数据属性中的数据渲染 -->
        <p v-text="content"></p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                content: "哈哈哈哈哈"
            }
        })

    </script>
</body>
</html>
```

对于 html 代码，`v-text` 和 `{{ }}` 都无法渲染：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>
    <div id="app">
        <!-- {{}} 和 v-text 无法渲染 html 代码 -->
        <p>{{htmlContent}}</p>
        <p v-text='htmlContent'></p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                htmlContent: "<span>hello world</span>"
            }
        })
    </script>
</body>

</html>
```

<img src="/static/vue_02.png" style="zoom:80%;" /> 

### 2.2 `v-html`

`v-html` 指令既可以渲染值，又能渲染 html 标签：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>

<body>

    <div id="app">
        <!-- {{}} 和 v-text 无法渲染 html 代码 -->
        <p>{{htmlContent}}</p>
        <p v-text='htmlContent'></p>
        <!-- v-html 既可以渲染值 也可以渲染html标签 -->
        <p v-html='content'></p>
        <p v-html='htmlContent'></p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                content: "哈哈哈哈哈",
                htmlContent: "<span>hello world</span>"
            }
        })

    </script>
</body>

</html>
```

<img src="/static/vue_03.png" style="zoom:80%;" /> 

## 三、条件渲染指令

### 3.1 `v-if`

`v-if` 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回 true 值的时候被渲染。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- 表达式或数据属性为true的时候才显示 -->
        <p v-if="5 > 2">呵呵呵</p> 
        <p v-if="5 < 2"> 嘿嘿嘿</p>
        <p v-if="isShow">哈哈哈</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                isShow: true
            }
        })
    </script>
</body>
</html>
```

<img src="/static/vue_04.png" style="zoom:90%;" /> 

也可以和 `v-else` 联合使用：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- v-if v-else -->
        <p v-if="randomNum">大大大</p>
        <p v-else>小小小</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                randomNum: Math.random() > 0.5
            }
        })
        console.log(app.randomNum)
    </script>
</body>
</html>
```

`vue` 2.1.0 版本新增了 `v-else-if` 指令：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- v-if和v-else-if和v-else -->
        <p v-if="type === 'A'">A</p>
        <p v-else-if="type === 'B'">B</p>
        <p v-else-if="type === 'C'">C</p>
        <p v-else>Not A/B/C</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                type: "D"
            }
        })
    </script>
</body>
</html>
```

**注意：** `v-else` 元素必须紧跟在带 `v-if` 或者 `v-else-if` 的元素的后面，否则它将不会被识别。类似于 `v-else`，`v-else-if` 也必须紧跟在带 `v-if` 或者 `v-else-if` 的元素之后。

### 3.2 `v-show`

 `v-show` 指令和 `v-if` 指令的作用和用法都大致一样，也是根据表达式的真假值来决定是否渲染内容：

```html	
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- v-show  -->
        <p v-show="isShow">显示</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                isShow: false,
            }
        })
    </script>
</body>
</html>
```

**注意：** `v-show` 不支持 `v-if-else` 和 `v-else` 指令，只能单独使用

### 3.3 `v-if` 和 `v-show` 的区别

`v-if` 和 `v-show` 虽然实现的效果是一样的，但是两者的实现原理却不同：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app">
        <!-- v-if 和 v-show 的区别 -->
        <p v-if="isShow">aaaaa</p>
        <p v-show="isShow">aaaaa</p>

    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                isShow: false,
            }
        })
    </script>
</body>
</html>
```

<img src="/static/vue_05.png" style="zoom:80%;" /> 

观察最后生成的 html 代码，我们可以发现，`v-if` 不显示标签时，是把整个对应的元素从页面上销毁掉从而实现隐藏的效果的，而 `v-show` 则是通过给标签添加 CSS 样式来达到隐藏效果的。

`v-if` 是 “真正” 的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。当然也是**惰性的**，即如果在初始渲染时条件为假，则什么也不做，直到条件第一次变为真时，才会开始渲染条件块。`v-show` 指令不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。