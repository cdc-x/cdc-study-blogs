# Vue起步

## 一、Vue简介

## 二、Vue实例

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
        <h3>
            <!-- 可以直接引用数据属性中的数据 -->
            {{title}}
        </h3>
    </div>

    <!-- 引入vue.js文件 -->
    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">
        // 创建 vue 实例
        let app = new Vue({
            // 绑定元素
            el: "#app",
            // 所有的数据都要放到数据属性
            // 绑定的区域内可以直接引用数据属性中的数据
            data: {
                title: "这是Vue实例"
            },
            // 所有的逻辑处理方法
            methods: {
                
            }
        })
    </script>
</body>

</html>
```

## 三、插值表达式

用双大括号 (Mustache 语法) 将要渲染的变量括住，Vue 将会获取计算后的值，并以文本的形式将其展示出来。

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
        {{content}}  <!-- 渲染数据属性中的变量 -->
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">

        let app = new Vue({
            el: "#app",
            data:{
                content:"这是一个不愿透露姓名的帅小伙！！！"
            }
        })

    </script>
</body>
</html>
```

除了变量，双括号还可以渲染展示其他数据：

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
        <p>变量：{{content}}</p>
        <p>对象：{{ {"name":"cdc", "age":18} }}</p>
        <p>表达式：{{10+5}}</p>
        <p>三目运算符：{{1>2 ? "真的" : "假的"}}</p>
        <p>函数对象：{{ getNum }}</p>
        <p>函数执行：{{ getNum() }}</p>
        <p>匿名函数：{{ (() => 1+5)() }}</p>
        <p>html代码：{{ '<span>哈哈哈</span>' }}</p>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">

        let app = new Vue({
            el: "#app",
            data:{
                content:"这是一个不愿透露姓名的帅小伙！！！"
            },
            methods:{
                getNum(){
                    return 10
                }
            }
        })

    </script>
</body>
</html>
```

<img src="/static/vue_01.png" style="zoom:50%;" /> 