# Python初识

## 一、入门基础

### **1. 第一个Python程序**

python 代码都是编写在以 `.py` 结尾的文件中。我们随便新建一个文件，并将文件后缀名改为 `.py`，在里面编写我们的第一个 python 脚本：

```python
# print函数用于输出指定的内容
print("Hello World")
```

介绍以下两种方式执行脚本：

- 方式一，从终端进入存放代码脚本的目录，执行 `python 文件名.py` 即可。例如，我们将上述代码写在一个名为 `demo.py` 的文件中，且该文件存储在 F 盘，那么我们想要执行该脚本就得这样做：

![](/static/img/Python专题/00024.png)  

- 方式二，通过 IDE 执行代码

<img src="/static/img/Python专题/00025.png" style="zoom:50%;" />   

### 2. 语法格式

#### 2.1  缩进和换行

python 中是用缩进和换行来判断一行语句是否结束的（类似 Java 中的 `{} ` 来包含代码，标识代码的结束）。

```python
# 缩进用于判断一行是否结束
if a == 3:
    print("3")
else:
    pass

# 错误实例
if a == 3:
print("3")
else:
pass
```

缩进没有明确规定多少个空格表示，实际上只要整个代码中的缩进保持一致，代码就能正常运行。但一般约定俗成的都用4个空格表示缩进。

#### 2.2 注释

```python
# 注释单行

"""
注释多行
注释多行
"""

'''
注释多行
注释多行
'''
```

**补充：**在 python 中 `单引号` 和 `双引号` 本质上没有区别，但都必须成对使用。在某些特殊情况下可以混合使用。

```python
# 必须成对使用
"abcdefg" ---> 合法

'abcdefg' ---> 合法

"abcdefg' ---> 非法

# 混合使用示例
"He's my good friend"  ---> 合法
'He's my good friend'  ---> 非法
```

### **3、常量和变量**

#### **3.1 变量简介及作用**

变量是程序运行过程中产生的中间值，暂时存储在内存中，变量指向的是数据存储在内存中的地址，供后面的程序使用。在 python 中变量使用前可以不声明，但是一定要赋值。

怎么样？是不是又没有看懂？其实我们可以这么理解，计算机中每一个数据都会有一块对应的内存来存储它，我们想去取出并操作这些数据，肯定就得知道这些数据存在哪，就好比我想去你家蹭饭，我得先找到你家在哪，所以我就得知道你家的详细地址，这个地址对于存储数据的内存空间而言，就是内存地址。如果我的朋友比较多，我可以每天换着人蹭饭，那我就需要记住很多家庭住址，可是时间长了可能就分不清每一家分别是谁了，所以我可以给每个家庭住址做个标记，比如 `小明家：xxx省xxx市xxx区xxx小区`，这个 "小明家" 就可以理解为是一个变量名，我只需要知道变量就能分清并且找到对应的蹭饭地点啦。其实这也侧面体现出了变量的作用**就是方便了编程时对内存空间的操作，不用通过记住内存地址才能去操作对应的内存空间**。

​                                              <img src="/static/img/Python专题/00026.png" style="zoom:80%;" />  

我们再看一个实际的示例，首先变量 a 指向 10 的存储地址，那么以后在查找数据10 的时候，实际上时先通过变量 a 找到 10 存储的内存地址，再从对应的地址中把 10 取出来操作。接着把 a 赋值给 b，那么 b 也指向了 10 存储的内存地址，当把 b 指向的内容改变后，b 所指向的地址也会随之发生改变，相当于小明搬家了，那么肯定对应的家庭地址就和原来不一样了。

​                                  <img src="/static/img/Python专题/00027.png" style="zoom: 67%;" />  



#### **3.2 变量的命名规则**

- 由字母，下划线，数字组合搭配而成
- 不能以数字开头或者变量名为全数字
- 不能用 python 的关键字
- 不能是中文（可以用中文但最好别用）
- 名字要有意义，不要太长，可以用单词拼接表达意义
- 变量名区分大小写
- 推荐驼峰式命名或者下划线方式命名

```python
# 变量
a = 123
# print(a)  # 123

# 驼峰式命名
MyName = "cdc"

# 下划线方式命名(蛇形命名)
my_name = "cdc"

__ = "cdc"
# print(__)   # cdc

a = 10
b = a
a = 5
# print(a)  # 5
# print(b)  # 10
```

#### 3.3 变量的数据类型

在 python 中可以使用内置函数`type()` 来查看变量的数据类型。

```python
s = "aaaa"
print(type(s))  # <class 'str'>
n = 123
print(type(n))  # <class 'int'>
```

#### **3.4 常量**

常量通常指不可变的量，一般是全局变量，即程序中所有的模块都能使用。在 python 不存在绝对的常量，因为 python 中常量的值也是可以改变的 (说白了 python 中的常量其实本质上还是一个变量) ，为了方便区分，一般常量所有的字母都大写。

```python
PI = 3.141526
```

## 二、基本数据类型

### **1. 数字类型**

#### 1.1 数字类型简介

python 中的数字类型主要分为 `整型(int)` 和 `浮点型(float)` ，其中整数在 32位 的操作系统上的，整型的表数范围为  -2\*\*31～2\*\*31-1，即 -2147483648～2147483647，在 64位 的操作系统上的，整型的表数范围为 -2\*\*63～2\*\*63-1，即 -9223372036854775808～9223372036854775807 

```python
# 数字类型
a = 10
b = 666
c = 3.1415926
print(a, type(a))  # 10 <class 'int'>
print(b, type(b))  # 666 <class 'int'>
print(c, type(c))  # 3.1415926 <class 'float'>
```

#### **1.2 数字类型之间的转换**

```python
# 数字类型转换
var1 = 3.14
var2 = 5

# 浮点型转整型
var3 = int(var1)
print(var3, type(var3))  # 3 <class 'int'>

# 整型转浮点型
var4 = float(var2)
print(var4, type(var4))  # 5.0 <class 'float'>
```

#### **1.3 python 常用内置数学方法**

```python
# math模块是 内置的一个第三方模块，包含了很多数学计算相关的方法
import math

# 返回数字的上入整数
res = math.ceil(4.1)
print(res)  # 5

# 返回e的x次幂(ex)
res = math.exp(1)  # 2.718281828459045
print(res)

# 返回数字的绝对值
res = math.fabs(-10)
print(res)  # 10.0

# 返回数字的下舍整数
res = math.floor(4.9)
print(res)  # 4

# 计算对数
res = math.log(100, 10)
print(res)  # 2.0

# 计算以10为基数的x的对数
res = math.log10(100)
print(res)  # 2.0

# 返回x的整数部分与小数部分，两部分的数值符号与x相同，整数部分以浮点型表示
res = math.modf(3.14)
print(res)  # (0.14000000000000012, 3.0)

# 返回数字x的平方根
res = math.sqrt(4)
print(res)  # 2.0

# 返回数字的绝对值
res = abs(-10)
print(res)  # 10

# 返回给定参数的最大值，参数可以为序列。
res = max(10, 5)
print(res)  # 10

# 返回给定参数的最小值，参数可以为序列。
res = min(10, 5)
print(res)  # 5

# x**y 运算后的值。
res = pow(2, 3)
print(res)  # 8

# 返回浮点数x的四舍五入值，如给出n值，则代表舍入到小数点后的位数。
# round(x [,n])
res = round(3.14658, 2)
print(res)  # 3.15

# 计算两个数值之间的商和余数
res = divmod(7, 3)
print(res)  # (2, 1)
```

### **2. 字符串**

#### **2.1 创建字符串**

```python
# 创建字符串
# 单行字符串，单双引号都可以使用
var1 = 'Hello World!'
var2 = "Python RAlvin"

# 多行字符串，三层引号包裹，单双引号都可以使用
var3 = """
aaa
bbb
ccc
"""

var4 = '''
aaa
bbb
ccc
'''
print(var1, type(var1))
print(var3, type(var3))
```

#### **2.2 字符串常用方法**

```python
# 1 * 重复输出字符串
print('hello' * 2)

# 2 [] ,[:] 通过索引获取字符串中字符,这里和列表的切片操作是相同的,具体内容见列表
print('helloworld'[2:])

# 3 in 成员运算符 如果字符串中包含给定的字符返回 True
print('el' in 'hello')

# 4 % 格式化输出字符串
print('my name is %s' % name)
print("my name is %s and I'm %d years old" % (name, age))

# 5 + 字符串拼接
a = '123'
b = 'abc'
c = '789'
d1 = a + b + c
print(d1)

# + 效率低,建议使用join
d2 = ''.join([a, b, c])
print(d2)
```

#### **2.3 其余字符串内置方法**

```
string.capitalize()                                  把字符串的第一个字符大写
string.center(width)                                 返回一个原字符串居中,并使用空格填充至长度 width 的新字符串
string.count(str, beg=0, end=len(string))            返回 str 在 string 里面出现的次数，如果 beg 或者 end 指定则返回指定范围内 str 出现的次数
string.decode(encoding='UTF-8', errors='strict')     以 encoding 指定的编码格式解码 string，如果出错默认报一个 ValueError 的 异 常 ， 除 非 errors 指 定 的 是 'ignore' 或 者'replace'
string.encode(encoding='UTF-8', errors='strict')     以 encoding 指定的编码格式编码 string，如果出错默认报一个ValueError 的异常，除非 errors 指定的是'ignore'或者'replace'
string.endswith(obj, beg=0, end=len(string))         检查字符串是否以 obj 结束，如果beg 或者 end 指定则检查指定的范围内是否以 obj 结束，如果是，返回 True,否则返回 False.
string.expandtabs(tabsize=8)                         把字符串 string 中的 tab 符号转为空格，tab 符号默认的空格数是 8。
string.find(str, beg=0, end=len(string))             检测 str 是否包含在 string 中，如果 beg 和 end 指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1
string.index(str, beg=0, end=len(string))            跟find()方法一样，只不过如果str不在 string中会报一个异常.
string.isalnum()                                     如果 string 至少有一个字符并且所有字符都是字母或数字则返回 True,否则返回 False
string.isalpha()                                     如果 string 至少有一个字符并且所有字符都是字母则返回 True,否则返回 False
string.isdecimal()                                   如果 string 只包含十进制数字则返回 True 否则返回 False.
string.isdigit()                                     如果 string 只包含数字则返回 True 否则返回 False.
string.islower()                                     如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回 True，否则返回 False
string.isnumeric()                                   如果 string 中只包含数字字符，则返回 True，否则返回 False
string.isspace()                                     如果 string 中只包含空格，则返回 True，否则返回 False.
string.istitle()                                     如果 string 是标题化的(见 title())则返回 True，否则返回 False
string.isupper()                                     如果 string 中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回 True，否则返回 False
string.join(seq)                                     以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
string.ljust(width)                                  返回一个原字符串左对齐,并使用空格填充至长度 width 的新字符串
string.lower()                                       转换 string 中所有大写字符为小写.
string.lstrip()                                      截掉 string 左边的空格
string.maketrans(intab, outtab])                     maketrans() 方法用于创建字符映射的转换表，对于接受两个参数的最简单的调用方式，第一个参数是字符串，表示需要转换的字符，第二个参数也是字符串表示转换的目标。
max(str)                                             返回字符串 str 中最大的字母。
min(str)                                             返回字符串 str 中最小的字母。
string.partition(str)                                有点像 find()和 split()的结合体,从 str 出现的第一个位置起,把 字 符 串 string 分 成 一 个 3 元 素 的 元 组 (string_pre_str,str,string_post_str),如果 string 中不包含str 则 string_pre_str == string.
string.replace(str1, str2,  num=string.count(str1))  把 string 中的 str1 替换成 str2,如果 num 指定，则替换不超过 num 次.
string.rfind(str, beg=0,end=len(string) )            类似于 find()函数，不过是从右边开始查找.
string.rindex( str, beg=0,end=len(string))           类似于 index()，不过是从右边开始.
string.rjust(width)                                  返回一个原字符串右对齐,并使用空格填充至长度 width 的新字符串
string.rpartition(str)                               类似于 partition()函数,不过是从右边开始查找.
string.rstrip()                                      删除 string 字符串末尾的空格.
string.split(str="", num=string.count(str))          以 str 为分隔符切片 string，如果 num有指定值，则仅分隔 num 个子字符串
string.splitlines(num=string.count('\n'))            按照行分隔，返回一个包含各行作为元素的列表，如果 num 指定则仅切片 num 个行.
string.startswith(obj, beg=0,end=len(string))        检查字符串是否是以 obj 开头，是则返回 True，否则返回 False。如果beg 和 end 指定值，则在指定范围内检查.
string.strip([obj])                                  在 string 上执行 lstrip()和 rstrip()
string.swapcase()                                    翻转 string 中的大小写
string.title()                                       返回"标题化"的 string,就是说所有单词都是以大写开始，其余字母均为小写(见 istitle())
string.translate(str, del="")                        根据 str 给出的表(包含 256 个字符)转换 string 的字符,要过滤掉的字符放到 del 参数中
string.upper()                                       转换 string 中的小写字母为大写
```

### **3. 布尔值**

布尔值只有 `True`、`False`  两种值，当语句成立或者某个变量不为空值时，此时布尔判断的值为 `True`，否则为 `False`。 实际上，计算机并不认识 `True` 和 `False`，解释器会将 `True` 转变成数值 1，将`False` 转变为数值 0。在 Python 中也可以直接用 `True`、`False` 来表示布尔值。

```python
# 1的布尔值为True，0的布尔值为False
print(bool(1))  # True
print(bool(0))  # False

# True的布尔值为True，False的布尔值为False
print(bool(True)  # True
print(bool(False)  # False

# 语句条件成立的情况下布尔值为True,否则为False
print(4 > 2)  # True

# 变量为空时布尔值为False,变量有值的时候布尔值为True
s1 = ""
s2 = "aaa"
print(bool(s1))  # True
print(bool(s1))  # False
      
# 布尔值也可以直接进行数学运算，因为True相当于1,False相当于0
print(True + 1) # 2

# 与或非操作：
bool(1 and 0)
bool(1 and 1)
bool(1 or 0)
bool(not 0)

# 布尔值经常用在条件判断中:
age=18
if age > 18:       # 等价于bool(age > 18)
    print('old')
else:
    print('young')
```

## 三、运算符

### **1. 算术运算符**

```python
a = 2
b = 3

# 加法
print(a + b)

# 减法
print(a - b)

# 乘法
print(a * b)

# 除法
print(a / b)

# 取余
print(a % b)

# 取整
print(a // b)

# 幂运算
print(a ** b)
```

### **2. 比较运算符**

```python
a = 2
b = 3

# 等于
print(a == b)

# 不等于
print(a != b)

# 大于
print(a > b)

# 小于
print(a < b)

# 大于等于
print(a >= b)

# 小于等于
print(a <=b )
```

### **3. 逻辑运算符**

#### 3.1 常用逻辑运算符

```python
# 与运算，两者都成立结果为真，只要有一者不成立结果为假
a and b

# 或运算，两者都不成立结果为假，只要有一者成立结果为真
a or b

# 非运算，与原来的结果相反
not a
```

#### **3.2 and** 和 **or** 与数字运算

**运算规则：**能够明确得到结果时，就不需要往下计算了，结果就是第一个数；否则结果就是第二个数。

```python
# 示例1
print(0 and 3)  # 0
print(1 and 3)  # 3
print(2 and 0)  # 0
```

在 Python 中，0 表示 False，对于与运算，只要有一者不成立结果为假，因此第一行代码当看到 0 时，就能确定结果肯定为假，没有必要再看后面的值，所以结果为一开始看到的数字，即 0；

第二行代码中，看到第一个值为 1，此时还无法确定整个表达式的结果是真还是假，因此还要继续往下看，发现后面的值为 3，那么整个表达式的结果就为真，所以结果就为后面看到的这个数字，即 3；

第三行代码同理，看到第一个值为 2，此时还无法确定整个表达式的结果是真还是假，因此还要继续往下看，发现后面的值为 0，那么整个表达式的结果就为假，所以结果就为后面看到的这个数字，即 0；

```python
# 示例2
print(2 or 0)  # 2
print(0 or 1)  # 1
print(0 or 0)  # 0
```

或操作中，只要有一者成立结果为真。对于第一行代码，当看到 2 时，已经可以确定整个表达式的结果了，没有必要继续看下去，所以结果为第一个看到的数字，即位 2；

第二行代码中，看到第一个值为 0，此时还无法确定整个表达式的结果是真还是假，因此还要继续往下看，发现后面的值为 1，那么整个表达式的结果就为真，所以结果就为后面看到的这个数字，即 1；

第三行代码同理，看到第一个值为 0，此时还无法确定整个表达式的结果是真还是假，因此还要继续往下看，发现后面的值为 0，那么整个表达式的结果就为假，所以结果就为后面看到的这个数字，即 0；

**补充：** 在没有()的情况下not 优先级高于 and，and优先级高于or，即优先级关系为( )>not>and>or，同一优先级从左往右计算。

```python
# 示例3
print(1 or 0 and 2)  # 1
print(0 or 1 and 2)  # 2
print(0 or 0 and 2)  # 0
```

### **4. 赋值运算符**

```python
a = 10
b = 5

a = b      # 普通赋值
a += b     # 加法赋值，等价于 a = a + b
a -= b	   # 减法赋值，等价于 a = a - b
a *= b     # 乘法赋值，等价于 a = a * b
a /= b     # 除法赋值，等价于 a = a / b
a **= b    # 幂赋值，等价于 a = a ** b
a %= b     # 取余赋值，等价于 a = a % b
a //= b     # 取整赋值，等价于 a = a // b
```

### **5. 成员运算符**

- **in：**如果在序列中找到指定的值，返回 True，否则返回 False

```python
a = "aaa"
b = "bbb"
c = "aaaccc"

print(a in c)  # True
print(b in c)  # False
```

- **not in：**如果在序列中未找到指定的值，返回 True，否则返回 False

```python
a = "aaa"
b = "bbb"
c = "aaaccc"

print(a in c)  # False
print(b in c)  # True
```

## 四、用户交互

input 函数用于接收用户在终端输入的信息，当程序运行到input()时，程序会阻塞，只有接收到用户的键入操作，程序才会继续向下执行。

```python
name = input("请输入用户名：")
print(name)
```

**注意：**通过 `input` 方法接收到的数据都是字符串类型，如果需要操作的是其他类型的数据，需要进行转换

```python
_age = input("请输入年龄：")
print(_age, type(_age))  # 123 <class 'str'>

age = int(_age)
print(age, type(_age))  # 123 <class 'int'>
```

## 五、格式化输出

字符串格式化输出的三种方式：

- ##### **方式一：%s**


在 Python 中 `%s` 可以接收任何类型的数据，对于其他类型数据不必使用专门对应的占位符去替换（当然你想使用也是可以的，例如用 `%d` 来对整型数据进行占位替换）

```python
# 按照位置和占位符一一对应，按照传值顺序进行替换，多了或少了都不行
name = "cdc"
age = 18

# 单个值
info1 = "my name is %s" % name

# 多个值
info2 = "my name is %s and I'm %d years old" % (name, age)
info3 = "my name is %s and I'm %s years old" % (name, age)
```

```python
# 以字典的形式进行传值，打破了位置的限制
info = "my name is %(name)s and I'm %(age)s years old" % {"age": 18, "name": "cdc"}
print(info) # my name is cdc and I'm 18 years old
```

- ##### **方式二：format**


```python
# 按照位置传值
name = "cdc"
age = 18
info = "my name is {} and I'm {} years old".format(name, age)

# 按照索引传值
info = "I'm {1} years old and my name is {0}".format(name, age)

# 使用关键字或者字典方式传值，打破位置限制
info = "I'm {name} years old and my name is {age}".format(name="cdc", age=18)

kwargs = {'name': 'cdc', 'age': 18}
info = "my name is {name} and I'm {age} years old".format(**kwargs)  # 使用**进行解包操作
```

```python
# 填充与格式化
# 先取到值,然后在冒号后设定填充格式：[填充字符][对齐方式][宽度]

# *<10：左对齐，总共10个字符，不够的用*号填充
print('{0:*<10}'.format('开始执行'))  # 开始执行******

# *>10：右对齐，总共10个字符，不够的用*号填充
print('{0:*>10}'.format('开始执行'))  # ******开始执行

# *^10：居中显示，总共10个字符，不够的用*号填充
print('{0:*^10}'.format('开始执行'))  # ***开始执行***
```

```python
# 精度与进制
print('{salary:.3f}'.format(salary=1232132.12351))  #精确到小数点后3位，四舍五入，结果为：1232132.124
print('{0:b}'.format(123))  # 转成二进制，结果为：1111011
print('{0:o}'.format(9))  # 转成八进制，结果为：11
print('{0:x}'.format(15))  # 转成十六进制，结果为：f
print('{0:,}'.format(99812939393931))  # 千分位格式化，结果为：99,812,939,393,931
```

- ##### **方式三：f**


```python
# 该方法是从 Python 3.6 版本才开始引入使用的
name = "cdc"
age = 18
info = f"my name is {name} and I'm {age} years old"

# {} 中除了可以传变量，也支持任意合法的Python表达式
# 比如：数学表达式
print(f'{3*3/2}') # 4.5

# 比如：函数的调用
def foo(n):
    print('foo say hello')
    return n

print(f'{foo(10)}') # 会调用foo(10),然后打印其返回值

# 比如：调用对象的方法
name='CDC'
print(f'{name.lower()}') # cdc
```

**区别：**以上三种字符串格式化方式的速度是依次越来越快的，但是最快的第三种只支持 `Python3.6` 及以上版本。

## 六、编码简介

- ASCII	主要用于显示现代英语和其他西欧语言，其多只能用 8 位来表示（一个字节），即：2**8 = 256，所 以，ASCII码多只能表示 256 个符号
-  GBK     国标码占用2个字节，兼容对应的ASCII码，由于国标码使用两个字节，所以对应ASCII码时，第一个字节全为0

```
字母A: 0100 0001           # ASCII 
字母A: 0000 0000 0100 0001 # 国标码
```

- Unicode	万国码	4个字（32位） 
- UTF-8      每个字符最少占8位，每个字符占用的字节数不定，根据文字内容进行具体编码。比如，英文就一个字节，汉字占3个字节。
- UTF-16    每个字符最少占16位

```python
# a=bytes('hello','utf8')
# a=bytes('中国','utf8')


a = bytes('中国', 'utf8')
b = bytes('hello', 'gbk')
#
print(a)  # b'\xe4\xb8\xad\xe5\x9b\xbd'
print(ord('h'))  # 其十进制 unicode 值为: 104
print(ord('中'))  # 其十进制 unicode 值为:20013

#  h   e  l   l   o
# 104 101 108 108 111   编码后结果:与ASCII表对应


#     中                国
#   \xd6\xd0         \xb9\xfa       gbk编码后的字节结果
# \xe4 \xb8 \xad   \xe5 \x9b \xbd    utf8编码后的字节结果
# 228 184 173      229 155  189        a[:]切片取


c = a.decode('utf8')
d = b.decode('gbk')
# b=a.decode('gbk') :很明显报错

print(c)  # 中国
print(d)  # hello
```



