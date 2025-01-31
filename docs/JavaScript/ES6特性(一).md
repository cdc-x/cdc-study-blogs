# ECMAScript6 新特性（一）

## 一、let 关键字

ES6新增了`let`命令，用来声明变量。它的用法类似于`var`，但是和 `var`又有以下区别：

### 1.1 作用域不同

var 声明的变量可作用于全局

```javascript
<script>
    {
        var a = 10;
    }
        
    console.log(a)  // 变量a正常生效，输出10
</script>
```

 let 声明的变量只能作用于块级

```javascript
<script>
    {
        // var a = 10;
        let a = 10;
    }
        
    console.log(a)
</script>
```

<img src="/static/img/JavaScript专题/es6_1.png" style="zoom: 67%;" /> 

### 1.2 不可重复声明

var 关键字可以重复声明赋值变量，后声明的值会覆盖前一次声明的值

```javascript
<script>
    {
        var a = 10;
		var a = 12;
    }
        
    console.log(a)  // 输出 12
</script>
```

let 关键词不能重复声明同一个变量

```javascript
<script>
	{
		let a = 10;
        let a = 12;
	}
        
    console.log(a)
</script>
```

<img src="/static/img/JavaScript专题/es6_2.png" style="zoom: 67%;" /> 

### 1.3 不存在变量提升

```javascript
<script>
	console.log(e)
    var e = 111
</script>
```

上述代码在变量未声明赋值前就先使用了变量，按照逻辑来说，应该会报错，但实际上可以正常执行，输出结果为  `undefined`。这是因为 var 声明的变量存在变量提升现象，即变量可以在声明之前使用，值为`undefined`，而 let 声明的变量则不存在该现象。

```javascript
<script>
	console.log(e)
    let e = 111
</script>
```

<img src="/static/img/JavaScript专题/es6_3.png" style="zoom: 67%;" /> 

综上所述：let 关键字的作用似乎更加贴合实际逻辑，也弥补了 var 关键字的一些不足。let 声明变量的好处主要有以下几点：

- 避免 for 循环影响

```javascript
<script>
    var a = [];
    for(var i = 0;i < 10;i++){
        a[i] = function(){
            console.log(i)
        }
    }
    
	a[6]()  // 输出10
</script>

// 解析：变量i是var命令声明的，在全局范围内都有效，所以全局只有一个变量i。每一次循环，变量i的值都会发生改变，而循环内被赋给数组a的函数内部的console.log(i)，里面的i指向的就是全局的i。也就是说，所有数组a的成员里面的i，指向的都是同一个i，导致运行时输出的是最后一轮的i的值，也就是 10
```

```javascript
<script>
    var a = [];
    for(let i = 0;i < 10;i++){
        a[i] = function(){
            console.log(i)
        }
    }
    
	a[6]()  // 输出6
</script>

// 解析：let 声明的变量都是局部作用域的，即每个i都是指向不同的变量
```

- 不会污染全局变量

```javascript
console.log(window.RegExp) // 原始的值为 ƒ RegExp() { [native code] }
var RegExp = 10;
console.log(window.RegExp)  // 10，全局变量被污染
```

```javascript
console.log(window.RegExp) // 原始的值为 ƒ RegExp() { [native code] }
let RegExp = 10;
console.log(RegExp)  // ƒ RegExp() { [native code] }
```

## 二、const关键字

`const` 声明一个只读的常量。一旦声明，常量的值就不能改变。

```javascript
const PI = 3.1415
PI  // 3.1415

PI = 3  // TypeError: Assignment to constant variable.
```

`const ` 声明的变量不得改变值，这意味着，`const` 一旦声明变量，就必须立即初始化，不能留到以后赋值

```javascript
const foo  // SyntaxError: Missing initializer in const declaration
```

`const` 的作用域与`let` 命令相同：只在声明所在的块级作用域内有效。

```javascript
{
  const PI = 5;
}

PI  // Uncaught ReferenceError: MAX is not defined
```

同样的，`const` 关键字也可以声明对象，且声明的对象支持属性修改，但是无法创建一个同名的对象来覆盖元对象。

```javascript
const person = {
            "name": "cdc"
        }

console.log(person.name)  // cdc

// 修改属性值
person.name = "TR"
console.log(person.name)  // TR

// 无法通过创建一个同名对象的方式来覆盖原有对象的值
person = {
            "name": "ASC"
        }
// 报错 Uncaught TypeError: Assignment to constant variable.
//      at <anonymous>:1:8
```

## 三、模板字符串

模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

- **作用1：可以定义多行文本**

<img src="/static/img/JavaScript专题/js字符串类型1.png" style="zoom: 80%;" /> 

<img src="/static/img/JavaScript专题/js字符串类型2.png" style="zoom: 80%;" /> 

- 作用2：简化模板拼接

```javascript
// 原始拼接模板渲染写法
var name = "cdc"
var hobby = "sleep"
$('#result').append(
  'My name is <b>' + name + '</b> and my hobby is <em>'+ hobby + '</em>'
);

// 使用模板字符串
var name = "cdc"
var hobby = "sleep"
$('#result').append(
  `My name is <b>${name}</b> and my hobby is <em>${hobby}</em>`
);

```

- 作用3：可以格式化字符串**

<img src="/static/img/JavaScript专题/js字符串类型3.png" style="zoom: 80%;" /> 

**注：对于模板字符串中某些特殊字符，需要进行转义，否则会报错**：

<img src="/static/img/JavaScript专题/es6_4.png" style="zoom: 60%;" /> 

使用 '\\' 进行转义：

  <img src="/static/img/JavaScript专题/es6_5.png" style="zoom: 60%;" /> 