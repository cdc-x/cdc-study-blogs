# 分页器

## 一、分页器简介

​		分页器主要是为了解决大规模数据展示的问题。试想一下，如果数据库中有成千上万条数据，一下子全部在页面上展示出来的话，对于用户的体验肯定是不好的。我们可以将数据分批展示，例如每一页就展示十条数据，让用户通过页码去控制。Django 中也专门提供了这种分页的机制。

## 二、分页器的简单使用

**第一步：**创建 Book 表，用于存放书籍名字和价格等信息。

```python
# app01/models.py

from django.db import models

# Create your models here.

class Book(models.Model):
    bid = models.AutoField(primary_key=True)
    title = models.CharField(max_length=32)
    price = models.CharField(max_length=32)
```

**第二步：**随机插入多条数据

```python
from app01.models import Book

def insert_data():
    # 批量导入数据
    book_list = list()
    for i in range(100):
        book_obj = Book(title=f"book_{i+1}", price=str((i+1)**2))
        book_list.append(book_obj)

    Book.objects.bulk_create(book_list)
```

这里不推荐使用 for 循环的方式去插入，那样每插入一条都需要连接和断开数据库，比较消耗性能。建议先将数据全都生成好再批量的插入。

**第三步：**使用分页器

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^index/', views.index),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse
from app01.models import Book
from django.core.paginator import EmptyPage, Paginator


def index(request):
    book_list = Book.objects.all()  # 将表中所有的数据获取出来

    paginator = Paginator(book_list, 10)  # 对所有的book数据，每10个分一页
    print(paginator.count)  # 数据总数
    print(paginator.num_pages)  # 总页数
    print(paginator.page_range)  # 页码列表（顾头不顾尾）

    page_content = paginator.page(1)  # 第一页的全部数据

    # 获取某一页全部数据的两种方式
    # 方式一：得到一个列表，里面都是book对象
    content_list = page_content.object_list
    print(content_list)
    
    # 方式二：通过for循环去获取值
    for i in page_content:
        print(i)
    
    return HttpResponse("ok")
```

## 三、配合前端显示数据

### **3.1 初始版本**

```python
# views.py

from django.shortcuts import render, HttpResponse
from app01.models import Book
from django.core.paginator import EmptyPage, Paginator

def index(request):
   
    current_page_num = int(request.GET.get("page", 1))  # 获取url中的page的值，如果没有值默认展示第一页

    book_list = Book.objects.all()  # 将表中所有的数据获取出来

    paginator = Paginator(book_list, 10)  # 对所有的book数据，每10个分一页
    print(paginator.count)  # 数据总数
    print(paginator.num_pages)  # 总页数
    print(paginator.page_range)  # 页码列表（顾头不顾尾）

    page_content = paginator.page(current_page_num)

    # 获取某一页全部数据的两种方式
    # 方式一：得到一个列表，里面都是book对象
    # content_list = page_content.object_list
    # 方式二：通过for循环去获取值
    # for i in page_content:
    #     print(i)

    return render(request, "index.html", locals())
```

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<ul>
    {% for i in page_content %}
        <li>{{ i.title }}：{{ i.price }}</li>
    {% endfor %}
</ul>

</body>
</html>
```

```
# 测试
浏览器访问 http://127.0.0.1:8000/index/
book_1：1
book_2：4
book_3：9
book_4：16
book_5：25
book_6：36
book_7：49
book_8：64
book_9：81
book_10：100


浏览器访问 http://127.0.0.1:8000/index/?page=2
book_11：121
book_12：144
book_13：169
book_14：196
book_15：225
book_16：256
book_17：289
book_18：324
book_19：361
book_20：400
```

但是当访问的page的值为 -1 或者超过页码的最大值时，会出现错误，我们还需要对后台逻辑进行一个异常的处理。

### **3.2 处理页码不在合理范围内的问题**

```python
# views.py

from django.shortcuts import render, HttpResponse
from app01.models import Book
from django.core.paginator import EmptyPage, Paginator


def index(request):
    current_page_num = int(request.GET.get("page", 1))  # 获取url中的page的值，如果没有值默认展示第一页

    book_list = Book.objects.all()  # 将表中所有的数据获取出来

    paginator = Paginator(book_list, 10)  # 对所有的book数据，每10个分一页
    print(paginator.count)  # 数据总数
    print(paginator.num_pages)  # 总页数
    print(paginator.page_range)  # 页码列表（顾头不顾尾）

    # 如果页码不在合理范围内，就默认显示第一页的内容
    try:
        page_content = paginator.page(current_page_num)
    except EmptyPage as e:
        page_content = paginator.page(1)

    return render(request, "index.html", locals())
```

```
# 测试

浏览器访问 http://127.0.0.1:8000/index/?page=-1
book_1：1
book_2：4
book_3：9
book_4：16
book_5：25
book_6：36
book_7：49
book_8：64
book_9：81
book_10：100


浏览器访问 http://127.0.0.1:8000/index/?page=11
book_1：1
book_2：4
book_3：9
book_4：16
book_5：25
book_6：36
book_7：49
book_8：64
book_9：81
book_10：100
```

### **3.3 配合bootstrap在页面展示页码**

```python
# views.py

from django.shortcuts import render, HttpResponse
from app01.models import Book
from django.core.paginator import EmptyPage, Paginator


def index(request):
    current_page_num = int(request.GET.get("page", 1))  # 获取url中的page的值，如果没有值默认展示第一页

    book_list = Book.objects.all()  # 将表中所有的数据获取出来

    paginator = Paginator(book_list, 10)  # 对所有的book数据，每10个分一页
    print(paginator.count)  # 数据总数
    print(paginator.num_pages)  # 总页数
    print(paginator.page_range)  # 页码列表（顾头不顾尾）

    # 如果页码不在合理范围内，就默认显示第一页的内容
    try:
        page_content = paginator.page(current_page_num)
    except EmptyPage as e:
        page_content = paginator.page(1)

    return render(request, "index.html", locals())
```

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<ul>
    {% for i in page_content %}
        <li>{{ i.title }}：{{ i.price }}</li>
    {% endfor %}
</ul>

<nav aria-label="Page navigation">
    <ul class="pagination">
        <li>
            <a href="#" aria-label="Previous">
                <span aria-hidden="true">上一页</span>
            </a>
        </li>
        <!-- 根据实际的总页数动态生成页码 -->
        {% for item in paginator.page_range %}
        	<!-- 如果当前点击的页码和请求的页码是同一个，就添加选中样式-->
            {% if current_page_num == item %}
                <li class="active"><a href="?page={{ item }}">{{ item }}</a></li>
            {% else %}
                <li><a href="?page={{ item }}">{{ item }}</a></li>
            {% endif %}

        {% endfor %}


        <li>
            <a href="#" aria-label="Next">
                <span aria-hidden="true">下一页</span>
            </a>
        </li>
    </ul>
</nav>

</body>
</html>
```

测试结果展示

<img src="/static/img/Django专题/分页器1.png" style="zoom:67%;" /> 

当我们每页展示的数据比较少或者数据足够大的时候，就会导致页码变得特别的多，这样就不会特别美观，例如：

<img src="/static/img/Django专题/分页器2.png" style="zoom:67%;" /> 

因此针对分页的设计，我们还需要进一步处理。

### **3.4 优化分页设计**

#### **3.4.1 实现上一页和下一页的功能**

所谓的上一页和下一页，就是在当前页码=的基础上进行加一或者减一的操作，因此我们可以在模板中这么改

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<ul>
    {% for i in page_content %}
        <li>{{ i.title }}：{{ i.price }}</li>
    {% endfor %}
</ul>


<nav aria-label="Page navigation">
    <ul class="pagination">
        <li>
            <a href="?page={{current_page_num|add:-1  }}" aria-label="Previous">
                <span aria-hidden="true">上一页</span>
            </a>
        </li>
        {% for item in paginator.page_range %}
            {% if current_page_num == item %}
                <li class="active"><a href="?page={{ item }}">{{ item }}</a></li>
            {% else %}
                <li><a href="?page={{ item }}">{{ item }}</a></li>
            {% endif %}

        {% endfor %}


        <li>
            <a href="?page={{ current_page_num|add:1 }}" aria-label="Next">
                <span aria-hidden="true">下一页</span>
            </a>
        </li>
    </ul>
</nav>

</body>
</html>
```

虽然实现了我们想要的功能，但是访问第一页和最后一页时，再点击上一页或者下一页时，url中的page的值就不在合理的范围内了。分页器中还提供了我们查看当前页的上一页或者下一页的方法。

```python
# views.py

from django.shortcuts import render, HttpResponse
from app01.models import Book
from django.core.paginator import EmptyPage, Paginator

def index_test(request):
    current_page_num = int(request.GET.get("page", 1))  # 获取url中的page的值，如果没有值默认展示第一页

    book_list = Book.objects.all()  # 将表中所有的数据获取出来

    paginator = Paginator(book_list, 5)  # 对所有的book数据，每10个分一页

    test_page = paginator.page(2)  # 假设当前页是2

    # 查看是否有上一页和下一页。有结果为True，没有为False
    print(test_page.has_next())  # 是否有下一页
    print(test_page.has_previous())  # 是否有上一页
    
    # 查看上一页或者下一页的页码，有结果为页码，没有就报错
    print(test_page.next_page_number())  # 下一页的页码
    print(test_page.previous_page_number())  # 上一页的页码

    return HttpResponse("OK")
```

我们可以根据这四种方法去对上一页和下一页的功能进行完善。

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<ul>
    {% for i in page_content %}
        <li>{{ i.title }}：{{ i.price }}</li>
    {% endfor %}
</ul>


<nav aria-label="Page navigation">
    <ul class="pagination">
        <!--如果当前页码有上一页，正常跳转；没有的话，禁用上一页功能-->
        {% if page_content.has_previous %}
            <li>
                <a href="?page={{ page_content.previous_page_number }}" aria-label="Previous">
                    <span aria-hidden="true">上一页</span>
                </a>
            </li>
        {% else %}
            <li class="disabled">
                <a href="" aria-label="Previous">
                    <span aria-hidden="true">上一页</span>
                </a>
            </li>
        {% endif %}

        {% for item in paginator.page_range %}
            {% if current_page_num == item %}
                <li class="active"><a href="?page={{ item }}">{{ item }}</a></li>
            {% else %}
                <li><a href="?page={{ item }}">{{ item }}</a></li>
            {% endif %}

        {% endfor %}

        <!--如果当前页码有下一页，正常跳转；没有的话，禁用下一页功能-->
        {% if page_content.has_next %}
            <li>
                <a href="?page={{ page_content.next_page_number }}" aria-label="Next">
                    <span aria-hidden="true">下一页</span>
                </a>
            </li>
        {% else %}
            <li class="disabled">
                <a href="" aria-label="Next">
                    <span aria-hidden="true">下一页</span>
                </a>
            </li>
        {% endif %}

    </ul>
</nav>

</body>
</html>
```

#### **3.4.2 解决页码显示过多的问题**

我们可以规定页面上最多只能出现11个页码，被选中的页码在最中间，左右各有5个相邻的页码，对于其他页码都隐藏

```python
# views.py
from django.shortcuts import render, HttpResponse
from app01.models import Book
from django.core.paginator import EmptyPage, Paginator


# Create your views here.


def index(request):
  
    current_page_num = int(request.GET.get("page", 1))  # 获取url中的page的值，如果没有值默认展示第一页
    book_list = Book.objects.all()  # 将表中所有的数据获取出来
    paginator = Paginator(book_list, 5)  # 对所有的book数据，每10个分一页
    
    # 我们自己定义一个页码的列表，前端显示的时候据根据这列表来动态生成页码
    # 如果当前页码在最前面5个，再往前减去5会出现负值，所以这种情况要固定展示前11个
    if current_page_num - 5 < 1:
        page_range = range(1, 12)
    # 如果当前页码在最后面5个，再往后加上5会超出边界，所以这种情况要固定展示最后11个
    elif current_page_num + 5 > paginator.num_pages:
        page_range = range(paginator.num_pages - 10, paginator.num_pages + 1)
    else:
        page_range = range(current_page_num - 5, current_page_num + 6)

    # 如果页码不在合理范围内，就默认显示第一页的内容
    try:
        page_content = paginator.page(current_page_num)
    except EmptyPage as e:
        page_content = paginator.page(1)

    return render(request, "index.html", locals())
```

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css"
          integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
</head>
<body>
<ul>
    {% for i in page_content %}
        <li>{{ i.title }}：{{ i.price }}</li>
    {% endfor %}
</ul>


<nav aria-label="Page navigation">
    <ul class="pagination">
        <!--如果当前页码有上一页，正常跳转；没有的话，禁用上一页功能-->
        {% if page_content.has_previous %}
            <li>
                <a href="?page={{ page_content.previous_page_number }}" aria-label="Previous">
                    <span aria-hidden="true">上一页</span>
                </a>
            </li>
        {% else %}
            <li class="disabled">
                <a href="" aria-label="Previous">
                    <span aria-hidden="true">上一页</span>
                </a>
            </li>
        {% endif %}
        
        <!--这里生成页码就根据自定义的范围生成了-->
        {% for item in page_range %}
            {% if current_page_num == item %}
                <li class="active"><a href="?page={{ item }}">{{ item }}</a></li>
            {% else %}
                <li><a href="?page={{ item }}">{{ item }}</a></li>
            {% endif %}

        {% endfor %}

        <!--如果当前页码有下一页，正常跳转；没有的话，禁用下一页功能-->
        {% if page_content.has_next %}
            <li>
                <a href="?page={{ page_content.next_page_number }}" aria-label="Next">
                    <span aria-hidden="true">下一页</span>
                </a>
            </li>
        {% else %}
            <li class="disabled">
                <a href="" aria-label="Next">
                    <span aria-hidden="true">下一页</span>
                </a>
            </li>
        {% endif %}

    </ul>
</nav>

</body>
</html>
```