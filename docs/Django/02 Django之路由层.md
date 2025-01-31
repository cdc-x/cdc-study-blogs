# Django之路由层

## 一、路由层简介

### **1.1 路由介绍**

​		在上一章介绍的 Django 生命周期中，我们知道了浏览器发出一个请求，在经过 wsgiref 和中间件后，就到达了路由层。那路由层的作用是啥呢？如果我们把一个 django 项目当作一本书，那么路由层相当于就是书的目录，我们可以根据目录查找到相关的章节位置，同样在 django 中，也是根据路由层来找到对应的视图函数。路由，就是请求地址和视图函数的映射关系。

### **1.2 简单的路由配置**

Django 中路由层的默认配置在 `urls.py` 文件中：

```python
# urls.py 
from django.conf.urls import url

# 由一条条映射关系组成的urlpatterns这个列表称之为路由表
urlpatterns = [
     url(regex, view, kwargs=None, name=None), # url本质就是一个函数
]
```

参数介绍：

- `regex`：正则表达式,用来匹配 url 地址的路径部分，例如地址为：http://127.0.0.1:8001/index/，正则表达式要匹配的部分是index/
- `view`：通常为一个视图函数，用来处理业务逻辑
- `kwargs`：用于在路由中传递参数
- `name`：用于路由反向解析

简单使用案例：

```python
# urls.py

from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'index/', views.index),
]
```

```python
# views.py

from django.shortcuts import HttpResponse

def index(request):
    return HttpResponse("这是index")
```

启动项目，在浏览器访问 http://127.0.0.1:8000/index/，会看到页面显示  "这是index"。

 在使用路由时，有以下几点要注意：

#### **1.2.1 路由匹配**

​		当我们在浏览器中输入 http://127.0.0.1:8000/index，django 会拿着路径部分 "index" 去路由表中自上而下匹配，一旦匹配到了就不会继续往下匹配了，例如：

```python
# urls.py

from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'index/', views.index),
    url(r'index/2', views.index2),
]
```

```python
# views.py

from django.shortcuts import HttpResponse


def index(request):
    return HttpResponse("这是index")


def index2(request):
    return HttpResponse("这是index2")
```

启动项目，在浏览器访问 http://127.0.0.1:8000/index/2，还是会看到  "这是index"，这是因为 django 匹配到 "index/" 时，就已经可以找到对应的路由了，所以不会继续去匹配了后面的 "2" 了，可以通过如下方式解决

```python
# urls.py

from django.conf.urls import url
from . import views

urlpatterns = [
    # 路由调换位置，将有包含关系且比较长的放在前面（不推荐）
    # url(r'^index/2', views.index2),
    # url(r'^index/', views.index),
    
    # 使用正则表达式匹配时，匹配开头和结尾
    url(r'^index/$', views.index),
    url(r'^index/2$', views.index2), 
]
```

#### **1.2.2 路由重定向**

​		当我们在浏览器中输入 http://127.0.0.1:8000/index，django 会拿着路径部分 "index" 去路由表中自上而下匹配，按理来说应该找不到对应的路由，因为路由表中的路由都是以 "/" 结尾的，但是我们还是可以顺利的匹配上，这是因为在配置文件 settings.py 中有一个参数 `APPEND_SLASH`，该参数有两个值True或False（如果配置文件中没有该配置，APPEND_SLASH的默认值为True），对应的作用如下：

- 当 `APPEND_SLASH=True`，并且用户请求的 url 地址的路径部分不是以 "/" 结尾，例如请求的 url 地址是  http://127.0.0.1:8001/index，Django会拿着路径部分（即index）去路由表中匹配正则表达式，发现匹配不成功，那么Django会在路径后加 "/"（即index/）再去路由表中匹配，如果匹配失败则会返回路径未找到，如果匹配成功，则会返回重定向信息给浏览器，要求浏览器重新向 http://127.0.0.1:8001/index/ 地址发送请求。
- 当 `APPEND_SLASH=False` 时，则不会执行上述过程，即一旦 url 地址的路径部分匹配失败就立即返回路径未找到，不会做任何的附加操作。
- 注意，在末尾加 "/" 然后重新发起请求，这是浏览器的功能。

## 二、路由分组

有时候，我们需要的一些信息或者数据可以直接从请求的 url 中获得，例如：http://127.0.0.1:8080/birthday/1999/11/03，其中出生日期都包含在 url 中了，此时我们就可以使用正则表达式的分组来进行操作取出信息。

### **2.1 无名分组**

下述正则表达式会匹配 url 地址的路径部分为: article/数字/数字/数字，匹配成功的分组部分会以位置参数的形式传给视图函数，有几个分组就传几个位置参数

```python
# urls.py

from django.conf.urls import url
from . import views

urlpatterns = [  
    url(r"^birthday/(\d+)/(\d+)/(\d+)$", views.get_birthday),
]
```

```python
# views.py

from django.shortcuts import HttpResponse

def get_birthday(request, year, month, day):
    year = year
    month = month
    day = day
    return HttpResponse(f"我的生日是 {year}-{month}-{day}")
```

测试：启动项目，浏览器输入 http://127.0.0.1:8000/birthday/1999/11/03，可以看到结果 "我的生日是 1999-11-03"

### **2.2 有名分组**

下述正则会匹配 url 地址的路径部分为: article/数字/数字/数字，匹配成功的分组部分会以关键字参数（birth_year=匹配成功的数字）的形式传给视图函数，有几个有名分组就会传几个关键字参数

```python
# urls.py

from django.conf.urls import url
from . import views

urlpatterns = [
    url(r"^birthday/(?P<birth_year>\d+)/(?P<birth_month>\d+)/(?P<birth_day>\d+)$", views.get_birthday),
]
```

使用有名分组时，视图函数的位置参数的名字必须和传入的关键字参数相同。

```python
# views.py

from django.shortcuts import HttpResponse

# def get_birthday(request, year, month, day):
#     year = year
#     month = month
#     day = day
#     return HttpResponse(f"我的生日是 {year}-{month}-{day}")

    # TypeError: get_birthday() got an unexpected keyword argument 'brith_year'


def get_birthday(request, birth_year, birth_month, birth_day):
    year = birth_year
    month = birth_month
    day = birth_day
    return HttpResponse(f"我的生日是 {year}-{month}-{day}")
```

注意：有名分组和无名分组都是为了获取路径中的参数，并传递给视图函数，区别在于无名分组是以位置参数的形式传递，有名分组是以关键字参数的形式传递。有名分组和无名分组不能混合使用。

## 三、路由分发

随着项目功能的增加，`app` 会越来越多，路由也越来越多，每个 `app` 都会有属于自己的路由，如果再将所有的路由都放到一张路由表中，会导致结构不清晰，不便于管理，所以我们应该将 `app` 自己的路由交由自己管理，然后在总路由表中做分发。

1. 创建多个 `app`，并注册

```
# 终端创建 app01
F:\学习代码\python\框架篇\Django之路由层>python manage.py startapp app01

# 终端创建 app02
F:\学习代码\python\框架篇\Django之路由层>python manage.py startapp app02
```

```python
# settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app01.apps.App01Config',	# 注册app01
    'app02.apps.App02Config'	# 注册app02
]
```

2. 在每个 `app` 下创建 `ursl.py`  存放自己模块的路由，在自己模块下的 `views.py` 编写自己的接口


```python
# app01 下的 urls.py

from django.conf.urls import url
from app01 import views
# 导入app01的Views
urlpatterns = [
    url(r"^index/$", views.index)
]
```

```python
# app01 下的 views.py

from django.shortcuts import render, HttpResponse

def index(request):
    return HttpResponse("这是app01的index")
```

```python
# app02 下的 urls.py

from django.conf.urls import url
from app02 import views
# 导入app02的Views
urlpatterns = [
    url(r"^index/$", views.index)
]
```

```python
# app02 下的 views.py

from django.shortcuts import render, HttpResponse

def index(request):
    return HttpResponse("这是app02的index")
```

3. 在总的 urls.py 文件中分发路由

```python
from django.conf.urls import url, include
from . import views

urlpatterns = [
    url(r"^app01/", include("app01.urls")),
    url(r"^app02/", include("app02.urls")),
]
```

测试：

```python
# 在浏览器输入：http://127.0.0.1:8000/app01/index/ 会看到"我是app01的index"
# 在浏览器输入：http://127.0.0.1:8000/app02/index/ 会看到"我是app02的index页面"
```

**注意：**

- 在总的路由配置 urls.py 中，对于分发的路由正则匹配一定不能加 `$`
-  include 函数就是做分发操作的，当在浏览器输入 http://127.0.0.1:8001/app01/index/时，会先进入到总路由表中进行匹配，正则表达式 r'^app01/' 会先匹配成功路径 app01/，然后include功能会去app01下的urls.py中继续匹配剩余的路径部分，这也就是上一条中所说的正则匹配规则一定不能加 $ 的原因。

## 四、别名和反向解析

### **4.1 别名**

​		在软件开发初期，url地址的路径设计可能并不完美，后期需要进行调整，如果项目中很多地方使用了该路径，一旦该路径发生变化，就意味着所有使用该路径的地方都需要进行修改，这是一个非常繁琐的操作。解决方案就是在编写一条 url(regex, view, kwargs=None, name=None) 时，可以通过参数 name 为 url 地址的路径部分起一个别名，项目中就可以通过别名来获取这个路径。以后无论路径如何变化，别名与路径始终保持一致。这样的好处就是无论 url 后期怎么修改，只要通过它的别名使用它，就能找到它。

### **4.2 反向解析**

将别名解析成对应的 url 的过程叫做反向解析

案例：判断用户登录名密码是否正确，正确就跳到首页

```python
# urls.py

from django.conf.urls import url, include
from . import views

urlpatterns = [
    url(r"^index/", views.index, name="index_page"),  # 路径 index/ 别名为 index_page
    url(r"^login/", views.login, name="login_page"),  # 路径 login/ 别名为 login_page
]

```

```python
# views.py

from django.shortcuts import HttpResponse, render, reverse, redirect

def login(request):
    if request.method == "GET":
        return render(request, "login.html")

    name = request.POST.get("username")
    print(name)
    pwd = request.POST.get("pwd")
    print(pwd)

    if name == "cdc" and pwd == "123456":
        url = reverse("index_page")  # 视图函数中反向解析别名，会将别名解析成 /index/
        print(url)  # /index/
        return redirect(url)  # 等价于 redirect("/index/")
    else:
        return HttpResponse("用户名或者密码错误")


def index(request):
    return render(request, "index.html")
```

```html
# templates/index.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>欢迎登录。。。。这是首页</h1>
</body>
</html>
```

```html
# templates/login.html

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1 style="text-align: center">登录页面</h1>
{#    在模板中反向解析别名#}
{#    别名必须用引号包住#}
    <form action="{% url 'login_page' %}" method="post">
        {% csrf_token %}
        用户名：<input type="text" name="username">
        密码：<input type="password" name="pwd">
        <input type="submit" value="登录">
    </form>
</body>
</html>
```

总结：

```python
# 在 views.py 中，反向解析的使用：    url = reverse('index_page') 
# 在模版 login.html 文件中，反向解析的使用  {% url 'login_page' %}
```

### **4.3 分组中反向解析**

```python
from django.conf.urls import url
from . import views
urlpatterns = [
   	url(r"^birthday/(\d+)/(\d+)/(\d+)$", views.get_birthday, name="birth_page"), #　无名分组
    url(r"^birthday/(?P<birth_year>\d+)/(?P<birth_month>\d+)/(?P<birth_day>\d+)$", views.get_birthday, name="birth_page"), # 有名分组
]
```

```python
# 1 针对无名分组，比如我们要反向解析出：/birthday/1999/11/03 这种路径，写法如下
## 在views.py中，反向解析的使用：
    url = reverse('birth_page',args=(year, month, day))  # 接收三个位置参数 
    
## 在模版文件中，反向解析的使用
    {% url 'birth_page' 1999 11 03 %}

    
# 2 针对有名分组，比如我们要反向解析出：/birthday/1999/11/03 这种路径，写法如下
## 在views.py中，反向解析的使用：
    url = reverse('birth_page',kwargs={'birth_year':1999, 'birth_month':11, 'birth_day':03}) 
    
## 在模版文件中，反向解析的使用
    {% url 'birth_page' birth_year=1999 birth_month=11 birth_day=03 %}
```

## 五、名称空间

当我们的项目下创建了多个 app，并且每个 app 下都针对匹配的路径起了别名，如果别名存在重复，那么在反向解析时则会出现覆盖。

案例：

创建两个 app，app03，app04，并注册

```
F:\学习代码\python\框架篇\Django之路由层>python manage.py startapp app03

F:\学习代码\python\框架篇\Django之路由层>python manage.py startapp app04
```

```python
# settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app01.apps.App01Config',
    'app02.apps.App02Config',
    'app03.apps.App03Config',
    'app04.apps.App04Config'
]
```

编写各自的路由和视图函数

```python
# app03 urls.py

from django.conf.urls import url
from app03 import views

urlpatterns = [
    url(r"^index/$", views.index, name="index_page")
]


# app03 views.py
from django.shortcuts import render, HttpResponse, reverse

def index(req):
    return HttpResponse("这是app03的index页面，反向解析的结果为 %s" % reverse("index_page"))

```

```python
# app04 urls.py

from django.conf.urls import url
from app04 import views

urlpatterns = [
    url(r"^index/$", views.index, name="index_page")
]


# app04 views.py
from django.shortcuts import render, HttpResponse, reverse

def index(req):
    return HttpResponse("这是app04的index页面，反向解析的结果为 %s" % reverse("index_page"))
```

在总路由配置模块进行路由分发

```python
# urls.py

from django.conf.urls import url, include
from Django之路由层 import views

urlpatterns = [
    url(r"^app03/", include("app03.urls")),
    url(r"^app04/", include("app04.urls")),

]
```

启动项目进行测试，发现 app04 的别名解析将 app03 的覆盖了

```
在浏览器输入 http://127.0.0.1:8000/app03/index/  看到结果 这是app03的index页面，反向解析的结果为 /app04/index/

在浏览器输入 http://127.0.0.1:8000/app04/index/  看到结果 这是app04的index页面，反向解析的结果为 /app04/index/
```

解决方法：

- 避免使用相同的别名
- 如果就想使用相同的别名，那就需要用到django中名称空间的概念，将别名放到不同的名称空间中，这样即便是出现重复，彼此也不会冲突，具体做法如下

1. 总的 urls.py 在分发路由时，指定名称空间

```python
from django.conf.urls import url, include
from . import views

urlpatterns = [
	# 传给 include 一个元组，元组的第一个值是路由分发的地址，第二个值是我们为名称空间取的名字
    url(r"^app03/", include("app03.urls", "app03")),
    url(r"^app04/", include("app04.urls", "app04")),
]
```

2. 修改每个 app 中 views.py 中的函数，针对不同名称空间中的别名 index_page 进行反向解析

```python
# app03 views.py

from django.shortcuts import render, HttpResponse, reverse

def index(req):
    url = reverse("app03:index_page")
    return HttpResponse("这是app03的index页面，反向解析的结果为 %s" % url)
```

```python
# app04 views.py

from django.shortcuts import render, HttpResponse, reverse

def index(req):
    url = reverse("app04:index_page")
    return HttpResponse("这是app04的index页面，反向解析的结果为 %s" % url)
```

启动项目进行测试

```
在浏览器输入 http://127.0.0.1:8000/app03/index/  看到结果 这是app03的index页面，反向解析的结果为 /app03/index/

在浏览器输入 http://127.0.0.1:8000/app04/index/  看到结果 这是app04的index页面，反向解析的结果为 /app04/index/
```

补充：

```
1、在视图函数中基于名称空间的反向解析，用法如下
url=reverse('名称空间的名字:待解析的别名')

2、在模版里基于名称空间的反向解析，用法如下
{% url '名称空间的名字:待解析的别名'%}
```

## 六、path 和 re_path

### **6.1 re_path**

Django2.0 中的 re_path 与 Django1.0 的url 用法是一样的

```python
from django.urls import re_path # django2.0中的re_path
from django.conf.urls import url # 在django2.0中同样可以导入1.0中的url

urlpatterns = [
    # 用法完全一致
    url(r'^app01/', include(('app01.urls','app01'))),
    re_path(r'^app02/', include(('app02.urls','app02'))),
]
```

### **6.2 path**

在 Django2.0 中新增了一个 path 功能，用来解决：数据类型转换问题与正则表达式冗余问题，如下

```python
# urls.py

from django.urls import re_path

from app01 import views

urlpatterns = [
    # 问题一：数据类型转换
    # 正则表达式会将请求路径中的年份匹配成功然后以str类型传递函数year_archive，在函数year_archive中如果想以int类型的格式处理年份，则必须进行数据类型转换
    re_path(r'^articles/(?P<year>[0-9]{4})/$', views.year_archive),

    # 问题二：正则表达式冗余
    # 下述三个路由中匹配article_id采用了同样的正则表达式，重复编写了三遍，存在冗余问题，并且极不容易管理，因为一旦article_id规则需要改变，则必须同时修改三处代码
    
    re_path(r'^article/(?P<article_id>[a-zA-Z0-9]+)/detail/$', views.detail_view),
    re_path(r'^articles/(?P<article_id>[a-zA-Z0-9]+)/edit/$', views.edit_view),
    re_path(r'^articles/(?P<article_id>[a-zA-Z0-9]+)/delete/$', views.delete_view),
]
```

```python
# views.py

from django.shortcuts import render,HttpResponse

# Create your views here.
def year_archive(request,year):
    print(year,type(year))
    return HttpResponse('year_archive page')

def detail_view(request,article_id):
    print(article_id, type(article_id))
    return HttpResponse('detail_view page')

def edit_view(request,article_id):
    print(article_id, type(article_id))
    return HttpResponse('edit_view page')

def delete_view(request,article_id):
    print(article_id, type(article_id))
    return HttpResponse('delete_view page')
```

使用 path 解决以上问题：

```python
from django.urls import path,re_path

from app01 import views

urlpatterns = [
    # 问题一的解决方案：
    path('articles/<int:year>/', views.year_archive), # <int:year>相当于一个有名分组，其中int是django提供的转换器，相当于正则表达式，专门用于匹配数字类型，而year则是我们为有名分组命的名，并且int会将匹配成功的结果转换成整型后按照格式（year=整型值）传给函数year_archive


    # 问题二解决方法：用一个int转换器可以替代多处正则表达式
    path('articles/<int:article_id>/detail/', views.detail_view), 
    path('articles/<int:article_id>/edit/', views.edit_view),
    path('articles/<int:article_id>/delete/', views.delete_view),
]
```

注意：

- path 与 re_path 或者1.0中的 url 的不同之处是，传给 path 的第一个参数不再是正则表达式，而是一个完全匹配的路径，相同之处是第一个参数中的匹配字符均无需加前导斜杠
- 使用尖括号(<>)从 url 中捕获值，相当于有名分组
- <>中可以包含一个转化器类型（converter type），比如使用  \<int:name\>  使用了转换器 int。若果没有转化器，将匹配任何字符串，当然也包括了 / 字符

django 默认支持以下5种转换器

```
str,匹配除了路径分隔符（/）之外的非空字符串，这是默认的形式
int,匹配正整数，包含0。
slug,匹配字母、数字以及横杠、下划线组成的字符串。
uuid,匹配格式化的uuid，如 075194d3-6885-417e-a8a8-6c931e272f00。
path,匹配任何非空字符串，包含了路径分隔符（/）（不能用？）
```

当然，使用转换器也有一些自身的缺陷

```
path('articles/<int:year>/<int:month>/<slug:other>/', views.article_detail) 
```

针对路径 http://127.0.0.1:8000/articles/2009/123/hello/，path会匹配出参数year=2009,month=123,other='hello'传递给函数article_detail，显然这是不合理的，因为月份不可能出现三位数，所以转换器是无法做到精准匹配的，我们可以根据自身业务的需求自定义转换器。转换器实际上就是一个类或者接口，它有以下几点要求：

- regex类属性，字符串类型
- to_python(self, value)方法，value是由类属性 regex所匹配到的字符串，返回具体的Python变量值，以供Django传递到对应的视图函数中
- to_url(self, value)方法，和to_python相反，value是一个具体的Python变量值，返回其字符串，通常用于url反向引用

具体步骤如下：

1. 在app01下新建文件path_ converters.py,文件名可以随意命名

```python
class MonthConverter:
    regex='\d{2}' # 属性名必须为regex

    def to_python(self, value):
        return int(value)

    def to_url(self, value):
        return value # 匹配的regex是两个数字，返回的结果也必须是两个数字
```

2. 在urls.py中，使用register_converter将其注册到URL配置中：

```python
from django.urls import path,register_converter
from app01.path_converts import MonthConverter

register_converter(MonthConverter,'mon')

from app01 import views

urlpatterns = [
    path('articles/<int:year>/<mon:month>/<slug:other>/', views.article_detail, name='aaa'),

]
```

3. views.py中的视图函数article_detail

```python
from django.shortcuts import render,HttpResponse,reverse

def article_detail(request,year,month,other):
    print(year,type(year))
    print(month,type(month))
    print(other,type(other))
    print(reverse('aaa',args=(1988,12,'hello'))) # 反向解析结果/articles/1988/12/hello/
    return HttpResponse('ok')
```

测试

```
在浏览器输入http://127.0.0.1:8000/articles/2009/12/hello/，path会成功匹配出参数year=2009,month=12,other='hello'传递给函数article_detail

在浏览器输入http://127.0.0.1:8000/articles/2009/123/hello/，path会匹配失败，因为我们自定义的转换器mon只匹配两位数字，而对应位置的123超过了2位
```