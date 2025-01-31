# Vue指令系统（二）

## 一、属性绑定

有如下一个示例：

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
        <a href="info.url" title="info.title">{{ info.name }}</a>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                info: {
                    "name": "百度",
                    "url": "https://www.baidu.com",
                    "title":"百度一下"
                }
            }
        })
    </script>
</body>
</html>
```

点击页面上的百度链接，发现并没有实现预期的效果，查看一下最终生成的页面代码，发现 `<a></a>` 标签的相关属性并没有被渲染：

<img src="/static/vue_06.png" style="zoom:80%;" /> 

Vue 中，操作元素的 class、内联样式、属性，需要使用 `v-bind` 指令。语法如下：

```html
<!-- 绑定属性 -->
<div v-bind:title=""></div>
<!-- 绑定class -->
<div v-bind:class=""></div>
<!-- 绑定内联样式 -->
<div v-bind:style=""></div>

<!-- v-bind 指令支持简写 -->
<div :title=""></div>
<!-- 绑定class -->
<div :class=""></div>
<!-- 绑定内联样式 -->
<div :style=""></div>
```

### 1.1 绑定元素属性

针对上述的例子，要想实现对应的效果，只需做如下修改：

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
        <a v-bind:href="info.url" v-bind:title="info.title">{{ info.name }}</a>
        
        <!-- 绑定自定义属性 -->
        <a href="" :aaa="info.name"></a>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                info: {
                    "name": "百度",
                    "url": "https://www.baidu.com",
                    "title":"百度一下"
                }
            }
        })
    </script>
</body>
</html>
```

属性可以直接绑定值，也可以绑定变量、表达式、执行函数等内容：

```html
<!-- 直接绑定值 -->
<p :title="'这是一个p标签'"></p>
<!-- 绑定变量 -->
<p :c1ass="c1assName" :title="tit1e">危险勿触</p>
<!-- 绑定表达式 -->
<button :disab1ed="10 + 10 === 20"> 点击有奖</button>
```

### 1.2 类名绑定

由于类名 class 和样式 style 在节点属性中是两个比较奇怪的存在，虽然他们可接收的类型都是字符串，但类名实际上是由数组拼接而成，而样式则是由对象键值对拼接而成的，所以 Vue 在绑定类名和样式时也采用不一样的机制：

可以通过字符串、数组和对象三种方式为节点动态绑定类名属性：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style>
        .size-20 {
            font-size: 20px;
        }

        .color-red {
            color: red;
        }

        .style-italic {
            font-style: italic;
        }
    </style>

</head>

<body>
    <div id="app">
        <!-- 绑定类名 -->
        <p :class="classStr">通过字符串绑定类</p>
        <p :class="classArr">通过数组绑定类</p>
        <!-- 可以直接绑定数组 -->
        <!-- <p :class="['size-20', 'color-red', 'style-italic']">通过数组绑定类</p> -->
        <p :class="classObj1">通过对象绑定类</p>
        <!-- 可以直接绑定对象 -->
        <!-- <p :class="{'size-20': isSize,'color-red': true,'style-italic': true}">通过对象绑定类</p> -->
        <p :class="classObj2">通过对象未绑定类</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                classStr: "size-20 color-red style-italic",
                classArr: ["size-20", "color-red", "style-italic"],
                classObj1: {
                    "size-20": true,
                    "color-red": true,
                    "style-italic": true
                },
                classObj2: {
                    "size-20": false,
                    "color-red": 0,
                    "style-italic": ""
                }
            }
        })
    </script>
</body>

</html>
```

<img src="/static/vue_07.png" style="zoom:80%;" /> 

**注意：**通过对象绑定 class 时，键值对的值必须为真。

### 1.3 绑定样式

绑定样式的方法和绑定类名的方法类似，但是只能通过键值对形式进行绑定，不能通过数组形式绑定：

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
        <!-- 绑定样式 -->
        <p :style="styleStr">通过字符串绑定样式</p>
        <p :style="styleObj">通过对象形式绑定样式</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                styleStr: "color:red; font-size:20px; font-style: italic;",
                styleObj: {
                    "color": "red",
                    "font-size": "20px",
                    "font-style": "italic"
                }
            }
        })
    </script>
</body>

</html>
```

<img src="/static/vue_08.png" style="zoom:80%;" /> 

对于已有的类名和样式，绑定新的类型和样式时，会在原有的基础上进行新增或覆盖(相同的类名或样式)：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style>
        .box{
            height: 200px;
            width: 200px;
        }

        .box-color{
            background: red;
        }
    </style>
</head>

<body>
    <div id="app">
        <!-- 已有类名和样式上新增 -->
        <div class="box" style="color: white;" :class="{'box-color':addClass}" :style="{'font-size':'20px'}">vegetable dog</div>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                addClass: true,
            }
        })
    </script>
</body>

</html>
```

<img src="/static/vue_09.png" style="zoom:80%;" /> 

## 二、事件绑定

### 2.1 绑定事件到DOM节点

Vue 中通过 `v-on` 指令监听 DOM 事件，并在触发时运行一些 JavaScript 代码：

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
        <p>{{num}}</p>
        <button v-on:click="num++">点我</button>
    </div>
    
    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                num: 0
            }
        })
    </script>
</body>
</html>
```

<img src="/static/vue_01.gif" style="zoom:80%;" /> 

对于复杂的逻辑操作，我们可以定义一个方法，再通过指令将函数绑定到 DOM 节点上，需要注意的是，方法必须写在 `methods` 下：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>

    <style>
        .box {
            height: 200px;
            width: 200px;
            background-color: red;
        }

        .box2 {
            background-color: green;
        }
    </style>
</head>

<body>
    <div id="app">
        <div class="box" :class="{box2:isGreen}"></div>
        <!-- changeColor是在下面定义的方法 -->
        <button v-on:click='changeColor'>切换颜色</button>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                isGreen: false
            },
            methods: {
                changeColor(event){
                    this.isGreen = !this.isGreen
                    
                    // event 是原生 DOM 事件，定义方法时也可以不写
                    console.log(event)
                }
            }
        })
	
    // 也可以用 JavaScript 直接调用方法
	app.changeColor()
        
    </script>
</body>

</html>
```

<img src="/static/vue_02.gif" style="zoom:80%;" /> 

绑定的方法也可以传参，如果需要在内联语句处理器中访问原始的 DOM 事件，可以用特殊变量 `$event` 把它传入方法：

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
        <button @click="say('Hi', $event)">say</button>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                num: 0,
                isGreen: false
            },
            methods: {
                say(message, event){
                    console.log(message)  // Hi
                    console.log(event)  // PointerEvent {isTrusted: true, …}
                }
            }
        })
    </script>
</body>

</html>
```

**补充：**

1）`v-on` 指令支持简写，语法如下

```html
<button v-on:click='changeColor'>切换颜色</button>
等价于
<button @click='changeColor'>切换颜色</button>
```

2）在绑定的事件中，可以通过 `this`  指向数据属性中的数据，其原因如下：

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
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                num: 0,
                isGreen: false
            }
            
        // 查看实例化的Vue对象到底是个啥
        console.log(app)
    </script>
</body>

</html>
```

<img src="/static/vue_10.png" style="zoom:80%;" /> 

可以看到 Vue 对象中封装了许多东西，包括我们在数据属性中定义的值，我们可以直接通过实例将数据取出：

```html
 <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                num: 0,
                isGreen: false
            }
            
        // 查看实例化的Vue对象到底是个啥
        console.log(app)
       	
        // 先获取数据属性对象，再通过对象取值
        console.log(app.$data)  // {__ob__: Observer}
        console.log(app.$data.num)  // 0
        console.log(app.$data.isGreen)  // false
        
        // 也可以通过实例直接取
        console.log(app.num)  // 0
     	console.log(app.isGreen)  // false
</script>
```

所以这就是为什么我们可以直接在 Vue 绑定的区域内 (即\<div id="app"\>\</div\> 包含的区域) 使用数据属性中的值的原因，那么我们再来看一下事件中的 `this` 是个什么鬼：

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
        <button v-on:click="clickHandle">点我</button>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
                num: 0,
                isGreen: false
            },
            methods: {
                clickHandle(){
                    
                    // 点击按钮，输出this，看看this是个什么鬼
                    console.log(this)
                }
            }
        })
    </script>
</body>

</html>
 ```

<img src="/static/vue_10.png" style="zoom:80%;" />

`this` 指向的是当前调用的对象，而在整个区域内，只有一个对象 `app`，即 Vue 的实例化对象，所以 `this` 的作用可以和 `app` 等价。

### 2.2 事件修饰符

在事件处理中，常常需要去规避一些 DOM 操作带来的影响，如事件冒泡、默认执行等行为，以事件冒泡为例：

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
        <!-- 事件冒泡 -->
        <div @click="sayHi('我是第一层')">第一层的div标签
            <p @click="sayHi('我是第二层')">
                第二层的p标签
                <span @click="sayHi('我是第三层')">
                    第三层的span标签
                </span>
            </p>
        </div>

    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            methods: {
                sayHi(message){
                    alert(message)
                }
            }
        })

    </script>
</body>

</html>
```

<img src="/static/vue_03.gif" style="zoom:80%;" /> 

上述三个标签嵌套，且分别绑定了一个点击事件。当点击最里层的 `span` 标签时，理论上只会触发 `span` 节点上绑定的事件，但实际上连父级和祖先级的标签对应的事件也被触发，我们可以通过调用 DOM 原生事件本身携带的一些方法去处理，如 `event.preventDefault()`(用于阻止默认行为触发) 或 `event.stopPropagation()` (用于阻止事件冒泡) 等：

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
        <!-- 事件冒泡处理 -->
        <div @click="sayHi('我是第一层', $event)">第一层的div标签
            <p @click="sayHi('我是第二层', $event)">
                第二层的p标签
                <span @click="sayHi('我是第三层', $event)">
                    第三层的span标签
                </span>
            </p>
        </div>

    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        let app = new Vue({
            el: "#app",
            data: {
        
            },
            methods: {
                sayHi(message, event){
                    alert(message)
                    // 阻止事件冒泡
                    event.stopPropagation()
                }
            }
        })

    </script>
</body>
</html>
```

针对上述情形，Vue 提供更为简介的操作，为 `v-on` 提供了**事件修饰符**来处理 DOM 细节。

```html
<!-- 阻止事件冒泡 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 也可以只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>

<!-- 点击事件将只会触发一次 -->
<a v-on:click.once="doThis"></a>
```

**注意：**使用修饰符时，顺序很重要，相应的代码会以对应的顺序产生。例如，用 `v-on:click.prevent.self` 会阻止**所有的点击**，而 `v-on:click.self.prevent` 只会阻止**对元素自身的点击**。

### 2.3 按键修饰符

在监听键盘事件时，我们经常需要检查详细的按键。Vue 允许为 `v-on` 在监听键盘事件时添加按键修饰符：

```html
<!-- 按下 enter 键才会触发对应的方法 -->
<input v-on:keyup.enter="submit">
```

按键修饰符也支持按键码：

```html
<input v-on:keyup.13="submit">
```

但由于按键数量过多，且按键码的事件很多已经被废弃了并可能与新版本的浏览器有兼容问题，因此一般推荐使用按键别名，Vue 提供了绝大多数常用的按键码的别名：

```html
.enter
.tab
.delete
.esc
.space
.up
.down
.left
.right
```

此外，Vue 针对 `v-on` 指令还提供了系统修饰键、鼠标修饰符等等，可以参考官方文档：[事件处理 — Vue.js (vuejs.org)](https://cn.vuejs.org/v2/guide/events.html)

