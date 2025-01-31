# Django之用户认证

## 一、用户认证机制的简单了解

​		我们之前已经学会使用会话跟踪技术来实现对用户的相关管理，那为什么 django 还要专门提供一套用于管理用户的组件呢？这是因为只单纯的使用会话技术会存数据混乱的问题。比如，用户A在会话中保存了姓名、邮箱、登陆状态三个属性，用户B只要姓名和登陆状态两个属性，此时换成用户B登录，只会把会话中的姓名和登陆状态覆盖，邮箱信息还会保留用户A的。

​		为了方便管理用户认证相关的操作，django 给我们提供了一套专门的组件。需要注意的是，一旦我们要使用django给我们提供的用户认证内部机制，就必须使用django自带的那张auth_user表，同样对于表的增删改查操作也和原先的ORM操作不太一样。我们可以简单看一个例子：

1、创建一个 django 项目并创建一个 app，注册该app；

2、不用建表，直接进行进行数据库迁移操作；

```
python manage.py makemigrations
python manage.py migrate
```

3、进入数据库，可以看到生成了一张 auth_user 表，之后对于用的操作都会基于这一张表。表中给我们提供了许多字段，如果这些字段还不能满足我们的需求，可以通过表关联的方式将我们所需要的字段关联上去；

4、通过命令方式创建两个用户（这里暂时用命令创建，后面会学习使用 User 对象来操作用户），django 会对密码自动加密处理

```
python manage.py createsuperuser
```

<img src="/static/img/Django专题/auth_user1.png" style="zoom:67%;" /> 

## 二、auth 模块的使用

对于 auth 模块，我们需要掌握以下三种方法：

### **2.1 authenticate()**

​		该方法用于用户认证，即验证用户名以及密码是否正确，如果认证信息有效，会返回一个 User 对象，该对象是 django 内部封装好的一个对象。authenticate 会在User 对象上设置一个属性标识哪种认证后端认证了该用户，且该信息在后面的登录过程中是需要的。当我们试图登陆一个从数据库中直接取出来不经过authenticate()的User对象会报错的。

我们先回忆一下普通检验用户的用户名和密码的正确性的方式

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from app01.models import UserInfo


def login(request):
    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        ret = UserInfo.objects.filter(user=user, pwd=pwd).first()
        
        if ret:
        	return HttpResponse("登录成功")
        else:
        	return HttpResponse("登录失败")
	return render(request, "login.html")
```

使用 authenticate 来认证

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from django.contrib import auth


def login(request):
    if request.method == "POST":
        name = request.POST.get("name")
        pwd = request.POST.get("pwd")
		
		# 认证成功会返回一个user对象，失败返回一个None
        user = auth.authenticate(username=name, password=pwd)

        print(user)

        return HttpResponse("OK")

    return render(request, "login.html")

```

如果我们想通过原始的ORM操作来对 auth_user 这张表进行校验是行不通的。我们从前端中获取的密码都是最原始的，但是表中的密码是加密过后的，所以查找永远是失败的。

### **2.2 login(HttpRequest, user)**

该函数接受一个HttpRequest对象，以及一个认证了的User对象。此函数使用 django 的 session 框架给某个已认证的用户附加上 sessionid 等信息，等同于我们手动设置相关的session信息。一旦登录验证成功，就会把当前的用户相关的信息全部封装到 request.user 中，且 request.user 是一个全局的变量，即其他视图函数和模板中可以直接引用。如果登录验证没有成功， request.user 中就会得到一个匿名用户对象。匿名用户对象有以下的特点：

```
id 永远为None。
username 永远为空字符串。
get_username() 永远返回空字符串。
is_staff 和 is_superuser 永远为False。
is_active 永远为 False。
groups 和 user_permissions 永远为空。
is_anonymous() 返回True 而不是False，表示该对象是一个匿名对象，没有通过登录验证
is_authenticated() 返回False 而不是True，表示是否是通过验证的对象
set_password()、check_password()、save() 和delete() 引发 NotImplementedError。

New in Django 1.8:
新增 AnonymousUser.get_username() 以更好地模拟 django.contrib.auth.models.User。
```

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from django.contrib import auth


def login(request):
    if request.method == "POST":
        name = request.POST.get("name")
        pwd = request.POST.get("pwd")

        # 认证成功会返回一个user对象，失败返回一个None
        user = auth.authenticate(username=name, password=pwd)

        print(user)

        if user:
            auth.login(request, user)  # 登录成功后将当前用户信息封装到 request.user 中去
            return HttpResponse("OK")

    return render(request, "login.html")


def index(request):
    # 如果 request.user 中的 is_anonymous 的值为 True，证明是匿名对象，没有登陆过
    if request.user.is_anonymous:
        return redirect("/login/")
    else:
        return HttpResponse("Index")
```

### **2.3 logout(request)**

该函数接受一个HttpRequest对象，用于注销用户信息，无返回值。当调用该函数时，当前请求的session信息会全部清除。该用户即使没有登录，使用该函数也不会报错。

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from django.contrib import auth

def logout(request):
    auth.logout(request)
    
    # 用户退出后，直接返回登录页面
    return redirect("/login/")
```

### **2.4 使用 auth 模块完成用户登录验证**

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^login/', views.login),
    url(r'^index/', views.index),
    url(r'^logout/', views.logout),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from django.contrib import auth


def login(request):
    if request.method == "POST":
        name = request.POST.get("name")
        pwd = request.POST.get("pwd")

        # 认证成功会返回一个user对象，失败返回一个None
        user = auth.authenticate(username=name, password=pwd)

        print(user)

        if user:
            auth.login(request, user)  # 登录成功后将当前用户信息封装到 request.user 中去
            return HttpResponse("OK")

    return render(request, "login.html")


def index(request):
    # 如果 request.user 中的 is_anonymous 的值为 True，证明是匿名对象，没有登陆过，那就返回登陆页面
    if request.user.is_anonymous:
        return redirect("/login/")
    else:
        return render(request, "index.html")


def logout(request):
    auth.logout(request)
    
    # 用户退出后，给用户返回一个登陆页面
    return redirect("/login/")
```

```html
<!-- login.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="" method="post">
    {% csrf_token %}
    <p>用户名：<input type="text" name="name"></p>
    <p>密  码：<input type="password" name="pwd"></p>
    <p><input type="submit" value="登录"></p>
</form>
</body>
</html>
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
    <h3>这是 Index 页面</h3>
    <p>欢迎，{{ request.user.username }}</p>
    <p><a href="/logout/">注销</a></p>
</body>
</html>
```

启动项目进行测试：

第一步，直接访问 http://127.0.0.1:8000/index/，此时用户没有登录，直接跳转到登录页面；

第二步，输入用户名和密码后，登陆成功跳转到首页；

第三步，点击首页注销按钮，此时用户登陆的相关会话信息已经被删除，跳转登录页面。此时再次直接访问  http://127.0.0.1:8000/index/，还是会跳回登录页面，证明用户登录的认证被注销。

## 三、User 对象

User 对象是 django 内部封装的，配合 auth 模块使用的一个对象类，主要掌握以下的操作：

### **3.1 is_authenticated**

​		如果是真正的 User 对象，该属性的返回值恒为 True 。 用于检查用户是否已经通过了认证。通过认证并不意味着用户拥有任何权限，甚至也不检查该用户是否处于激活状态，这只是表明用户成功的通过了认证。 这个方法很重要, 在后台用request.user.is_authenticated()判断用户是否已经登录，如果true则可以向前台展示request.user中的相关信息

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from django.contrib import auth

def index(request):
    # 如果 request.user 中的 is_authenticated 的值为 False，证明是匿名对象，没有登陆过，那就返回登陆页面
    if not request.user.is_authenticated:
        return redirect("/login/")
    else:
        return render(request, "index.html")
```

### **3.2 创建一个user对象**

```python
from django.contrib.auth.models import User
user = User.objects.create_user（username='',password='',email=''）
```

### **3.3 check_password(passwd)**

用于检查密码是否符合规则，通常和修改密码功能联合使用

### **3.4 set_password()**

修改密码示例

```python
def set_password(request):
    user = request.user
    state = None
    if request.method == 'POST':
        old_password = request.POST.get('old_password', '')
        new_password = request.POST.get('new_password', '')
        repeat_password = request.POST.get('repeat_password', '')
        if user.check_password(old_password):
            if not new_password:
                state = 'empty'
            elif new_password != repeat_password:
                state = 'repeat_error'
            else:
                user.set_password(new_password)
                user.save()
                return redirect("/log_in/")
        else:
            state = 'password_error'
    content = {
        'user': user,
        'state': state,
    }
    return render(request, 'set_password.html', content)
```

### **3.5 使用装饰器来实现用户认证**

​		想要判断用户是否已经通过登录认证，就需要在每个视图中对 request.user 中的 is_authenticated 进行判断，这其实是非常繁琐的。django 还专门提供了用于判断用户登录认证的装饰器，方便我们进行使用。

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^login/', views.login),
    url(r'^index/', views.index),
    url(r'^logout/', views.logout),
    url(r'^order/', views.order),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from django.contrib import auth
from django.contrib.auth.decorators import login_required


def login(request):
    if request.method == "POST":
        name = request.POST.get("name")
        pwd = request.POST.get("pwd")

        # 认证成功会返回一个user对象，失败返回一个None
        user = auth.authenticate(username=name, password=pwd)

        print(user)

        if user:
            auth.login(request, user)  # 登录成功后将当前用户信息封装到 request.user 中去
            return redirect("/index/")

    return render(request, "login.html")


@login_required
def index(request):
    # 如果 request.user 中的 is_anonymous 的值为 True，证明是匿名对象，没有登陆过，那就返回登陆页面
    # if request.user.is_anonymous:
    #     return redirect("/login/")
    # else:
    #     return render(request, "index.html")
    return render(request, "index.html")


@login_required
def order(request):
    return render(request, "order.html")


def logout(request):
    auth.logout(request)

    # 用户退出后，给用户返回一个登陆页面
    return redirect("/login/")
```

```html
<!-- order.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h2>这是 order 页面</h2>
</body>
</html>
```

注意：使用装饰器时，要在 settings 中添加一个 LOGIN_URL 配置参数，否则访问登陆页面时会跳转到 django 内置的一个登录路径，而不是往我们自己定义的路由跳转

```python
# settings.py

LOGIN_URL = "/login/"
```

启动项目进行测试，发现用户未登录的情况下直接访问 index 和 order 都会跳转到登陆页面，只有登录了才能正常访问。

​		但是整体还存在一个问题，当我我们未登录的情况下访问 http://127.0.0.1:8000/login/index 的时候，会自动跳转到登陆页面，此时路由变为 http://127.0.0.1:8000/login/?next=/index/ ，这个路由的意思是，当前在login页面，登录成功后跳转到 index 请求；同样的，对于 order 页面，http://127.0.0.1:8000/login/?next=/order/ 表示当前在login页面，登录成功后跳转到 order请求，但是实际测试的时候，两者都是跳转到 index 页面，这是由于在login视图逻辑中，我们将验证成功后跳转的请求写死了，即

```
if user:
	auth.login(request, user)  # 登录成功后将当前用户信息封装到 request.user 中去
    return redirect("/index/")
```

所以，我们应该根据实际要跳转的路由来进行跳转，而不是固定跳转到某一个路由。我们只需要对 login 函数进行一下改动即可。

```python
# views.py

def login(request):
    if request.method == "POST":
        name = request.POST.get("name")
        pwd = request.POST.get("pwd")

        # 认证成功会返回一个user对象，失败返回一个None
        user = auth.authenticate(username=name, password=pwd)

        print(user)

        if user:
            auth.login(request, user)  # 登录成功后将当前用户信息封装到 request.user 中去
            # return redirect("/index/")
            next_url = request.GET.get("next")
            return redirect(next_url)

    return render(request, "login.html")
```

