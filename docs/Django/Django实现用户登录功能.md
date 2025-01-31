# Django实现用户登录功能

## app的创建和注册

​		在功能和应用模块较少的前提下，我们可以将所有业务逻辑处理的函数写在一个 views.py 下，但是往往在实际的项目中，会涉及到多个应用模块和复杂操作。因此，将不同的功能模块（app）进行划分是非常有必要的。

创建 app 命令：python manage.py startapp app名字

在创建完 app 应用后，jango 并不知道有新的应用被创建，因此还需要在项目中手动注册一下。

<img src="/static/img/django_register_app.png" style="zoom:50%;" />

在创建完对应的功能模块后，相关的操作就可以写在对应模块下的  views.py  中了。

## 登录功能流程梳理

1. ### 用户输入登录的网址，得到一个登录页面

2. ### 用户在登录页面填写用户名和密码后，点击登录的按钮，此时web页面将用户填写的信息传给后端服务器

3. ### 后端服务器获取到用户填写的信息后，进行用户名和密码正确性校验

4. ### 如果密码正确，返回首页给用户，否则提示用户用户名或密码错误

## 功能实现

1、用户输入登录的网址，得到一个登录页面

mysite2/urls.py

```python
from django.conf.urls import url
from django.contrib import admin
from login import views

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^login/$', views.login)
]

```

login/views.py

```python
from django.shortcuts import render

# request是固定参数，所有跟请求相关的数据都封装到了这个request对象里面
def login(request):
    return render(request, "login.html")

```

django 三种返回方式

- HttpResponse	返回一个指定的字符串时
- render				 返回一个HTML文件
- redirect				跳转

templates/login.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登陆页面</title>
</head>
<body>
    <form action="/login_check/" method="post">
        <div>
             <label for="name">用户名</label>
        <input type="text" id="name" name="name">
        </div>

        <div>
            <label for="pwd">密码 </label>
            <input type="password" id="pwd" name="pwd">
        </div>

        <div>
            <input type="submit" value="登录">
        </div>
    </form>

</body>
</html>
```

