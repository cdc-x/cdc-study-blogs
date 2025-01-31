# JavaScript

## JavaScript概述

### **一、简介**

​		JavaScript（JS）是由 Netscape 公司出品的一款脚本语言，并与1996年11月决定将 JavaScript 提交给国际标准化组织 ECMA，希望这门语言能够成为国际标准。1997年，ECMA 发布262号标准文件（ECMA-262）的第一版，规定了浏览器脚本语言的标准，并将这种语言称为 ECMAScript，这个版本就是1.0版。ECMAScript 和 JavaScript的关系是，前者是后者的规格，后者是前者的一种实现。

### **二、ECMAScript发展历史**

| 年份 | 名称           | 描述                                              |
| ---- | -------------- | ------------------------------------------------- |
| 1997 | ECMAScript 1   | 第一个版本                                        |
| 1998 | ECMAScript 2   | 版本变更                                          |
| 1999 | ECMAScript 3   | 添加正则表达式、添加try/catch                     |
|      | ECMAScript 4   | 没有发布                                          |
| 2009 | ECMAScript 5   | 添加 "strict mode" 严格模式添加 JSON 支持         |
| 2011 | ECMAScript 5.1 | 版本变更                                          |
| 2015 | ECMAScript 6   | 添加类和模块                                      |
| 2016 | ECMAScript 7   | 增加指数运算符（**）增加 Array.prototype.includes |

​		我们常说的 ES6 就是指ECMAScript 6。尽管 ECMAScript 是一个重要的标准，但它并不是 JavaScript 唯一的部分，当然，也不是唯一被标准化的部分。

### **三、JavaScript的组成**

- 核心（ECMAScript） 
- 文档对象模型（DOM） Document object model (整合js，css，html)
- 浏览器对象模型（BOM） Broswer object model（整合js和浏览器）

### **四、特点**

- JavaScript 是脚本语言
- JavaScript 是一种轻量级的编程语言。
- JavaScript 是可插入 HTML 页面的编程代码。
- JavaScript 插入 HTML 页面后，可由所有的现代浏览器执行。

### **五、引入方式**

- 直接在 HTML 文档中通过 script 标签包裹 js 的代码

```html
<script>
  // 在这里写你的JS代码，一般推荐写在body标签的最底部
</script>
```

- 引入 js 文件

```html
// 在head里面直接引入
<script src="myscript.js"></script>
```

注：由于浏览器加载HTML文档的时候，是按从上往下顺序加载，如果将 js 代码写在最前面，有可能会因为 js 操作的元素尚未加载出来而导致 js 代码失效，因此常常将 js 代码写在 body 标签的最末端。

### **六、语法规范**

- 注释：// 单行注释    		/*  */ 多行注释
- 语句结束要以 " ;" 结尾

## 变量和常量

### **一、变量的使用**

- 可以使用_，数字，字母，$组成，不能以数字开头；
- 首次声明一个变量，要使用 **var** 关键字进行声明；
- 变量名是区分大小写的，如 a 和 A 是两个不同的变量；
- 推荐使用驼峰式命名规则；
- 保留字不能用做变量名。
- 变量在声明和赋值时，可以动态的规定数据类型

```javascript
var name = "Cdc";  // name为字符串类型
var age = 18;	// age为数值类型
```

```javascript
// JS的保留字
abstract		boolean			byte		char		class		const		debugger
double			enum			export		extends		final		float		goto
implements		import			int			interface	 long		native		package
private			protected		public		short		static		super		synchronized
throws			transient		volatile
```

### **二、let 和 var 关键字**

在 ES6 中，新增使用 `let`  关键字声明变量的方式，`let` 和 `var` 的区别在于：

- var 声明的变量作用于全局
- let 声明的变量作用于局部

```javascript
var n = 10;
for(var n=0;n<5;n++){
    console.log(n);
}；
console.log(n);
```

<img src="/static/img/JavaScript专题/js常量和变量1.png" style="zoom: 80%;" /> 

```javascript
var n = 10;
for(let n=0;n<5;n++){
    console.log(n)
};
console.log(n);
```

 <img src="/static/img/JavaScript专题/js常量和变量2.png" style="zoom: 80%;" /> 

​		从上述两个循环输出的例子对比可以看出，使用 `let` 关键字在 for 循环内部声明使用的局部变量 n 并不会影响全局的变量 n，而使用 `var` 在循环内部声明使用的局部变量 n 改变了全局变量 n 的值。

### **三、常量**

使用 **const**  关键字来声明常量。一旦声明，其值就不能改变。

```javascript
const PI = 3.1415;
PI // 3.1415
```

<img src="/static/img/JavaScript专题/js常量和变量3.png" style="zoom: 80%;" /> 

### **四、JavaScript 保留字列表**

```javascript
abstract        boolean        byte        char           class        const
debugger        double         enum        export         extends      final
float           goto           implements  import         int          interface
long            native         package     private        protected    public
short           static         super       synchronized   throws       transient
volatile
```

## 基本数据类型

JavaScript 和 Python 一样，拥有动态类型：

```javascript
var x;  // 此时x是undefined
var x = 1;  // 此时x是数字
var x = "cdc"  // 此时x是字符串 
```

在 JavaScript 中可以通过 `typeof` 方法查看变量对应的类型，`typeof` 是一个一元运算符，就像加减乘除运算符一样，不是一个函数，也不是一个语句。

```javascript
var name = "cdc";
var age = 18;
var isMale = true;
console.log(typeof name);  // string
console.log(typeof agg);  // number
console.log(typeof isMale);  // boolean
```

对变量或值调用 `typeof`  运算符将返回下列值之一：

- undefined - 如果变量是 Undefined 类型的
- boolean - 如果变量是 Boolean 类型的
- number - 如果变量是 Number 类型的
- string - 如果变量是 String 类型的
- object - 如果变量是一种引用类型或 Null 类型的

### **一、数字类型（number）**

#### 1.1 数字类型变量声明

- JavaScript 不区分整型和浮点型，就只有一种数字类型。

```javascript
var a = 12.34;
var b = 20;

// JS中还支持科学计数法
var c = 123e5;  // 12300000  
var d = 123e-5;  // 0.00123
```

<img src="/static/img/JavaScript专题/js数字类型1.png" style="zoom: 80%;" /> 

#### 1.2 数字类型转换：

- `parseInt`：转换成整数

- `parseFloat`：转换成小数

```javascript
// 可以将字符串转换成整数
parseInt("123123")  // 返回123123

// 可以将小数转换成整数，只取整数部分
parseInt(123.123)  // 返回123123

// 如果字符串开头包含数字，也可以转换成整数，只从开头取到第一个不为数字的字符
parseInt("12dasdasd123456")  // 返回12

// 可以将字符串转换成小数
parseFloat("123.456")  // 返回123.456
```

#### 1.3 特殊数字类型

- NaN 是一种特殊的数字类型，它表示该变量不是一个数字。

```javascript
parseInt("dasdasd123456")  // 返回 NaN，表示不是一个数字，所以没有办法转换
```

### **二、字符串（String）**

#### 2.1 字符串类型变量声明

```javascript
var a = "Hello"
typeof(a);  // 返回string
```

#### 2.2 模板字符串

- **作用1：可以定义多行文本**

<img src="/static/img/JavaScript专题/js字符串类型1.png" style="zoom: 80%;" /> 

<img src="/static/img/JavaScript专题/js字符串类型2.png" style="zoom: 80%;" /> 



- **作用2：可以格式化字符串**

<img src="/static/img/JavaScript专题/js字符串类型3.png" style="zoom: 80%;" /> 



**注意：**`${变量名}` 会直接去找大括号中变量名对应的值，如果找不到直接报错：

<img src="/static/img/JavaScript专题/js字符串类型4.png" style="zoom: 80%;" /> 

#### 2.3 字符串常用方法

- **length**    获取字符串长度 

```javascript
var s1 = "hello,world!!"
console.log(s1.length)  // 13  
```

- **trim**    去除字符串两边的空格，结果返回一个新的字符串，并不会对原字符串产生影响

```javascript
let s1 = "  hello world  ";
console.log(s1);  // "  hello world  "
let s2 = s1.trim();
console.log(s1);  // "  hello world  "
console.log(s2);  // "hello world"
```

- **trimLeft**    去除字符串左边的空格，结果返回一个新的字符串，并不会对原字符串产生影响

```javascript
let s1 = "  hello world  ";
console.log(s1);  // "  hello world  "
let s2 = s1.trimLeft();
console.log(s1);  // "  hello world  "
console.log(s2);  // "hello world  "
```

- **trimRight**    去除字符串右边的空格，结果返回一个新的字符串，并不会对原字符串产生影响

```javascript
let s1 = "  hello world  ";
console.log(s1);  // "  hello world  "
let s2 = s1.trimRight();
console.log(s1);  // "  hello world  "
console.log(s2);  // "  hello world"
```

- **charAt**     返回第n个字符，n为索引值，取值从0开始

```javascript
let s1 = "Hello World !";
console.log(s1.charAt(2));  // "l"
```

- **concat**    字符串拼接，用的比较少，推荐使用 "+" 拼接，结果返回一个新的字符串，并不会对原字符串产生影响

```javascript
let s1 = "Hello World !";
let s2 = s1.concat("cdc");
console.log(s1);  // Hello World !
console.log(s2);  // Hello World !cdc
```

**注意：**在 python 中不管是列表拼接成字符串还是字符串之间拼接，都必须保证拼接的对象都是字符串类型，但是由于 js 是弱类型语言，在字符串拼接时，会把其他类型数据先转换成字符串，再进行拼接，因此不同类型的数据之间可以之间拼接。

```javascript
let name = "cdc";
console.log(name.concat(true, 123456));  // "cdctrue123456"
```

- **indexOf**    获取子列的位置，返回值为子列首字母在原字符串中的索引位置，如果找不到，返回 -1

```javascript
let s1 = "Hello World !";
console.log(s1.indexOf("lo"));  // 3

// indexOf 方法还可以接收两个参数，第二个参数表示查找的起始位置
console.log(s1.indexOf("o", 2));  // 4
console.log(s1.indexOf("o", 10));  // -1
```

- **toLowerCase**    将字符串变成小写，结果返回一个新的字符串，并不会对原字符串产生影响

```javascript
let s1 = "Hello World !";
let s3 = s1.toLowerCase();
console.log(s3);  // hello world !
```

- **toUpperCase**    将字符串变成大写，结果返回一个新的字符串，并不会对原字符串产生影响

```javascript
let s1 = "Hello World !";
let s4 = s1.toUpperCase();
console.log(s4);  // HELLO WORLD !
```

- **split**    根据指定元素进行切分，结果返回一个数组对象，并不会对原字符串产生影响

```javascript
let s1 = "Hello World !";
console.log(s1.split("l"));  // ["He", "", "o Wor", "d !"]

// split 方法可以接收两个参数，第二个数值参数n表示只显示切割结果的前n个，如果n大于数组元素个数，则显示全部，不会报错
console.log(s1.split("l"， 2));  // ["He", ""]
```

- **substring 和 slice**    对字符串进行切片

```javascript
let s1 = "Hello World !";
console.log(s1.substring(0,5));  // Hello
console.log(s1.slice(0,5));  // Hello
```

看到这里可能有的金针菇就要问了，这两个方法的作用貌似一样啊？其实两者还是有区别的：

**区别：** 

- **相同点：**
  如果 start 等于 end，返回空字符串
  如果 stop 参数省略，则取到字符串末
  如果某个参数超过 string 的长度，这个参数会被替换为 string 的长度
- **substirng 的特点：**会对参数进行两步判断：
  1. 如果 start > stop ，start和stop将被交换；
  2. 如果参数是负数或者不是数字，将会被0替换
- **slice 的特点：** 
  如果 start > stop 不会交换两者，此时取到的是空字符串
  如果 start 小于 0，则切割从字符串末尾往前数的第 abs(start) 个的字符开始(包括该位置的字符)
  如果 stop 小于 0，则切割在从字符串末尾往前数的第 abs(stop) 个字符结束(不包含该位置字符)

​                             <img src="/static/img/JavaScript专题/js表情1.png" style="zoom:80%;" />      

总结下来就是：`slice` 方法支持索引传负数值，`substirng` 方法不支持，所以推荐使用 `slice` 方法

```javascript
let s1 = "Hello World !";
console.log(s1.slice(0,-3));  // Hello Worl
console.log(s1.substring(0,-3));  // 返回空值
```

### **三、布尔值（Boolean）**

```javascript
// 值都为小写
// 空字符串、0、null、undefined、NaN 的布尔值都是false
var a = true;
var b = false;
```

### **四、null 和 undefined**

- null 表示值是空，一般在需要指定或清空一个变量时才会使用，如：

```javascript
let name = "cdc";
// 清空变量
let name = null;
```

- undefined 表示当声明一个变量但未初始化时，该变量的默认值是 undefined。还有函数无明确的返回值时，返回的也是 undefined。

总结来说，null 表示变量的值是空，undefined 则表示只声明了变量，但还没有赋值。再举一个你身上的例子来带你形象理解下，钱包里没有钱，但是钱包是存在的，此时就是 null；等到你把钱包都卖了，啥也没了，就是 undefined。

​                                    <img src="/static/img/JavaScript专题/js表情2.png" style="zoom:100%;" /> 

## 运算符

### **一、算术运算符**

```javascript
// + - * / % ++ --
// 需要特别关注自增++ 和自减—— 的使用

let a1 = 10;
let s1 = a1++;  // 先赋值再自增
let s2 = ++a1;  // 先自增再赋值
console.log(s1);  // 10
console.log(s2);  // 12
```

- 第一步，定义了一个变量 `a1`，初始值为 `10`；
- 第二步，`++` 运算在后面，所以执行的顺序是先把 `a1` 的值赋值给 `s1`，`a1` 再自加 `1`，所以这行执行以后， `s1` 的值为 `10`，`a1` 的值为 `11`；
- 第三步， `++` 运算在前面，所以执行的顺序是 `a1` 先自加 `1`，再把值赋值给 `s2`，所以这行执行以后，`a1` 的值为 `12`，`s2` 的值为 `12`；

### **二、比较运算符**

```javascript
// 主要有 >  >=  <  <=  ==  ===  !=  !==  返回值为布尔值
// 需要特别关注 弱等于== 和 强等于===

1 == "1"  // 弱等于，内部转换成相同的数据类型后再进行比较，结果为true
1 === "1"  // 强等于，内部不做类型转换，结果为false
1 != "1"  // false
1 !== "1"  // true
```

### **三、逻辑运算符**

 ```javascript
// 主要有 &&(与)  ||(或)  !(非)  遵循短路原则，返回值为数据
5 && "5"  // 返回"5"
0 || 1  // 返回1
 ```

### **四、赋值运算符**

```javascript
=   +=   -=   *=   /=
```

## 流程控制

### **一、条件判断**

#### 1.1 if-else

```javascript
let age = 30;
if(age<18){
    console.log("培养一下")
}else{
    console.log("考虑一下")
}
// "考虑一下"
```

#### 1.2 if-else if-else

```javascript
let age = 50;
if(age<18){
    console.log("培养一下")
}else if(age<30){
    console.log("考虑一下")
}else{
    console.log("我不想努力了")
}
// "我不想努力了"
```

#### 1.3 switch

```javascript
// 提前列举好可能出现的条件和解决方式，按顺序匹配，找到对应的条件后执行对应的方法
let num = 1;
switch (num) {
    case 0:
        console.log("aaaa");
        break;
    case 1:
        console.log("bbbb");
        break;
    case 2:
        console.log("cccc");
        break;
    case 3:
        console.log("dddd");
        break;
    default:
        console.log("所有条件都没匹配上就执行这一行");
        break;
}
// "bbbb"
```

**注意：**对于 `switch` 下所有的条件，都要加上 `break` ，否则会产生 `case穿透` 现象，如：

<img src="/static/img/JavaScript专题/条件判断1.png" style="zoom:60%;" /> 

当匹配到 case1 并执行后，程序并没有退出，而是继续向下执行了所有的代码。

### **二、循环**

#### 2.1 for循环

```javascript
for (let i=0;i<10;i++ ){
        console.log(i)
    }  // 输出 0-9

// 使用for循环打印九九乘法表
for (let i = 1; i <= 9; i++) {
        let res = "";
        for (let j = 1; j <= i; j++) {
            res += `${j} x ${i} = ${i*j}\t`
        }
        console.log(res)
    }
```

使用 `for` 来变量数组对象：

```javascript
let s = [111, 222, 333, 444, 555];
for (let i = 0; i < s.length; i++) {
    console.log(s[i])
}
```

#### 2.2 while 循环

```javascript
let num = 1;
while (num < 10) {
    console.log(num);
    num ++;
}
```

### **三、三元运算**

```javascript
// 语法格式：let res = a>b?c:d
// 如果判断条件成立，就返回问号后面的值，否则返回冒号后面的值

// 示例1：
let s = 1>2?666:444
console.log(s) // 444
// 解析：1>2不成立，所以返回冒号后面的值

// 示例2：嵌套判断
let s = 1>2?666:(5>8?333:444)
console.log(ss) // 444
// 解析：1>2不成立，所以返回冒号后面的值 (5>8?333:444),返回的是一个三元运算，再继续分析，5>8不成立，所以返回冒号后面的值，即444
```

## 函数

在 Javascript 中使用关键字 `function` 来定义一个函数，语法格式为：

```javascript
// 定义函数
function 函数名 (形参1，形参2,...) {函数体代码}

// 执行函数
函数名(形参1，形参2,...)
```

### **一、函数分类**

#### 1.1 无参函数

```javascript
function func() {
        console.log("hello world !")
    }
func()  // hello world !
```

#### 1.2 有参函数

```javascript
function func(a, b) {
        res = a + b;
        console.log(res);
    }
func(1,2)  // 3
```

如果我们传的参数个数没对上会咋样呢？

```javascript
// 先定义一个函数，将接收到的形参进行输出
function func(a, b) {
        console.log(a, b)
    }

// 传入的参数多于形参的个数
func(1,2,3,4,5,6)  // 1 2 只取对应的前两个值

// 传入的参数少于形参的个数
func(1)  // 1 undefined  缺少的形参默认赋值为 undefined
```

所以无论多传还是少传，Javascript 都不会报错，用户体验感 "满分" ！！！既然 Javascript 的函数机制这么友好，那么怎么样才能拿到调用函数时真正传入的参数呢？Javascript 有一个内置的 `arguments` 关键字，通过它可以获取到函数接收到的所有参数：

```javascript
function func(a, b) {
     console.log(a, b)
     console.log(arguments)
}
func(1,2,3,4,5,6);
```

<img src="/static/img/JavaScript专题/函数1.png" style="zoom:60%;" /> 

#### 1.3 带返回值的函数

Javascript 中函数使用 `return` 返回处理的结果：

```javascript
function add(a, b) {
    let res = a + b;
    return res
}

let addRes = add(1,2);
console.log(addRes);  // 3
```

Javascript 函数无法返回多个值，如果返回多个，只能取到最后一个：

```javascript
function func(){
    return 111, 222, 333, 666
}

let res = func();
console.log(res);  // 666
```

如果想返回多个值需要自己手动给他们包一个数组或对象中：

```javascript
function func(){
    return [111, 222, 333, 666]
}
```

#### 1.4 匿名函数

```javascript
let sum = function(a, b) {
    let res = a + b;
    return res
}

// 执行函数
sum(1, 2)  
```

#### 1.5 立即执行函数

顾名思义，立即执行函数在定义完后立即执行，不需要函数调用

```javascript
(function(a,b){
    console.log(a+b);
})(1,2);  // 直接打印 3

// 有返回值
let res = (function(a,b){
    return a+b;
})(1,2);
console.log(res)  // 函数在定义完后直接执行，直接拿到返回值
```

#### 1.6 箭头函数

 ES6 中允许使用 "箭头"（=>）定义函数。

```javascript
var f = v => v;   // “=>”符号左边是形参，右边是返回值
// 等同于
var f = function(v){
  return v;
}
```

如果箭头函数不需要参数或需要多个参数，用圆括号代表参数部分：

```javascript
var f = () => 5;
// 等同于
var f = function(){return 5};

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2){
  return num1 + num2;  //这里的return只能返回一个值，如果想返回多个值需要自己手动给他们包一个数组或对象中
}
```

### **二、局部变量和全局变量**

#### 2.1 局部变量

​		在 Javascript 函数内部声明的变量是局部变量，所以只能在函数内部访问它（该变量的作用域是函数内部）。只要函数运行完毕，本地变量就会被删除。

#### 2.2 全局变量

​		在函数外声明的变量是全局变量，网页上的所有脚本和函数都能访问它。

#### 2.3 变量生存周期

- Javascript 变量的生命期是从它们被声明的时间开始的；
- 局部变量会在函数运行以后被删除；
- 全局变量会在页面关闭后被删除。

### **三、作用域**

代码在执行时，首先在函数内部查找变量，找不到则到外层函数查找，逐步找到最外层。例如：

```javascript
// 示例1
var city = "BeiJing";
function f() {
  var city = "ShangHai";
  function inner(){
    var city = "NanJing";
    console.log(city);
  }
  inner();
}

f();  // NanJing
```

```javascript
// 示例2
var city = "BeiJing";
function Bar() {
  console.log(city);
}
function f() {
  var city = "ShangHai";
  return Bar;
}
var ret = f();  // 相当于 Bar()
ret();  // BeiJing
```

```javascript
// 示例3，闭包
var city = "BeiJing";
function f(){
    var city = "ShangHai";
    function inner(){
        console.log(city);
    }
    return inner;
}
var ret = f();  // 相当于 inner()
ret();  //  ShangHai
```

### **四、词法分析**

Javascript 中函数调用的一瞬间，其实经过了两个步骤：

- 词法分析
- 代码执行

词法分析又遵循以下步骤：

- 当函数调用的前一瞬间，会先形成一个激活对象：Avtive Object（AO），并会分析以下3个方面：
  1. 函数有无参数，如果有，则将此参数赋值给AO，且值为undefined。如果没有，则不做任何操作。
  2. 函数的局部变量，如果AO上有同名的值，则不做任何操作。如果没有，则将此变量赋值给AO，并且值为undefined。
  3. 函数内部声明的函数，如果AO上有同名的值，则将AO上的对象覆盖。如果没有，则不做任何操作。

**函数内部无论是使用参数还是使用局部变量都到AO上找。** 

 ```javascript
// 示例1
var age = 18;
function foo(){
  console.log(age);  // undefined
  var age = 22;
  console.log(age);  // 22
}
foo();  
 ```

​		首先对函数进行词法分析。该函数是无参函数，所以词法分析的第一步不用考虑；接着函数内部定义了一个局部变量 `age`，此时 AO 上是没有这个值的，所以要将变量 `age` 赋值给 AO 且值为 `undefined`，即 `AO.age = undefined`；函数内部没有再声明函数，所以词法分析第三步不用考虑。

​		词法分析结束后，开始执行函数。执行第一行代码 `console.log(age)` 时，AO.age 的值为 undefined，所以此时输出 `undefined`；第二行代码 `var age = 22` 给变量 age 进行了赋值操作，此时 AO 上对应的变量也发生了改变，即 `AO.age = 22`；执行第三行代码时，输出 22 。

```javascript
// 示例2
var age = 18;
function foo(age){
  console.log(age);  // ƒ age(){console.log("呵呵");}
  var age = 22;
  console.log(age);  // 22
  function age(){
    console.log("呵呵");
  }
  console.log(age);  // 22
}
foo(age); 
```

首先对函数进行词法分析。第一步，该函数有参数，所以要先把参数赋值给 AO，且值为 undefined，即`AO.age = undefined`；第二步，函数内部定义了一个局部变量 `age`，但是 AO 上已经有同名的值，所以不做任何操作；第三步，函数内部声明了一个函数 age，AO 上有同名的值，所以要用函数将原始值覆盖，即 `AO.age = f age(){console.log("呵呵");}`。

​		词法分析结束后，开始执行函数。执行第一行代码 `console.log(age)` 时，AO.age 的值为 `AO.age = f age(){console.log("呵呵");}`，所以此时输出的是函数；第二行代码 `var age = 22` 给变量 age 进行了赋值操作，此时 AO 上对应的变量也发生了改变，即 `AO.age = 22`；执行第三行代码时，输出 22；内部的函数只有在声明的时候才会对 AO 造成影响，执行时不会再去改变 AO 上对应的值，所以最后输出的 age 还是 22 。

## 对象类型

- JavaScript 中的所有事物都是对象：字符串、数值、数组、函数...此外，JavaScript 允许自定义对象。
- JavaScript 提供多个内建对象，比如 String、Date、Array 等等。
- 对象只是带有属性和方法的特殊数据类型。

### **一、数组**

#### 1.1 数组简介

数组对象的作用是：使用单独的变量名来存储一系列的值。类似于Python中的列表。

```javascript
// 定义一个数组
var a = [123, "ABC"];

// 数组也支持索引取值，索引值从0开始
console.log(a[1]);  // 输出"ABC"
```

#### 1.2 常用方法

- **length**    获取数组长度

```javascript
let l = [111,222,333,444,555,666];
console.log(l.length);  // 6
```

- **push**    数组尾部追加元素，直接作用于原数组，返回值为操作后数组的长度

```javascript
let l = [111,222,333,444,555,666];
let ele = l.push(777);
console.log(ele);  // 7
console.log(l);  // [111,222,333,444,555,666,777]
```

- **pop**    移除数组尾部元素，直接作用于原数组，返回值为移除的数组元素

```javascript
let l = [111,222,333,444,555,666,777];
let ele = l.pop();
console.log(ele);  // 777
console.log(l);  // [111,222,333,444,555,666]
```

- **unshift**    在数组头部插入元素，直接作用于原数组，返回值为操作后数组的长度

```javascript
let l = [111,222,333,444,555,666,777];
let ele = l.unshift(888);
console.log(ele);  // 8
console.log(l);  // [888,111,222,333,444,555,666,777]
```

- **shift**    移除数组头部的元素，直接作用于原数组，返回值为移除的数组元素

```javascript
let l = [111,222,333,444,555,666,777];
let ele = l.shift();
console.log(ele);  // 111
console.log(l);  // [222,333,444,555,666,777]
```

- **slice**    数组切片，返回值为切片的结果，不改变原数组的值

```javascript
let l = [111,222,333,444,555,666,777];
let ele = l.slice(2,4)
console.log(ele);  // [333, 444]
```

- **reverse**    数组元素反转，直接作用于原数组，返回值为反转后的数组

```javascript
let l = [111,222,333,444,555,666];
let ele = l.reverse();
console.log(ele);  // [666, 555, 444, 333, 222, 111]
```

- **join**    将数组元素连接成字符串，返回值为拼接的字符串，不改变原数组的值

```javascript
let l = [111,222,333,444,555,666];
let ele = l.join("$");
console.log(ele);  // "111$222$333$444$555$666"
```

- **concat**    连接数组，返回值为连接后的新数组

```javascript
let l = [111,222,333,444,555,666];
let ele = l.concat(["aaa", "bbb", "ccc"]);
console.log(ele);  // [111, 222, 333, 444, 555, 666, "aaa", "bbb", "ccc"]
```

- **sort**    数组排序，并不是按照值的大小排序，而是将元素转换成字符串，按照字符串顺序排序

```javascript
var s = [1,10,11,2,22];
s.sort();
console.log(s);  // [1, 10, 11, 2, 22]
```

**注意：**sort 方法是先将元素转换成字符串，再按照字符串顺序排序，即按照字符编码的顺序进行排序。如果想按照其他标准进行排序，就需要自定义比较函数，该函数要比较两个值，然后返回一个用于说明这两个值的相对顺序的数字。比较函数应该具有两个参数 a 和 b，其返回值如下：

​		若 a 小于 b，在排序后的数组中 a 应该出现在 b 之前，则返回一个小于 0 的值。
​		若 a 等于 b，则返回 0。
​		若 a 大于 b，则返回一个大于 0 的值。

```javascript
function sortNumber(a,b){
    return a - b
};
var arr1 = [11, 100, 22, 55, 33, 44];
arr1.sort(sortNumber);
console.log(arr1);  // [11, 22, 33, 44, 55, 100]
```

- **forEach**  

语法格式：`forEach(function(currentValue, index, arr), thisValue)`

参数说明：

| 参数                                 | 描述                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| *function(currentValue, index, arr)* | 必须传一个函数，数组中每个元素需要调用该函数。               |
| *thisValue*                          | 可选，传递给函数的值一般用 "this" 值，即调用该方法的数组；如果这个参数为空， "undefined" 会传递给 "this" 值。 |

函数参数说明：

| 参数           | 描述                           |
| -------------- | ------------------------------ |
| *currentValue* | 必需，当前元素。               |
| *index*        | 可选，当前元素的索引值。       |
| *arr*          | 可选，当前元素所属的数组对象。 |

```javascript
let l = [111,222,333];
// 函数传一个参数 
l.forEach(function(value){console.log(value)}, l);
// 输出结果为数组元素：
// 111
// 222
// 333

// 函数传两个参数
l.forEach(function(value,index){console.log(value,index)}, l);
// 输出结果为数组元素和索引：
// 111 0
// 222 1
// 333 2

// 函数传三个参数
l.forEach(function(value,index,arr){console.log(value,index,arr)}, l);
// 输出结果为数组元素、索引和数组：
// 111 0 [111,222,333]
// 222 1 [111,222,333]
// 333 2 [111,222,333]

// 尝试给函数传四个参数
l.forEach(function(value,index,arr, arr2){console.log(value,index,arr, arr2)}, l);
// 输出结果为数组元素、索引、数组和undefined：
// 111 0 [111,222,333] undefined
// 222 1 [111,222,333] undefined
// 333 2 [111,222,333] undefined

// 所以函数最多可以传三个参数
```

- **splice**    删除数组元素，并向数组添加新元素

| 参数         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| *index*      | 必需，规定从何处添加/删除元素。 该参数是开始插入和（或）删除的数组元素的下标，必须是数字。 |
| *howmany*    | 必需，规定应该删除多少元素。必须是数字，但可以是 "0"。 如果未规定此参数，则删除从 index 开始到原数组结尾的所有元素。 |
| *item1，...* | 可选，要添加到数组的新元素                                   |

```javascript
let l = [111,222,333,444,555,666];
let ele = l.splice(1);  //从索引为1的位置开始，往后删除所有元素,即删除索引[1:](两边都闭合)
console.log(ele);  // [222,333,444,555,666]
console.log(l);  // [111]
 
let l = [111,222,333,444,555,666];
let ele = l.splice(1,1);  //从索引为1的位置开始，往后删除1个元素，即删除索引[1:1](两边都闭合)
console.log(ele);  // [222]
console.log(l);  // [111,333,444,555,666]

let l = [111,222,333,444,555,666];
let ele = l.splice(1,2);  //从索引为1的位置开始，往后删除2个元素，即删除索引[1:2](两边都闭合)
console.log(ele);  // [222,333]
console.log(l);  // [111,444,555,666]

let l = [111,222,333,444,555,666];
let ele = l.splice(0,2,"aaa");  // 删除前两个元素并将"aaa"放到对应的位置上
console.log(ele);  // [111，222]
console.log(l);  // ["aaa",333,444,555,666]

let l = [111,222,333,444,555,666];
let ele = l.splice(0,2,["aaa","bbb","ccc"]);  //删除前两个元素并将传入的数组放到对应的位置
console.log(ele);  // [111，222]
console.log(l);  // [["aaa","bbb","ccc"],333,444,555,666]
```

- **map**     使用方式和 `forEach` 差不多

```javascript
l.map(function(value, index){return value*index},l)
// [0, 222, 666, 1332, 2220, 3330]
```

### **二、自定义对象**

#### 2.1 创建对象

- 方式一，直接创建

```javascript
let d1 = {"name":"cdc", "age":18}
```

- 方式二，通过 `new` 关键字

```javascript
let d2 = new Object()
console.log(d2)  // {}  是一个空对象

// 给空对象赋值或者修改对象的值也有两种方式
// 方式一
d2.name = "cdc"
d2.age = 18

// 方式二
d2["name"] = "cdc"
d2["age"] = 18
```

#### 2.2 对象取值

```javascript
let d1 = {"name":"cdc", "age":18}

// 方式一
console.log(d1.name)
console.log(d1.age)

// 方式二
console.log(d1["name"])
console.log(d1["age"])
```

#### 2.3 循环取值

```javascript
// 通过 for 循环取出对象所有的值
let d1 = {"name":"cdc", "age":18}
for(let i in d1){
    console.log(i, d1[i])  // i 为对象的每个键
}
```

### **三、Date对象**

#### 3.1 创建对象

```javascript
let d1 = new Date();
console.log(d1);  // Wed Jan 06 2021 22:48:17 GMT+0800 (中国标准时间)

// 转换成本地时间
d1.toLocaleString()  // 2021/1/6 下午10:48:17

// 支持自己手动输入时间
let d1 = new Date('2020/11/11 11:11:11')
console.log(d1);  // Wed Nov 11 2020 11:11:11 GMT+0800 (中国标准时间)

let d1 = new Date(2020,11,11,11,11,11)
console.log(d1);  // Fri Dec 11 2020 11:11:11 GMT+0800 (中国标准时间)
```

#### 3.2 具体方法

```javascript
var d = new Date(); 
d.getDate()                 // 获取日
d.getDay ()                 // 获取星期
d.getMonth ()               // 获取月（0-11）
d.getFullYear ()            // 获取完整年份
d.getHours ()               // 获取小时
d.getMinutes ()             // 获取分钟
d.getSeconds ()             // 获取秒
d.getMilliseconds ()        // 获取毫秒
d.getTime ()                // 返回累计毫秒数(从1970/1/1午夜)
```

### **四、JSON对象**

#### 4.1 序列化

```javascript
// 对象转换成JSON字符串(序列化)
var obj = {"name": "cdc", "age": 18};
var strObj = JSON.stringify(obj);
console.log(typeof(obj))  // object
console.log(typeof(strObj))  // string
```

#### 4.2 反序列化

```javascript
// JSON字符串转换成对象(反序列化)
var strObj = '{"name": "cdc", "age": 18}';
var obj = JSON.parse(strObj);
console.log(typeof(obj))  // object
console.log(typeof(strObj))  // string
```

### **五、Math对象**

```javascript
Math.abs(x)      // 返回数的绝对值。
Math.exp(x)      // 返回 e 的指数。
Math.floor(x)    // 对数进行下舍入。
Math.log(x)      // 返回数的自然对数（底为e）。
Math.max(x,y)    // 返回 x 和 y 中的最高值。
Math.min(x,y)    // 返回 x 和 y 中的最低值。
Math.pow(x,y)    // 返回 x 的 y 次幂。
Math.random()    // 返回 0 ~ 1 之间的随机数。
Math.round(x)    // 把数四舍五入为最接近的整数。
Math.sin(x)      // 返回数的正弦。
Math.sqrt(x)     // 返回数的平方根。
Math.tan(x)      // 返回角的正切。
```

### **六、RegExp对象**

#### 6.1 创建对象

```javascript
// 方式一
let reg1 = new RegExp('^[a-z][a-zA-Z0-9]{5,11}')

// 方式二（推荐）
let reg1 = /'^[a-z][a-zA-Z0-9]{5,11}'/
```

#### 6.2 匹配字符串

```javascript
let reg1 = /'^[a-z][a-zA-Z0-9]{5,11}'/
console.log(reg1.test("cdchello")) // true
```

#### 6.3 字符串和正则配合使用

- **match**    查找字符串中符合正则的内容

```javascript
let s = "hello world";
console.log(s.match(/o/));
// 输出 ["o", index: 4, input: "hello world", groups: undefined]
```

- **search**    查找字符串中符合正则表达式的内容位置

```javascript
let s = "hello world";
console.log(s.search(/o/));  // 4  只返回匹配到的第一次的索引
```

- **split**    按照正则表达式对字符串进行切割

```javascript
let s = "hello world";
console.log(s.split(/o/));  // ["hell", " w", "rld"]
```

- **replace**    对字符串按照正则进行替换

```javascript
let s = "hello world";
console.log(s.replace(/o/, "s")); // hells world  只替换第一次匹配到的内容
```

#### 6.4 正则匹配模式

​       通过上述字符串和正则表达式配合使用的例子，我们可以发现有些方法不会匹配操作整个字符串。如果想要对整个字符串进行匹配操作，可以通过设置匹配模式实现。

```javascript
// g：表示匹配全局，使用时直接在正则表达式后面加g
let s = "hello world";
console.log(s.match(/o/g));  // ["o", "o"]

// i：表示匹配时不区分大小写，使用时直接在正则表达式后面加i
let s = "hellO world";
console.log(s.match(/o/i));  //["O", index: 4, input: "hellO world", groups: undefined]

// 两种模式可以联合使用
let s = "hellO world";
console.log(s.match(/o/gi));  // ["O", "o"]
```

#### 6.5 正则表达式中的那些坑

- **编写正则表达式时，逗号后面不要加空格**

```javascript
let reg1 = /[a-z][a-zA-Z0-9]{5,11}/
console.log(reg1.test("cdchello"));  // true

let reg1 = /[a-z][a-zA-Z0-9]{5, 11}/  // 注意逗号后面多加了一个空格
console.log(reg1.test("cdchello"));  // false
```

- **全局匹配时注意索引位置**

```javascript
let reg1 = /[a-z][a-zA-Z0-9]{5,11}/g
reg1.test("cdchello")  // true
reg1.test("cdchello")  // false
reg1.test("cdchello")  // true
reg1.test("cdchello")  // false
```

​       如果 `regExpObject` 带有全局标志 `g`，`test()` 函数不是从字符串的开头开始查找，而是从属性`regExpObject.lastIndex` 所指定的索引处开始查找；该属性值默认为 0，所以第一次仍然是从字符串的开头查找。当找到一个匹配时，`test()` 函数会将 `regExpObject.lastIndex` 的值改为字符串中本次匹配内容的最后一个字符的下一个索引位置。当再次执行 `test()` 函数时，将会从该索引位置处开始查找，从而找到下一个匹配。   

​		因此，当我们使用 `test()` 函数执行了一次匹配之后，如果想要重新使用 `test()` 函数从头开始查找，则需要手动将 `regExpObject.lastIndex` 的值重置为 0。

​      如果 `test()` 函数再也找不到可以匹配的文本时，该函数会自动把 `regExpObject.lastIndex` 属性重置为 0。

```javascript
let reg1 = /[a-z][a-zA-Z0-9]{5,11}/g
reg1.test("cdchello");  // true
console.log(reg1.lastIndex);  // 8
reg1.test("cdchello");  // false
console.log(reg1.lastIndex);  // 0
reg1.test("cdchello");  // true
console.log(reg1.lastIndex);  // 8
reg1.test("cdchello");  // false
console.log(reg1.lastIndex); // 0
```

- **test() 方法不加参数时，永远返回 true**

```javascript
let reg1 = /[a-z][a-zA-Z0-9]{5,11}/
reg1.test()  // true
```

当我们不加参数调用 `RegExpObj.test()` 方法时，相当于执行 `RegExpObj.test("undefined")`，并且 `/undefined/.test()`  默认返回 `true`。我们可以来验证下：

```javascript
var reg4 = /^undefined$/;
reg4.test(); // 返回true
reg4.test(undefined); // 返回true
reg4.test("undefined"); // 返回true
```

