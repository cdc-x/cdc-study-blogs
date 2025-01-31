# JavaScript BOM

## BOM 简介

BOM（Browser Object Model）是指浏览器对象模型，它使 JavaScript 有能力与浏览器进行 "对话"。

## window 对象

​		window 对象是客户端 JavaScript 最高层对象之一。由于 window 对象是其它大部分对象的共同祖先，在调用 window 对象的方法和属性时，可以省略 window 对象的引用。例如：window.document.write() 可以简写成：document.write()。

​		所有浏览器都支持 window 对象。它表示浏览器窗口。所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。全局变量是 window 对象的属性。全局函数是 window 对象的方法。

### **一、常用方法**

- **window.innerHeight** - 浏览器窗口的内部高度

```javascript
window.innerHeight  // 465
```

- **window.innerWidth** - 浏览器窗口的内部宽度

```javascript
window.innerWidth  // 878
```

- **window.open()** - 打开新窗口

```javascript
// 可以接收三个参数
//     第一个参数为打开的网站url
//     第二个参数可以为空
//     第三个参数可以设置打开窗口的大小以及距离浏览器左上角的位置

window.open("https://www.baidu.com")  // 默认全屏
window.open("https://www.baidu.com","","height=200px,width=200px,top=200px,left=200px")
```

- **window.close()** - 关闭当前窗口

## window 子对象

### **一、navigator**

浏览器对象，通过这个对象可以判定用户所使用的浏览器，包含了浏览器相关信息。

```javascript
navigator.appName　　// Web浏览器全称
navigator.appVersion　　// Web浏览器厂商和版本的详细字符串
navigator.userAgent　　// 客户端绝大部分信息
navigator.platform　　　// 浏览器运行所在的操作系统
```

### **二、screen**

屏幕对象，可以获得屏幕相关的一些属性。

```javascript
screen.availWidth  //可用的屏幕宽度
screen.availHeight  //可用的屏幕高度
```

### **三、history**

浏览历史对象，包含了用户对当前页面的浏览历史，但无法查看具体的地址，可以简单的用来前进或后退一个页面。

```javascript
history.forward()  // 前进一页
history.back()  // 后退一页
```

### **四、location**

window.location 对象用于获得当前页面的地址 (URL)，并把浏览器重定向到新的页面。

```javascript
location.href  // 获取当前页的URL
location.href="https://www.baidu.com" // 跳转到指定页面
location.reload()  // 重新加载当前页面
```

### **五、弹出框**

#### 5.1 alert

警告框，警告框经常用于确保用户可以得到某些信息，当警告框出现后，用户需要点击确定按钮才能继续进行操作。

```javascript
alert("hello")
```

<img src="/static/img/JavaScript专题/BOM1.png" style="zoom:60%;" />  

#### 5.2 confirm

确认框，确认框用于使用户可以验证或者接受某些信息。当确认框出现后，用户需要点击确定或者取消按钮才能继续进行操作。如果用户点击确认，那么返回值为 true。如果用户点击取消，那么返回值为 false。

```javascript
confirm("你确定吗？")
```

<img src="/static/img/JavaScript专题/BOM2.png" style="zoom:60%;" /> 

#### 5.3 prompt

提示框，提示框经常用于提示用户在进入页面前输入某个值。当提示框出现后，用户需要输入某个值，然后点击确认或取消按钮才能继续操纵。如果用户点击确认，那么返回值为输入的值。如果用户点击取消，那么返回值为 null。

````javascript
prompt("你的名字：")
````

<img src="/static/img/JavaScript专题/BOM3.png" style="zoom:60%;" /> 

### **六、计时器相关**

通过使用 JavaScript，我们可以在一定时间间隔之后来执行代码，而不是在函数被调用后立即执行，我们称之为计时事件。计时事件有两种：

#### 6.1 一段时间后触发

```javascript
function func(){
    console.log("hello")
};

setTimeout(func, 3000)  // 3000毫秒后触发函数 func
```

可以使用 `clearTimeout()` 方法来清除定义的计时事件，此时需要先用一个变量来接收计时事件，再将变量传给 `clearTimeout()` 方法：

```javascript
function func(){
    console.log("hello")
};

let t = setTimeout(func, 3000)  // 3000毫秒后触发函数 func
clearTimeout(t)  // 清除计时事件，此时计时事件将不会执行
```

#### 6.2 间隔一段时间后循环触发

`setInterval()` 方法可按照指定的周期（以毫秒计）来调用函数或计算表达式。该方法会不停地调用函数，直到 `clearInterval()` 被调用或窗口被关闭。

```javascript
function func(){
    console.log("hello")
};

setInterval(func, 3000)  // 每隔3000毫秒后触发函数 func
```

同样的，可以通过 `clearInterval()` 方法来结束计时任务：

```javascript
function func(){
    console.log("hello")
};

let t = setInterval(func, 3000)  // 每隔3000毫秒后触发函数 func
clearInterval(t)  // 清除计时事件，计时事件以后将不会执行
```

```javascript
// 综合使用示例：终端会每隔2秒打印一次hello，打印三次后再也不会打印
function func1() {
        console.log("hello")
    }

    function func2() {
        let t = setInterval(func1, 2000);  // 每隔2秒触发一次func1

        function inner() {
            clearInterval(t)  // 清除周期性计时任务
        }
        setTimeout(inner, 6000)  // 6秒后触发inner
    }

    func2()
```

<img src="/static/img/JavaScript专题/BOM.gif" style="zoom:60%;" />   