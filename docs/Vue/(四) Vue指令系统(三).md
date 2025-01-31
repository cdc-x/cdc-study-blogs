# 指令系统（三）

## 一、列表渲染

### 1.1 渲染列表

`v-for` 用于实现列表渲染，可以使用 `item in item` 或者 `item of item` 的语法：

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

        <h3>晚上吃点啥？</h3>
        <table cellspacing=0 border=1>
            <tr>
                <th>序号</th>
                <th>菜名</th>
                <th>价格</th>
            </tr>

            <tr v-for="item in memus">
                <td>{{item.id}}</td>
                <td>{{item.name}}</td>
                <td>{{item.price}}</td>
            </tr>


        </table>

    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">

        let app = new Vue({
            el: "#app",
            data: {
                memus: [
                    { id: 1, name: "烤韭菜", price: 2 },
                    { id: 2, name: "烤生蚝", price: 10 },
                    { id: 3, name: "大腰子", price: 9 },
                ]
            }
        })

    </script>
</body>

</html>
```

也可以传入两个参数：

```html
<!-- item为数据内容，index为索引 -->
<div v-for="(item, index) in memus">
    索引：{{index}}---序号：{{item.id}}---菜名：{{item.name}}---价格：{{item.price}}
</div>
```

### 1.2 渲染对象

除了用于渲染列表，`v-for` 也可用于渲染对象：

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
        <!-- 渲染对象 -->
        <!-- 只接受一个值，取到的是对象中每一项的值 -->
        <div v-for="value in person">
            {{value}}
        </div>

        <!-- 接受两个值，第一个值为对象中每一项的值，第二个值为每一项的键名 -->
        <div v-for="(value, key) in person">
            {{key}} --- {{value}}
        </div>

        <!-- 接受三个值，第一个值为对象中每一项的值，第二个值为每一项的键名，第三个值为索引 -->
        <div v-for="(value, key, index) in person">
            {{index}} --- {{key}} --- {{value}}
        </div>
    </div>

    <script type="text/javascript" src="../vue.js"></script>
    <script type="text/javascript">

        let app = new Vue({
            el: "#app",
            data: {
                person: {
                    name: "cdc",
                    gender: "male",
                    age: 18
                }
            }
        })
    </script>
</body>

</html>
```

### 1.3 状态维护

当 `Vue` 使用 `v-for` 渲染的元素列表时，它默认使用 “就地更新” 的策略，即元素列表发生改变时，不会重载整个列表，而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。

为了给 `Vue` 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，需要为每项提供一个唯一 `key`，所以一般都会这么写：

```html
<!-- 绑定key -->
<!-- key必须唯一 -->
<div v-for="(item, index) in memus" :key="index"></div>
```

## 二、双向数据绑定

`v-model` 指令只能运用于在表单类元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素，它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

