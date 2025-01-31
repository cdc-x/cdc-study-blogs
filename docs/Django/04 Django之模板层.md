# Django之模板语言

## 一、模板简介

​		对于一个web请求的全过程，浏览器发出的请求会转到视图函数进行相关的处理，再将处理完的结果返回给浏览器进行渲染。如果我们想给浏览器返回一个我们自己设计好的页面，可以在视图函数中这么写：

```python
from django.shortcuts import HttpResponse

def index(request):
    name = "cdc"
    return HttpResponse("<h1>欢迎%s</h1>" % name)
```

但是这种将前端代码和后端代码耦合到一起的开发方式会存在以下的问题：

```
1、程序的可维护性与可扩展性问题
前端界面一旦需要重新设计、修改，则必须对后端的Python代码进行相应的修改。 然而前端界面的修改往往比后端 Python 代码的修改要频繁得多，因此如果可以在不进行 Python 代码修改的情况下变更前端界面的设计，那将会方便得多。

2、开发效率问题
Python 代码编写和 HTML 设计是两项不同的工作，大多数专业的网站开发环境都将它们分配给不同的人员（甚至不同部门）来完成。 专门的程序员去编写 Python代码、专门的设计人员去制作模板，这两项工作同时进行，效率才是最高的。
```

​		为此，Django专门提供了模板系统（模板层）来实现这种将HTML页面和python代码分离开发的模式。存放在templates目录下的html文件称之为模板文件，如果我们想返回的html页面中的数据是动态的，那么必须在html页面中进行相关的操作，django提供了一套专门的模板语法。

## 二、模板语法之变量

### **2.1、变量**

​		在 html 页面中需要动态生成的数据，我们可以使用一个嵌入的变量进行占位，最后使用视图函数渲染模板的时候再将对应的变量只以字典键值对的形式传给模板。对于 html 模板而言，当模版引擎遇到一个变量，它将解析这个变量，然后用结果替换掉它本身。例如，我们要实现根据不同的登录用户，在首页显示欢迎该用户的信息。

语法：{{ 变量名 }}

```python
# views.py

from django.shortcuts import HttpResponse, render


def login(request):
    if request.method == "POST":
        user = request.POST.get("name")
        pwd = request.POST.get("pwd")
        return render(request, "index.html", {"user": user})

    return render(request, "login.html")
```

```python
# urls.py
from django.conf.urls import url
from Django_templates import views

urlpatterns = [
    url(r"^login", views.login)

]
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
    <h1 style="text-align: center">欢迎光临，{{ user }} !</h1>
</body>
</html>

<!-- templates/login.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登陆页面</title>
</head>
<body>
<form action="/login/" method="post">
    <p>用户名：<input type="text" name="name"></p>
    <p>密  码：<input type="password" name="pwd"></p>
    <p><input type="submit" value="登录"></p>
</form>
</body>
</html>
```

传给模板的值可以是任意的python数据类型

```python
# views.py

from django.shortcuts import render

def test(request):
    # 传给模板的变量值可以是任意python类型，如下
    msg='hello world'
    dic={'k1':1,'k2':2}
    class Person(object):
        def __init__(self,name,age):
            self.name=name
            self.age=age

    obj=Person('egon',18)
    li = [1,'aaa',obj]

    return render(request,'test.html',{'msg':msg,'dic':dic,'obj':obj,'li':li})
```

```html
<!-- test.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p>{{ msg }}</p>
<p>{{ dic }}</p>
<p>{{ obj }}</p>
<p>{{ li }}</p>
</body>
</html>
```

注意：

​		render函数的第三个参数包含了要传给模板的变量值，是一个**字典类型**，**该字典中的key必须与模板文件中的变量名相对应**，render函数会去templates目录下找到模板文件，然后根据字典中的key对应到模板文件中的变量名进行赋值操作，最后将赋值后的模板文件内容返回给浏览器

在视图函数中，如果需要传输的变量较多，我们可以直接将render函数的第三个参数简写为locals()，该函数会将视图内定义的变量与值转换为字典中的k与v，如

```python
# views.py

from django.shortcuts import render

def test(request):
    # 传给模板的变量值可以是任意python类型，如下
    msg='hello world'
    dic={'k1':1,'k2':2}
    class Person(object):
        def __init__(self,name,age):
            self.name=name
            self.age=age

    obj=Person('egon',18)
    li = [1,'aaa',obj]

    return render(request,'test.html',locals()) #locals()会将函数test内定义的名字与值转换为字典中的k与v
```

### **2.2、使用句点符号进行查询**

当视图传给模板的值中包含多个元素时，若想取出单个元素，可以使用句点符。句点符既可以引用容器类型的元素（字典、列表等），也可以引用对象的方法

```python
# views.py

from django.shortcuts import HttpResponse, render

def index(request):
    msg = 'hello world'
    dic = {'name': "cdc", 'age': 20}

    class Person(object):
        def __init__(self, name, age):
            self.name = name
            self.age = age

    obj = Person('egon', 18)
    li = [1, 'aaa', obj]

    return render(request, "index.html", locals())
```

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>

<!--将接收到的字符串转成大写-->
<p>{{ msg.upper }}</p>  

<!--取字典的键来取值-->
<p>{{ dic.name }}</p>

<!--获取对象的属性-->
<p>{{ obj.name }}</p>

<!--获取列表中第二个元素-->
<p>{{ li.1 }}</p>
</body>
</html>
```

**注意：Django的模板语言中属性的优先级大于方法**

视图中定义的字典中含有一个名为 items 的键

```
def xx(request):
    d = {"a": 1, "b": 2, "c": 3, "items": "100"}
    return render(request, "xx.html", {"data": d})
```

模板中对字典进行操作

```
{{ data.items }}
```

此时得到的结果并不是  dict_items([('a', 1), ('b', 2), ('c', 3)],('items',100))，而是100，即得到的是字典中键为 items 的值。

### **2.3、过滤器**

过滤器类似于python中的内置函数，会对变量进行对应的操作以后再显示

语法：{{ 变量名|过滤器:传给过滤器的参数 }}

常用过滤器如下：

- **default**

```
作用：如果一个变量值是False或者为空，使用default后指定的默认值，否则，使用变量本身的值
```

```python
<!-- 如果value=''则输出'nothing' -->
{{ value|default:"nothing" }}
```

- **length**

```
作用：返回值的长度。它对字符串、列表、字典等容器类型都起作用，如果value是 ['a', 'b', 'c', 'd']，那么输出是4
```

```python
{{ value|length }}
```

- **filesizeformat**

```
作用：将值的格式化为一个"人类可读的"文件尺寸(如13KB、4.1 MB、102bytes等等），如果 value 是 12312312321，输出将会是 11.5 GB
```

```python
{{ value|filesizeformat }}
```

- **date**

```
作用：将日期按照指定的格式输出，如果value=datetime.datetime.now(),按照格式Y-m-d则输出2019-02-02
```

```
{{ value|date:"Y-m-d" }}
```

date可用参数（显示默认都是英文，想要显示中文要去修改 django 项目中 settings.py 中的 LANGUAGE_CODE ，将值改为zh-Hans(简体中文)/zh-Hant(繁体中文)）

| 格式化字符 | 描述                                                         | 示例输出                                                     |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| a          | `'a.m.'`或`'p.m.'`（请注意，这与PHP的输出略有不同，因为这包括符合Associated Press风格的期间） | `'a.m.'`                                                     |
| A          | `'AM'`或`'PM'`。                                             | `'AM'`                                                       |
| b          | 月，文字，3个字母，小写。                                    | `'jan'`                                                      |
| B          | 未实现。                                                     |                                                              |
| c          | ISO 8601格式。 （注意：与其他格式化程序不同，例如“Z”，“O”或“r”，如果值为naive datetime，则“c”格式化程序不会添加时区偏移量（请参阅[`datetime.tzinfo`](https://docs.python.org/3/library/datetime.html#datetime.tzinfo)） 。 | `2008-01-02T10:30:00.000123+02:00`或`2008-01-02T10:30:00.000123`如果datetime是天真的 |
| d          | 月的日子，带前导零的2位数字。                                | `'01'`到`'31'`                                               |
| D          | 一周中的文字，3个字母。                                      | `“星期五”`                                                   |
| e          | 时区名称 可能是任何格式，或者可能返回一个空字符串，具体取决于datetime。 | `''`、`'GMT'`、`'-500'`、`'US/Eastern'`等                    |
| E          | 月份，特定地区的替代表示通常用于长日期表示。                 | `'listopada'`（对于波兰语区域，而不是`'Listopad'`）          |
| f          | 时间，在12小时的小时和分钟内，如果它们为零，则分钟停留。 专有扩展。 | `'1'`，`'1:30'`                                              |
| F          | 月，文，长。                                                 | `'一月'`                                                     |
| g          | 小时，12小时格式，无前导零。                                 | `'1'`到`'12'`                                                |
| G          | 小时，24小时格式，无前导零。                                 | `'0'`到`'23'`                                                |
| h          | 小时，12小时格式。                                           | `'01'`到`'12'`                                               |
| H          | 小时，24小时格式。                                           | `'00'`到`'23'`                                               |
| i          | 分钟。                                                       | `'00'`到`'59'`                                               |
| I          | 夏令时间，无论是否生效。                                     | `'1'`或`'0'`                                                 |
| j          | 没有前导零的月份的日子。                                     | `'1'`到`'31'`                                                |
| l          | 星期几，文字长。                                             | `'星期五'`                                                   |
| L          | 布尔值是否是一个闰年。                                       | `True`或`False`                                              |
| m          | 月，2位数字带前导零。                                        | `'01'`到`'12'`                                               |
| M          | 月，文字，3个字母。                                          | `“三月”`                                                     |
| n          | 月无前导零。                                                 | `'1'`到`'12'`                                                |
| N          | 美联社风格的月份缩写。 专有扩展。                            | `'Jan.'`，`'Feb.'`，`'March'`，`'May'`                       |
| o          | ISO-8601周编号，对应于使用闰年的ISO-8601周数（W）。 对于更常见的年份格式，请参见Y。 | `'1999年'`                                                   |
| O          | 与格林威治时间的差异在几小时内。                             | `'+0200'`                                                    |
| P          | 时间为12小时，分钟和'a.m。'/'p.m。'，如果为零，分钟停留，特殊情况下的字符串“午夜”和“中午”。 专有扩展。 | `'1 am'`，`'1:30 pm' / t3>，'midnight'，'noon'，'12：30 pm' / T10>` |
| r          | [**RFC 5322**](https://tools.ietf.org/html/rfc5322.html)格式化日期。 | `'Thu, 21 Dec 2000 16:01:07 +0200'`                          |
| s          | 秒，带前导零的2位数字。                                      | `'00'`到`'59'`                                               |
| S          | 一个月的英文序数后缀，2个字符。                              | `'st'`，`'nd'`，`'rd'`或`'th'`                               |
| t          | 给定月份的天数。                                             | `28` to `31`                                                 |
| T          | 本机的时区。                                                 | `'EST'`，`'MDT'`                                             |
| u          | 微秒。                                                       | `000000` to `999999`                                         |
| U          | 自Unix Epoch以来的二分之一（1970年1月1日00:00:00 UTC）。     |                                                              |
| w          | 星期几，数字无前导零。                                       | `'0'`（星期日）至`'6'`（星期六）                             |
| W          | ISO-8601周数，周数从星期一开始。                             | `1`，`53`                                                    |
| y          | 年份，2位数字。                                              | `'99'`                                                       |
| Y          | 年，4位数。                                                  | `'1999年'`                                                   |
| z          | 一年中的日子                                                 | `0`到`365`                                                   |
| Z          | 时区偏移量，单位为秒。 UTC以西时区的偏移量总是为负数，对于UTC以东时，它们总是为正。 | `-43200`到`43200`                                            |

- **slice**

```
作用：对输出的字符串进行切片操作，顾头不顾尾,如果value="egon"，则输出"eg"
```

```
{{ value|slice:"0:2" }}　
```

- **truncatechars**

```
作用：如果字符串字符多于指定的字符数量，那么会被截断。截断的字符串将以可翻译的省略号序列（"..."）结尾，如果value="hello world egon 嘎嘎"，则输出"hello...",注意8个字符也包含末尾的3个点
```

```
{{ value|truncatechars:8 }}
```

- **truncatewords**

```
作用：同truncatechars，但truncatewords是按照单词截断，注意末尾的3个点不算作单词，如果value="hello world egon 嘎嘎"，则输出"hello world ..."
```

```
{{ value|truncatewords:2 }}
```

- **safe**

```
作用：出于安全考虑，Django的模板会对HTML标签、JS等语法标签进行自动转义,例如value="<script>alert(123)</script>"，模板变量{{ value }}会被渲染成&lt;script&gt;alert(123)&lt;/script&gt;
交给浏览器后会被解析成普通字符"<script>alert(123)</script>"，失去了js代码的语法意义，但如果我们就想让模板变量{{ value }}被渲染的结果有语法意义，那么就用到了过滤器safe，比如value='<a href="https://www.baidu.com">点我啊</a>'，在被safe过滤器处理后就成为了真正的超链接，不加safe过滤器则会当做普通字符显示'<a href="https://www.baidu.com">点我啊</a>'
```

```
{{ value|safe }}
```

- **cut**

```
作用：移除value中所有的与给出的变量相同的字符串，如果value的值是"banana"，结果为"bnn"
```

```
<!-- 把字符串中所有的字母a移除 -->
{{ value|cut:'a' }}
```

- **join**

```
作用：使用字符串按照指定的符号连接列表，例如Python的str.join(list)，如果value的值是"["hello", "world", "!"]"，结果为"hello/world/!"
```

```
{{ value|join:"/" }}
```

- **timesince**

```
作用：计算日期间的时间间隔，变量和参数必须是时间对象，且变量的日期要小于参数的日期
```

```
{{ value|timesince }}  不加参数，表示 value 据当前日期间隔多少，如 value是2020年03月12日12时的一个时间对象，当前为2020年03月14日14时的一个时间对象，得到的结果为"2日，2小时"
{{ value|timesince:value2 }}  加参数，表示 value 据 value2 日期间隔的时间
```

- **timeuntil**

```
作用：类似于timesince，它测量从现在开始直到给定日期或日期时间的间隔，且变量的日日期要大于参数的日期
```

```
{{ value|timeuntil }}  不加参数，表示当前日期距离 value 的时间间隔（value必须在当前日期之后，不然结果为0）
{{ value|timeuntil:value2 }}  加参数，表示 value2 距离 value 的时间间隔（value必须在value2之后，不然结果为0）
```

- 其余常用内置过滤器（了解）

| 过滤器             | 描述                                                     | 示例                                                         |
| ------------------ | -------------------------------------------------------- | ------------------------------------------------------------ |
| upper              | 以大写方式输出                                           | {{ user.name \| upper }}                                     |
| add                | 给value加上一个数值                                      | {{ user.age \| add:”5” }}                                    |
| addslashes         | 单引号加上转义号                                         |                                                              |
| capfirst           | 第一个字母大写                                           | {{ ‘good’\| capfirst }} 返回”Good”                           |
| center             | 输出指定长度的字符串，把变量居中                         | {{ “abcd”\| center:”50” }}                                   |
| cut                | 删除指定字符串                                           | {{ “You are not a Englishman” \| cut:”not” }}                |
| date               | 格式化日期                                               |                                                              |
| default            | 如果值不存在，则使用默认值代替                           | {{ value \| default:”(N/A)” }}                               |
| default_if_none    | 如果值为None, 则使用默认值代替                           |                                                              |
| dictsort           | 按某字段排序，变量必须是一个dictionary                   | {% for moment in moments \| dictsort:”id” %}                 |
| dictsortreversed   | 按某字段倒序排序，变量必须是dictionary                   |                                                              |
| divisibleby        | 判断是否可以被数字整除                                   | {{ 224 \| divisibleby:2 }} 返回 True                         |
| escape             | 按HTML转义，比如将”<”转换为”&lt”                         |                                                              |
| filesizeformat     | 增加数字的可读性，转换结果为13KB,89MB,3Bytes等           | {{ 1024 \| filesizeformat }} 返回 1.0KB                      |
| first              | 返回列表的第1个元素，变量必须是一个列表                  |                                                              |
| floatformat        | 转换为指定精度的小数，默认保留1位小数                    | {{ 3.1415926 \| floatformat:3 }} 返回 3.142 四舍五入         |
| get_digit          | 从个位数开始截取指定位置的数字                           | {{ 123456 \| get_digit:’1’}}                                 |
| join               | 用指定分隔符连接列表                                     | {{ [‘abc’,’45’] \| join:’*’ }} 返回 abc*45                   |
| length             | 返回列表中元素的个数或字符串长度                         |                                                              |
| length_is          | 检查列表，字符串长度是否符合指定的值                     | {{ ‘hello’\| length_is:’3’ }}                                |
| linebreaks         | 用或 标签包裹变量                                        | {{ “Hi\n\nDavid”\|linebreaks }} 返回HiDavid                  |
| linebreaksbr       | 用 标签代替换行符                                        |                                                              |
| linenumbers        | 为变量中的每一行加上行号                                 |                                                              |
| ljust              | 输出指定长度的字符串，变量左对齐                         | {{‘ab’\|ljust:5}}返回 ‘ab ’                                  |
| lower              | 字符串变小写                                             |                                                              |
| make_list          | 将字符串转换为列表                                       |                                                              |
| pluralize          | 根据数字确定是否输出英文复数符号                         |                                                              |
| random             | 返回列表的随机一项                                       |                                                              |
| removetags         | 删除字符串中指定的HTML标记                               | {{value \| removetags: “h1 h2”}}                             |
| rjust              | 输出指定长度的字符串，变量右对齐                         |                                                              |
| slice              | 切片操作， 返回列表                                      | {{[3,9,1] \| slice:’:2’}} 返回 [3,9] {{ 'asdikfjhihgie' \| slice:':5' }} 返回 ‘asdik’ |
| slugify            | 在字符串中留下减号和下划线，其它符号删除，空格用减号替换 | {{ '5-2=3and5 2=3' \| slugify }} 返回 5-23and5-23            |
| stringformat       | 字符串格式化，语法同python                               |                                                              |
| time               | 返回日期的时间部分                                       |                                                              |
| timesince          | 以“到现在为止过了多长时间”显示时间变量                   | 结果可能为 45days, 3 hours                                   |
| timeuntil          | 以“从现在开始到时间变量”还有多长时间显示时间变量         |                                                              |
| title              | 每个单词首字母大写                                       |                                                              |
| truncatewords      | 将字符串转换为省略表达方式                               | {{ 'This is a pen' \| truncatewords:2 }}返回`This is ...     |
| truncatewords_html | 同上，但保留其中的HTML标签                               | {{ 'This is a pen' \| truncatewords:2 }}返回``This is ...    |
| urlencode          | 将字符串中的特殊字符转换为url兼容表达方式                | {{ ‘http://www.aaa.com/foo?a=b&b=c’ \| urlencode}}           |
| urlize             | 将变量字符串中的url由纯文本变为链接                      |                                                              |
| wordcount          | 返回变量字符串中的单词数                                 |                                                              |
| yesno              | 将布尔变量转换为字符串yes, no 或maybe                    | {{ True \| yesno }}{{ False \| yesno }}{{ None \| yesno }} 返回 yes  no maybe |

## 三、模板语法之标签

标签是为了在模板中完成一些特殊功能

语法：{% 标签名 %}	（一些标签还需要搭配结束标签 {% endtag %}）

### **3.1、for 标签**

#### **3.1.1、遍历所有元素**

```python
# 普通循环遍历
{% for value in value_list %}
    <p>{{ value }}</p>
{% endfor %}

# 反向循环遍历
{% for value in value_list reversed %}
    <p>{{ value }}</p>
{% endfor %}
```

```python
# view.py

from django.shortcuts import HttpResponse, render
import datetime

def index(request):
    li = ["a", "b", "c", "d"]
    return render(request, "index.html", locals())
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
	<!-- 结果为 a b  c d-->
    {% for ele in li %}
        {{ ele }}
    {% endfor %}


	<!-- 结果为 d c b a -->
    {% for ele in li reversed %}
        {{ ele }}
    {% endfor %}

</body>
</html>
```

#### **3.1.2、遍历字典**

```
{% for key,val in dic.items %}
    <p>{{ key }}:{{ val }}</p>
{% endfor %}
```

```python
# views.py
from django.shortcuts import HttpResponse, render

def index(request):
    dic = {"a": "AAA", "b": "BBB", "c": "CCC"}
    return render(request, "index.html", locals())
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
	
    <!-- 结果为 a b c -->
	{% for key in dic %}
        {{ key }}
    {% endfor %}

	
   <!-- 结果为 ('a','AAA')  ('b','BBB')  ('c','CCC') -->
    {% for ele in dic.items %}
        {{ ele }}
    {% endfor %}
    
    
    <!-- 结果为 a:AAA  b:BBB  c:CCC -->
    {% for key,value in dic.items %}
        {{ key }}:{{ value }}
    {% endfor %}
  
</body>
</html>
```

#### **3.1.3、循环的序号**

```
# 循环序号可以通过{{ forloop }}显示　
forloop.counter            当前循环的索引值（从1开始）
forloop.counter0           当前循环的索引值（从0开始）
forloop.revcounter         当前循环的倒序索引值（从1开始）
forloop.revcounter0        当前循环的倒序索引值（从0开始）
forloop.first              当前循环是第一次循环则返回True，否则返回False
forloop.last               当前循环是最后一次循环则返回True，否则返回False
forloop.parentloop         本层循环的外层循环
```

```python
# views.py

from django.shortcuts import HttpResponse, render

def index(request):
    li = ["a", "b", "c", "d"]
    li2 = [["a", "b", "c"], [1, 2, 3], ["a", "o", "e"]]
    return render(request, "index.html", locals())
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>

    {% for ele in li %}
        <p>循环次数-{{ forloop.counter0 }}，值-{{ ele }}，是否是第一次循环：{{ forloop.first }}，是否是最后一次循环-{{ forloop.last }}</p>
    {% endfor %}
		

    {% for li in li2 %}
        <div style="margin-bottom: 50px">
            <p>这是外层循环-元素为{{ li }}</p>
            {% for ele in li %}
                <p>这是内层循环-元素为{{ ele }}，外层循环信息：{{ forloop.parentloop }}</p>
            {% endfor %}
        </div>

    {% endfor %}

</body>
</html>
```

```
# 结果为

循环次数-0，值-a，是否是第一次循环：True，是否是最后一次循环-False
循环次数-1，值-b，是否是第一次循环：False，是否是最后一次循环-False
循环次数-2，值-c，是否是第一次循环：False，是否是最后一次循环-False
循环次数-3，值-d，是否是第一次循环：False，是否是最后一次循环-True



这是外层循环-元素为['a', 'b', 'c']
这是内层循环-元素为a，外层循环信息：{'parentloop': {}, 'counter0': 0, 'counter': 1, 'revcounter': 3, 'revcounter0': 2, 'first': True, 'last': False}
这是内层循环-元素为b，外层循环信息：{'parentloop': {}, 'counter0': 0, 'counter': 1, 'revcounter': 3, 'revcounter0': 2, 'first': True, 'last': False}
这是内层循环-元素为c，外层循环信息：{'parentloop': {}, 'counter0': 0, 'counter': 1, 'revcounter': 3, 'revcounter0': 2, 'first': True, 'last': False}

这是外层循环-元素为[1, 2, 3]
这是内层循环-元素为1，外层循环信息：{'parentloop': {}, 'counter0': 1, 'counter': 2, 'revcounter': 2, 'revcounter0': 1, 'first': False, 'last': False}
这是内层循环-元素为2，外层循环信息：{'parentloop': {}, 'counter0': 1, 'counter': 2, 'revcounter': 2, 'revcounter0': 1, 'first': False, 'last': False}
这是内层循环-元素为3，外层循环信息：{'parentloop': {}, 'counter0': 1, 'counter': 2, 'revcounter': 2, 'revcounter0': 1, 'first': False, 'last': False}

这是外层循环-元素为['a', 'o', 'e']
这是内层循环-元素为a，外层循环信息：{'parentloop': {}, 'counter0': 2, 'counter': 3, 'revcounter': 1, 'revcounter0': 0, 'first': False, 'last': True}
这是内层循环-元素为o，外层循环信息：{'parentloop': {}, 'counter0': 2, 'counter': 3, 'revcounter': 1, 'revcounter0': 0, 'first': False, 'last': True}
这是内层循环-元素为e，外层循环信息：{'parentloop': {}, 'counter0': 2, 'counter': 3, 'revcounter': 1, 'revcounter0': 0, 'first': False, 'last': True}
```

#### **3.1.4、for 和 empty**

for标签可以带有一个可选的{% empty %} 从句，在变量为空或者没有被找到时，则执行empty子句

```
{% for ele in ele_list %}
    <p>{{ ele }}</p>
{% empty %}
    <p>sorry,no element here</p>
{% endfor %}
```

```python
# views.py

from django.shortcuts import HttpResponse, render

def index(request):
    li_1 = ["a", "b", "c", "d"]
    li_2 = []
    return render(request, "index.html", locals())
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>
	
    {% for ele in li_1 %}
    	<p>{{ ele }}</p>
    {% empty %}
         <p>sorry,no element here</p>
    {% endfor %}


    {% for ele in li_2 %}
        <p>{{ ele }}</p>
    {% empty %}
         <p>sorry,no element here</p>
    {% endfor %}	
  
</body>
</html>
```

### **3.2、if 标签**

```
# 1、注意：
{% if 条件 %}条件为真时if的子句才会生效，条件也可以是一个变量，if会对变量进行求值，在变量值为空、或者视图没有为其传值的情况下均为False

# 2、具体语法
{% if num > 100 or num < 0 %}
    <p>无效</p>
{% elif num > 80 and num < 100 %}
    <p>优秀</p>
{% else %}
    <p>凑活吧</p>
{% endif %}

# 3、if语句支持 and 、or、==、>、<、!=、<=、>=、in、not in、is、is not判断。
```

```python
# views.py

from django.shortcuts import HttpResponse, render

def index(request):
    name = "cdc"
    user = []
    black = ["aaa", "bbb", "ccc", "ddd"]

    return render(request, "index.html", locals())
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>

    <!-- 普通判断 -->
    {% if name == "cdc" %}
        用户名正确
    {% else %}
        用户名错误
    {% endif %}


    <!-- 多分支判断 -->
    {% if user %}
        用户人数为{{ user|length }}
    {% elif black %}
        黑名单人数为{{ black|length }}
    {% else %}
        没有查到对应的信息
    {% endif %}

</body>
</html>
```

**注意： Django的模板语言不支持连续判断，即不支持以下写法**

```
{% if a > b > c %}
...
{% endif %}
```

### **3.3、with 标签**

定义一个中间变量，多用于给一个复杂的变量起别名。如果有赋值操作，注意等号左右不要加空格。

```
{% with total=business.employees.count %}
    {{ total }} employee{{ total|pluralize }}
{% endwith %}

或者

{% with business.employees.count as total %}
    {{ total }} employee{{ total|pluralize }}
{% endwith %}
```

### **3.4、csrf_token标签**

​		csrf_token标签用于防止伪造跨域请求保护。在实际的项目中，如果用户浏览器通过**post请求**发送了一些数据，在不做特殊的处理的情况下，django无法知道这些数据是不是有害数据，接收该请求会不会对真个服务器造成危害，为了防止这种情况，会对该请求进行跨域伪造，该post请求就会请求失败，我们也无法直接获取到发送过来的数据。

```python
# urls.py

from django.conf.urls import url
from Django_templates import views

urlpatterns = [
    url(r"^login", views.login),
]
```

```html
<!-- templates/login.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登陆页面</title>
</head>
<body>
<form action="/login/" method="post">
    <p>用户名：<input type="text" name="name"></p>
    <p>密  码：<input type="password" name="pwd"></p>
    <p><input type="submit" value="登录"></p>
</form>
</body>
</html>
```

```python
# views.py

from django.shortcuts import HttpResponse, render

def login(request):
    if request.method == "POST":
        user = request.POST.get("name")
        pwd = request.POST.get("pwd")
        if user == "cdc" and pwd == "123456":
            return HttpResponse("欢迎登录")

    return render(request, "login.html")
```

```
启动项目，访问 http://127.0.0.1:8000/login/ ，输入用户名密码，点击登录，发现请求失败，浏览器显示

禁止访问 (403)
CSRF验证失败. 请求被中断.
```

我们可以通过以下两种方式来解决跨域问题（任选一种）：

- 修改项目配置文件settings.py中的中间件

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    #  'django.middleware.csrf.CsrfViewMiddleware',  #  注释掉此行就不会发生跨域问题了
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

- 通过csrf_token标签

```html
<!-- templates/login.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登陆页面</title>
</head>
<body>

<!-- 当用form表单提交POST请求时必须加上标签{% csrf_token%}，该标签用于防止跨站伪造请求 -->
<form action="/login/" method="post">
	{% csrf_token %}
    <p>用户名：<input type="text" name="name"></p>
    <p>密  码：<input type="password" name="pwd"></p>
    <p><input type="submit" value="登录"></p>
</form>
</body>
</html>
```

`csrf_token` 标签具体工作原理为:

1. 在GET请求到form表单时，标签{% csrf_token%}会被渲染成一个隐藏的input标签，该标签包含了由服务端生成的一串随机字符串
2. 在使用form表单提交POST请求时，会提交上述随机字符串，服务端在接收到该POST请求时会对比该随机字符串，对比成功则处理该POST请求，否则拒绝，以此来确定客户端的身份

```html
<!-- 加上csrf_token标签后，实际上的login.html的代码如下 -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登陆页面</title>
</head>
<body>

<form action="/login/" method="post">
	<!-- 隐藏的input标签，该标签包含了由服务端生成的一串随机字符串 -->
	<input type="hidden" name="csrfmiddlewaretoken" value="FwdFXQsqgX0XpAphwjWojA45TPYQsnUS7ZKhpi3hhZZxlAzl41Nc1sVQQSORrUFb">  
    <p>用户名：<input type="text" name="name"></p>
    <p>密  码：<input type="password" name="pwd"></p>
    <p><input type="submit" value="登录"></p>
</form>
</body>
</html>
```

## 四、自定义过滤器和标签

当Django内置的过滤器或者标签无法满足我们的需求时，我们可以自定义过滤器和标签，步骤如下：

**第一步：**

检查要自定义标签或过滤器的app是否已经在配置文件中注册，如果没有注册就注册以下，不然django无法找到自定义的过滤器或标签

```python
# settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    "app01.apps.App01Config",  # app01为新创建的app
]
```

**第二步：**

在文件夹 app01中创建子文件夹 templatetags，**注意子文件夹的名字只能是templatetags**

**第三步：**

在 templatetags 新建任意 .py 文件，如 my_tags.py，在该文件中自定义过滤器或标签

```python
# app01/templatetags/my_tags.py

from django import template

register = template.Library()  # 变量名必须为register


# 自定义过滤器
# @register.filter(name="MyFilter")  # name是可选参数，如果使用了，在模板中使用过滤器时就要用name的值；不使用，过滤器就是函数名
@register.filter
def my_filter(v1, v2):
    # 自定义的过滤器最多只能接收两个参数
    # {{ value1|my_filter:value2 }}，其中 v1 = value1  v2 = value2
    return v1 * v2


# 自定义标签
# @register.simple_tag(name="MyTag")  # name是可选参数，如果使用了，在模板中使用标签时就要用name的值；不使用，标签名就是函数名
@register.simple_tag
def my_tag(v1, v2, v3):
    # 自定义标签可以接收多个值
    return (v1 + v2) * v3

# 用于返回 html 片段，括号内的参数就是html片段存放的文件
@register.inclusion_tag('result.html')
def show_results(n):
    n = 1 if n < 1 else int(n)
    data = ["第{}项".format(i) for i in range(1, n+1)]
    return {"data": data}


# 自定义标签 mark_safe
# 我们可以用内置的标签safe来让标签内容有语法意义，如果我们想让自定义标签处理的结果也有语法意义，则不能使用内置标签safe了，需要使用mark_safe，可以实现与内置标签safe同样的功能
from django.utils.safestring import mark_safe


@register.simple_tag
def my_input_tag(id, name):
    res = "<input type='text' id='%s' name='%s' />" % (id, name)
    return mark_safe(res)
```

```python
# app01/views.py

from django.shortcuts import render


def index(request):
    data_1 = 10
    data_2 = 5
    id = "inp1"
    name = "username"
    return render(request, "index.html", locals())
```

```python
# urls.py
from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r"^app01/index/$", views.index),

]
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>首页</title>
</head>
<body>

    <!-- 必须先加载存有自定义过滤器和标签的文件 -->
    {% load my_tags %}

    <!-- data_1的值是10，经过自定义过滤器处理的结果为 100 -->
    {{ data_1|my_filter:10 }}

    <!-- data_2的值是5，经过自定义标签处理的结果为 12 -->
    {% my_tag data_2 1 2 %}

    <!-- input标签的id是'inp1'，name属性的值是'username' -->
    {% my_input_tag id name %}
    
</body>
</html>
```

自定义标签和过滤器对比：

- 自定义过滤器只能传两个值，而自定义标签可以传多个
- 自定义过滤器可以使用 if 判断，而自定义标签不能

**补充：使用自定义标签返回html片段**

```python
# app01/templatetags/my_tags.py

from django import template

register = template.Library()


@register.inclusion_tag('result.html')
def show_results(n):
    n = 1 if n < 1 else int(n)
    data = ["第{}项".format(i) for i in range(1, n+1)]
    return {"data": data}
```

```html
<!-- templates/snippets/result.html -->

<ul>
  {% for choice in data %}
    <li>{{ choice }}</li>
  {% endfor %}
</ul>
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="x-ua-compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>inclusion_tag test</title>
</head>
<body>

{% load inclusion_tag_test %}

{% show_results 10 %}
</body>
</html>
```

## 五、模板的导入和继承

​		在项目开发过程中，模板中可能会使用大量的相同的代码，这就会导致造成大量的代码重复使用和冗余的问题。我们可以将代码和功能重复的地方进行抽取和封装（类似面向对象的思想），需要使用时，我们再去调用，这就是Django中的模板继承思想。

### **5.1、模板（组件）的导入**

```
# 作用：在一个模板文件中，引入/重用另外一个模板文件的内容，我们通常把一些常用的特定功能写在模板中方便使用，所以这些模板也叫组件
{% include '模版名称' %}
```

```html
<!-- templates/base.html -->

<div class="head" style="height: 50px;text-align: center;font-weight: bolder;font-size: 30px;color: black; background-color: red;">
    <div class="text">这是导航栏</div>
</div>
```

```python
# urls.py
from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r"^app01/index/$", views.index),

]
```

```python
# app01/views.py

from django.shortcuts import render

def index(request):
    return render(request, "index.html")
```

```html
<!-- templates/index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
	<!--在index.html引入base.html文件的内容-->
    {% include "base.html" %}
</body>
</html>
```

测试可以看到，虽然index页面可以显示和base页面一样的内容，但是index页面中并灭有写那些代码。

### **5.2、模板的继承与派生**

```
#  作用：在一个模板文件中，引入/重用另外一个模板文件的内容
{% extends "模版名称" %}
#  注意：include有的功能extends全都有，但是extends可以搭配一个block标签，用于在继承的基础上定制新的内容
```

Django模版引擎中最复杂且最强大的部分就是模版继承了。我们以先创建一个基本的“骨架”模版，它包含我们站点中的全部元素，并且可以定义多处blocks 。

首先我们先定义一个用户填写注册信息的基础页面：

```html
<!-- templates/register_base.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>
        {% block title %}
            注册页面的基础模板
        {% endblock %}
    </title>
</head>
<body>
<form action="/app01/register/" method="post">
    <p style="font-size: 30px;font-weight: bolder">用户名密码信息</p>
    <hr>
    <p>
        <label for="user_name"></label>用户名：
        <input type="text" name="user_name" id="user_name" placeholder="请输入用户名">
    </p>

    <p>
        <label for="pwd"></label>密 码：
        <input type="password" name="pwd" id="pwd" placeholder="请设置密码">
    </p>
    <p style="font-size: 30px;font-weight: bolder">设置个人资料</p>
    <hr>
    {% block personal_info %}
        <p>
            <label for="phone"></label>手机号：
            <input type="text" name="phone" id="phone" placeholder="请输入手机号">
        </p>
        <p>
            男<input type="radio" value="male" name="gender">
            女<input type="radio" value="female" name="gender">
        </p>
    {% endblock %}
</form>

{% block content %}
    <p>
        基础模板中的内容
    </p>
{% endblock %}
</body>
</html>
```

我们新建子模板register.html的主要工作就是继承基础模板l然后填充/覆盖其内部的blocks。

```html
<!-- templates/register.html -->

{% extends "register_base.html" %}

<!--用新内容完全覆盖了父模板内容-->
{% block title %}
    用户注册详细页面
{% endblock %}


{% block personal_info %}
    <!--该变量会将父模板中sidebar中原来的内容继承过来，然后我们可以在此基础上新增，否则就是纯粹地覆盖-->
    {{ block.super }}

    唱<input type="checkbox" name="hobby" value="sing">
    条<input type="checkbox" name="hobby" value="dance">
    rap<input type="checkbox" name="hobby" value="rap">
    篮球<input type="checkbox" name="hobby" value="basketball">

{% endblock %}


{% block content %}
    <!--用新内容完全覆盖了父模板内容-->
    <p>详细注册页面内容</p>
{% endblock %}
```

我们通过django访问register.html，实际上看到内容如下(block标签的内容都完成了替换或更新)

```html
<!DOCTYPE html>
<!-- saved from url=(0037)http://127.0.0.1:8000/app01/register/ -->
<html lang="en"><head><meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    
    <title>
        
    用户注册详细页面

    </title>
</head>
<body>
<form action="http://127.0.0.1:8000/app01/register/" method="post">
    <p style="font-size: 30px;font-weight: bolder">用户名密码信息</p>
    <hr>
    <p>
        <label for="user_name"></label>用户名：
        <input type="text" name="user_name" id="user_name" placeholder="请输入用户名">
    </p>

    <p>
        <label for="pwd"></label>密 码：
        <input type="password" name="pwd" id="pwd" placeholder="请设置密码">
    </p>
    <p style="font-size: 30px;font-weight: bolder">设置个人资料</p>
    <hr>
    
    <!--该变量会将父模板中sidebar中原来的内容继承过来，然后我们可以在此基础上新增，否则就是纯粹地覆盖-->
    
        <p>
            <label for="phone"></label>手机号：
            <input type="text" name="phone" id="phone" placeholder="请输入手机号">
        </p>
        <p>
            男<input type="radio" value="male" name="gender">
            女<input type="radio" value="female" name="gender">
        </p>
    

    唱<input type="checkbox" name="hobby" value="sing">
    条<input type="checkbox" name="hobby" value="dance">
    rap<input type="checkbox" name="hobby" value="rap">
    篮球<input type="checkbox" name="hobby" value="basketball">


</form>


    <!--用新内容完全覆盖了父模板内容-->
    <p>详细注册页面内容</p>


</body></html>
```

**总结与注意：**

```python
# 1、标签extends必须放在首行，base.html中block越多可定制性越强

# 2、include仅仅只是完全引用其他模板文件，而extends却可以搭配block在引用的基础上进行扩写

# 3、变量{{ block.super }} 可以重用父类的内容，然后在父类基础上增加新内容，而不是完全覆盖

# 4、为了提升可读性，我们可以给标签{% endblock %} 起一个名字 。例如：
    {% block content %}
    ...
    {% endblock content %}　　
# 5、在一个模版中不能出现重名的block标签。
```

### **5.3、静态文件相关**

我们在编写模板文件时，需要大量引用css、js、图片等静态文件，如果我们将这些文件在服务端存放的路径都固定写死那么将非常不利于后期的扩展，我们可以这么做

**第一步：**

在settings.py中配置相关的静态文件目录

```python
STATIC_URL = '/static/' # 找到这一行，然后新增下述代码
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'statics'),# 获取静态文件在服务端的绝对路径
]

# STATIC_URL = '/static/'就是为静态文件的绝对路径起了一个别名，以后我们只需要用路径/static/即可
```

**第二步：**

在项目根目录下新增文件夹statics，为了更便于管理，可以在statics下新建子文件夹css、js、img等

**第三步：**

新建模板文件index.html,在该文件中对静态文件的引用如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    
    <!-- 引用css文件 -->
    <link rel="stylesheet" href="/static/css/my.css">
    
</head>
<body>
<h4>我是红色的，点我就绿</h4>

 <!-- 引用图片 -->
<img src="/static/img/rb.jpeg" alt="">

 <!-- 引用js文件 -->
<script src="/static/js/jquery-3.3.1.min.js"></script>
<script src="/static/js/my.js"></script>

</body>
</html>
```

​		综上，在配置完settings.py后，所有的静态文件路径都可以采用别名/static/作为起始，这在一定程度上会有利于程序的扩展性，但如果我们在项目后期维护时，连/static/这个值也需要修改，那意味着所有模板文件中也都需要跟着改了，扩展性依然很差，为此，django在一个名为static.py的文件中定义了标签static、get_static_prefix，二者都可以解决该问题

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--注意：必须先加载文件static.py-->
    {% load static %}
    <!--注意：此处的static是一个定义在static.py中的一个标签，名字与文件名一样而已，不要搞混-->
    <link rel="stylesheet" href="{% static 'css/my.css' %}">
</head>
<body>
<h4>我是红色的，点我就绿</h4>
<img src="{% static 'img/rb.jpeg' %}" alt="">

<!-- 某个文件被多处引用，也可以将该文件的引用存为一个变量>
{% load static %}
{% static "images/hi.jpg" as myphoto %}
<img src="{{ myphoto }}"></img>

{% load static %}
<script src="{% static 'js/jquery-3.3.1.min.js' %}"></script>
<script src="{% static 'js/my.js' %}"></script>

</body>
</html>
```

​		标签static会接收传入的参数，然后这根据settings.py中变量 STATIC_URL 的值拼接出一个完整的路径，如果STATIC_URL = '/static/'，那么href = "{% static 'css/my.css' %}" 会被渲染成href="/static/css/my.css"，如果 STATIC_URL  =  '/static123/'，那么 href = "{% static 'css/my.css' %}"会被渲染成href="/static123/css/my.css"。

​		标签get_static_prefix也可以完成同样的效果，只不过用法不同。我们不能为标签get_static_prefix传参，因为标签get_static_prefix代表的只是settings.py中STATIC_URL的值，所以我们需要做的是在get_static_prefix的基础上自行拼接路径，如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--注意：同样需要先加载文件static.py-->
    {% load static %}
    <!--使用标签get_static_prefix拼接路径-->
    <link rel="stylesheet" href="{% get_static_prefix %}css/my.css">
</head>
<body>
<h4>我是红色的，点我就绿</h4>
<img src="{% get_static_prefix %}img/rb.jpeg" alt="">


{% load static %}
<script src="{% get_static_prefix %}js/jquery-3.3.1.min.js"></script>
<script src="{% get_static_prefix %}js/my.js"></script>

</body>
</html>
```

如果STATIC_URL = '/static/'，那么href="{% get_static_prefix %}css/my.css"会被渲染成href="/static/css/my.css"，其它同理。