# JavaScript Demo

### **一、定时器**

#### 1.1 简单版

**需求：在输入框中实时显示当前时间**

**实现：**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

    </style>
</head>
<body>
    <input type="text" id="d1">

    <script>
        let inpEle = document.getElementById("d1");
        function showTime() {
            let currentTime = new Date();
            inpEle.value = currentTime.toLocaleString()
        }
        setInterval(showTime, 1000)
    </script>
</body>
</html>
```

<img src="/static/img/JavaScript专题/JS示例1.gif" style="zoom:70%;" /> 

#### 1.2 复杂版

**需求：点击开始按钮，动态展示；点击停止按钮，时间静止**

**实现：**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>

    </style>
</head>
<body>
    <input type="text" id="d1">
    <button id="start">开始</button>
    <button id="end">结束</button>

    <script>
        let inpEle = document.getElementById("d1");
        let startButton = document.getElementById("start");
        let endButton = document.getElementById("end");
        let t = null;  // 定义一个全局变量用于接收定时器

        function showTime() {
            let currentTime = new Date();
            inpEle.value = currentTime.toLocaleString()
        }

        startButton.onclick = function () {
            t = setInterval(showTime, 1000)
        };

        endButton.onclick = function () {
            clearInterval(t)
        }
    </script>
</body>
</html>
```

<img src="/static/img/JavaScript专题/JS示例2.gif" style="zoom:70%;" /> 

看似功能已经实现了，但是实际上存在如下 bug，多次点击开始后，再点击结束就无法让时间停止显示了：

<img src="/static/img/JavaScript专题/JS示例3.gif" style="zoom:70%;" /> 

这是因为每当点击开始按钮，就会创建一个定时器，而变量 `t` 只能接收最后一次的定时器，清除定时器时也就只能清除最后一个了，其余定时器无法清除。所以开始定时器时，要先判断 `t` 是否已被占用，如果 `t` 为 `null` 就可以赋值，否则就无法赋值：

```html
<script>
        let inpEle = document.getElementById("d1");
        let startButton = document.getElementById("start");
        let endButton = document.getElementById("end");
        let t = null;  // 定义一个全局变量用于接收定时器

        function showTime() {
            let currentTime = new Date();
            inpEle.value = currentTime.toLocaleString()
        }

        startButton.onclick = function () {
            if (t===null){
                t = setInterval(showTime, 1000)
            }
        };

        endButton.onclick = function () {
            clearInterval(t)
        }
 </script>
```

<img src="/static/img/JavaScript专题/JS示例4.gif" style="zoom:70%;" /> 

多次点击开始的问题是解决了，但是点完结束后，再次点击开始，发现时间不再动态显示：

  <img src="/static/img/JavaScript专题/JS示例5.gif" style="zoom:70%;" /> 

这是因为在清除定时器后，`t` 的值不为 `null`，需要重新赋值：

```html
<script>
    	// 最终版本
        let inpEle = document.getElementById("d1");
        let startButton = document.getElementById("start");
        let endButton = document.getElementById("end");
        let t = null;  // 定义一个全局变量用于接收定时器

        function showTime() {
            let currentTime = new Date();
            inpEle.value = currentTime.toLocaleString()
        }

        startButton.onclick = function () {
            if (t===null){
                t = setInterval(showTime, 1000)
            }
        };

        endButton.onclick = function () {
            clearInterval(t);
            t = null
        }
</script>
```

