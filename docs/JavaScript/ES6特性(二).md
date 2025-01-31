# ECMAScript6 新特性（二）

## 一、函数

ES6 中给函数增添了许多新的特性，主要有以下几个方面：

### 1.1 参数允许有默认值

在 ES5 语法中，想要给函数形参设置默认值，要如下编写：

```javascript
function add(a, b){
    a = a || 10;
    b = b || 20;
    return a + b;
}

let ret = add()
console.log(ret)  // 30
```

ES6 中可以直接在形参中设置，更加贴合编程规范：

```javascript
function add(a=10, b=20){
    return a + b
}
```

### 1.2 默认值表达式可以多样

形参的默认值除了可以是整形、字符串、数组、对象等类型外，还可以是函数：

```javascript
let getVal = function(val){
    return val + 5
}

let fn = function(a, b=getVal(5)){
    return a + b
}

console.log(fn(20))  // 30
```

### 1.3 剩余参数

剩余参数由三个点号（...）和一个紧跟着的具名参数构成，例如 ...keys，其中 keys 名字可以自定义。使用剩余参数有啥好处呢？我们可以通过以下一个例子来体会一下：

需求如下，传入一个对象，需要根据传入的对象的属性名，获取到该对象的属性值，如：

```javascript
{
    "title":"富婆聊天技巧~",
    "author":"cdc",
    "desc":"嘿嘿嘿~~"
}

// 输入 (book, "title")  输出 {"title":"富婆聊天技巧~"}
// 输入 (book, "title", "desc")  输出 {"title":"富婆聊天技巧~", "desc":"嘿嘿嘿~~"}
```

在 ES5 中我们可以这么实现：

由于只能明确知道用户输入的第一个实参为对象，而后面的实参不管是个数还是参数值都是不确定的，因此我们可以通过函数中的 `arguments`先拿到包含用户输入的所有的参数值，再过滤去除多余的部分，就可以拿到我们想要的属性名了：

```javascript
 let book = {
     "title":"富婆聊天技巧~",
     "author":"cdc",
     "desc":"嘿嘿嘿~~"
 }

 let pick = function(obj){
     console.log(arguments)
 }

 pick(book, "author", "desc")  //
```

<img src="/static/img/JavaScript专题/es6_6.png" style="zoom:70%;" /> 

所以，我们只需要从 `arguments` 数组中索引为 1 的位置开始往后遍历，即可拿到目标属性名：

```javascript
let book = {
    "title":"富婆聊天技巧~",
    "author":"cdc",
    "desc":"嘿嘿嘿~~"
}

let pick = function(obj){
    // console.log(arguments)

    let ret = {}

    // 从索引为1开始获取属性名
    for(let i = 1; i < arguments.length; i ++){
        ret[arguments[i]] = obj[arguments[i]]
    }

    return ret
}

let booData = pick(book, "author", "desc")
console.log(booData)  // {author: 'cdc', desc: '嘿嘿嘿~~'}
```

显然，通过分析过滤 `arguments` 还是比较麻烦，因此 ES6 中新增了剩余运算符，可以弥补 `arguments` 使用的一些不足之处：

```javascript
let book = {
    "title":"富婆聊天技巧~",
    "author":"cdc",
    "desc":"嘿嘿嘿~~"
}

let pick = function(obj, ...keys){
    console.log(keys)  // ['author', 'desc']
}   

let booData = pick(book, "author", "desc")
console.log(booData)
```

通过剩余运算符可以获得所有可变长位置参数，并封装到一个数组中，我们可以直接循环该数组：

```javascript
let book = {
    "title":"富婆聊天技巧~",
    "author":"cdc",
    "desc":"嘿嘿嘿~~"
}

let pick = function(obj, ...keys){
    // console.log(keys)  // ['author', 'desc']
    let ret = {}
    // 从索引为1开始获取属性名
    for (let i = 0; i < keys.length; i++) {
        ret[keys[i]] = obj[keys[i]]
    }

    return ret
}   

let booData = pick(book, "author", "desc")
console.log(booData)
```

### 1.4 拓展运算符

拓展运算符和剩余运算符的写法类似，但是作用却恰恰相反。剩余运算符的作用是将多个独立的值合并到一个数组当中，常常用于函数声明时的形参当中；而拓展运算符是将数组分割，并将各个项作为分离的参数传给函数，常常用于调用函数的实参当中，如：

```javascript
// 示例1-输出最大数
console.log(Math.max(10, 20))  // 20
// Math.max() 方法只能比较两个数之间的大小，如果有多于两个数字时，可以怎么实现呢？
// 方式1 调用 Math.max.apply()
console.log(Math.max.apply(null, [10,20,30,40]))  // 40
// 方式2 使用拓展运算符打散数据
console.log(Math.max(...[10,20,30,40]))  // 40

// 示例2-分散传值
let fn = function (a, b, c) {
    console.log(a, b, c)
}

fn(...[1,2,3])   // 1 2 3 
```

