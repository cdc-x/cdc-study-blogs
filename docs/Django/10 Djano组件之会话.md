# cookie与session

## 一、会话跟踪技术

### **1.1 HTTP 协议的无状态保存**

​		现在有这样一个需求，用户先通过网站的登陆页面进行登录，之后用户再去访问网站的其他页面或者模块，我们怎么才能知道该用户是已经登陆过的呢？显然只依靠 HTTP协议自身是无法实现这一点的，HTTP协议本身无法保存用户的状态，请求与请求之间是没有任何关系的。

### **1.2 会话跟踪**

​		什么是会话？可以把会话理解为客户端与服务器之间的一次会晤，在一次会晤中可能会包含多次请求和响应。例如你给10086打个电话，你就是客户端，而10086服务人员就是服务器了。从双方接通电话那一刻起，会话就开始了，到某一方挂断电话表示会话结束。在通话过程中，你会向10086发出多个请求，那么这多个请求都在一个会话中。

 在一个会话的多个请求中共享数据，这就是会话跟踪技术。例如在一个会话中的请求如下：

- 请求登录（请求参数是用户名和密码）；
- 请求转账（请求参数与转账相关的数据）； 
- 请求信誉卡还款（请求参数与还款相关的数据）。 

在这上会话中当前用户信息必须在这个会话中共享的，因为登录的是张三，那么在转账和还款时一定是相对张三的转账和还款！这就说明我们必须在一个会话过程中有共享数据的能力。

### **1.3 会话跟踪技术的实现**

​		我们知道HTTP协议是无状态协议，也就是说每个请求都是独立的！无法记录前一次请求的状态。但HTTP协议中可以使用Cookie来完成会话跟踪！在Web开发中，使用session来完成会话跟踪，session底层依赖Cookie技术。

## 二、cookie

### **2.1 cookie简介**

​		其实Cookie是key-value结构的一个数据，类似于一个python中的字典。它是随着服务器端的响应发送给客户端浏览器的，然后客户端浏览器会把Cookie保存起来，当下一次再访问服务器时把Cookie再发送给服务器。 Cookie是由服务器创建，然后通过响应发送给客户端的一个键值对。客户端会保存Cookie，并会标注出Cookie的来源（哪个服务器的Cookie）。当客户端向服务器发出请求时会把所有这个服务器Cookie包含在请求中发送给服务器，这样服务器就可以识别客户端了！

<img src="/static/img/Django专题/cookie原理.png" style="zoom:67%;" /> 

需要注意的是，**一个浏览器和一个服务器之前只能识别自己的Cookie**。这是什么意思呢？

比如我使用浏览器访问了百度，进行了登录等操作以后，浏览器和百度的服务器之间就会有一套自己的专属cookie，此时我又使用了另一个浏览器访问了百度，此时之前的cookie不能被使用，因为对于百度的服务器来说，之前的浏览器和现在的浏览器是两个完全不同的客户端；

同样的，我们使用同一个浏览器在访问百度的同时也去访问京东，此时浏览器与百度之间的cookie也不能被京东使用，因为对于浏览器来说，百度和京东是完全不同的服务器。

### **2.2 cookie的设置于读取**

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^login/', views.login),
    url(r'^index/', views.index),
]
```

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
            """
            cookie是服务器端来设置的，要封装到响应体中：
            return HttpResponse()
            return render()
            return redirect()
            """

            # 设置cookie
            response = HttpResponse("登陆成功")
            response.set_cookie("is_login", True)
            return response

    return render(request, "login.html")


def index(request):
    # 从请求中获取cookie，如果获取不到就默认赋值 None
    is_login = request.COOKIES.get("is_login", None)

    if is_login:
        return render(request, "index.html")
    else:
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
    <h3>登陆页面</h3>

    <form action="" method="post">
        {% csrf_token %}
        <p>用户名<input type="text" name="user"></p>
        <p>密码<input type="password" name="pwd"></p>
        <p><input type="submit" value="登录"></p>
    </form>
</body>
</html>
```

上述示例主要实现了校验用户是否登录过的功能，实现流程如下：

- 首先用户访问登陆页面，浏览器发出一个 get 请求；
- 服务器接收到 get 请求后返回登陆页面；
- 用户在登陆页面填写用户名和密码以后，点击提交数据，此时浏览器会发出一个 post 请求；
- 服务器接收到 post 请求带来的数据后进行正确性校验，如果校验成功会在响应体中封装 cookie，定义一个特殊的键值对表示该用户已经登陆成功，校验失败则还是继续返回登陆页面；
- 浏览器在接收到响应后，会把 cookie 暂存，继续访问 index 页面时，会把 cookie 封装在请求体中再次一起发送；
- 服务器接收到访问 index 页面的请求以后，检查请求中的 cookie 是否含有该特殊的键值，如果有就表示该用户已经登陆过了，直接返回首页；没有则返回登陆页面让用户登录。

上述示例主测试结果如下：

**第一步：**访问 http://127.0.0.1:8000/login/，观察此时浏览器中的 cookie

<img src="/static/img/Django专题/cookie1.png" style="zoom:50%;" /> 

**第二步：**在为登录的情况下，直接访问 http://127.0.0.1:8000/index/，会跳转会回登陆页面，此时观察 index 相关请求中的 cookie

<img src="/static/img/Django专题/cookie2.png" style="zoom:50%;" /> 

**第三步：**输入用户名密码进行登录，登录成功后的 cookie 中可以看到我们自己设置的值了

<img src="/static/img/Django专题/cookie3.png" style="zoom:50%;" /> 

**第四步：**登录成功后再次访问 index，此时 index 请求相关的 cookie 为

<img src="/static/img/Django专题/cookie4.png" style="zoom:50%;" /> 

### **2.3 cookie的参数设置**

#### **2.3.1 设置超时时间**

如果不设置超时时间，cookie 的有效期默认是在关闭浏览器之前。有两种方式可以设置超时时长

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from app01.models import UserInfo
import datetime


def login(request):
    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        ret = UserInfo.objects.filter(user=user, pwd=pwd).first()

        if ret:
            """
            cookie是服务器端来设置的，要封装到响应体中：
            return HttpResponse()
            return render()
            return redirect()
            """

            # 设置cookie
            response = HttpResponse("登陆成功")

            # 方式一：max_age 规定多长时间后 cookies 失效
            response.set_cookie("is_login", True, max_age=30)  # 30秒后设置的该键值对失效

            # 方式二： expires 规定 cookie 失效的具体时间，值必须是一个时间对象
            data = datetime.datetime(year=2020, month=3, day=20, hour=14)
            response.set_cookie("is_login", True, max_age=30, expires=data)  # 2020年3月20号14点时，该键值对失效

            return response

    return render(request, "login.html")
```

#### **2.3.2 设置相对路径**

默认情况下，所有视图都可以使用我们设置的cookie，但是我们也可以手动规定 cookie 由哪些固定的视图可以获取。

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from app01.models import UserInfo
import datetime


def login(request):
    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        ret = UserInfo.objects.filter(user=user, pwd=pwd).first()

        if ret:
            """
            cookie是服务器端来设置的，要封装到响应体中：
            return HttpResponse()
            return render()
            return redirect()
            """

            # 设置cookie
            response = HttpResponse("登陆成功")

            response.set_cookie("is_login", True, path="/index/")  # 只有index请求可以获取该 cookie

            return response

    return render(request, "login.html")


def index(request):
    print("index", request.COOKIES)

    # 从请求中获取cookie，如果获取不到就默认赋值 None
    is_login = request.COOKIES.get("is_login", None)

    if is_login:
        return render(request, "index.html")
    else:
        return redirect("/login/")


def test(request):
    print("test", request.COOKIES)

    return HttpResponse("test")
```

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^login/', views.login),
    url(r'^index/', views.index),
    url(r'^test/', views.test),
]
```

```
# 测试结果
index {'is_login': 'True', 'csrftoken': '1rBhf4zliEruWHaoPmvQurF4RirU36j0D5snKSSkIdnbcZUiWEQysFvBSJJuBzGt'}
test {'csrftoken': '1rBhf4zliEruWHaoPmvQurF4RirU36j0D5snKSSkIdnbcZUiWEQysFvBSJJuBzGt'}
```

#### **2.3.3 其他参数**

```
domain=None,        Cookie生效的域名。你可用这个参数来构造一个跨站cookie。如 domain=".example.com" 所构造的cookie对下面这些站点都是可读的：www.example.com 、 www2.example.com 和                           an.other.sub.domain.example.com 。如果该参数设置为 None ，cookie只能由设置它的站点读取。

secure=False,        如果设置为 True ，浏览器将通过HTTPS来回传cookie。
httponly=False       只能http协议传输，无法被JavaScript获取（不是绝对，底层抓包可以获取到也可以被覆盖）
```

### **2.4 删除 cookie**

```python
response.delete_cookie("cookie_key",path="/",domain=name)
```

## 三、cookie 的使用

实现用户登录后可以访问首页的功能，且首页显示用户名和上次访问首页的时间，点击退出后，用户退出登录。

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^login/', views.login),
    url(r'^index/', views.index),
    url(r'^test/', views.test),
    url(r'^exit/', views.exit),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from app01.models import UserInfo
import datetime


def login(request):
    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        ret = UserInfo.objects.filter(user=user, pwd=pwd).first()

        if ret:
            """
            cookie是服务器端来设置的，要封装到响应体中：
            return HttpResponse()
            return render()
            return redirect()
            """

            # 设置cookie
            response = HttpResponse("登陆成功")

            response.set_cookie("is_login", True)
            response.set_cookie("user", ret.user)  # 把登陆成功的用户名也添加到 cookie 中

            return response

    return render(request, "login.html")


def index(request):
    print("index", request.COOKIES)

    # 从请求中获取cookie，如果获取不到就默认赋值 None
    is_login = request.COOKIES.get("is_login", None)

    if is_login:
        # 取出登陆成功的用户名
        user = request.COOKIES.get("user")

        # 取出上一次的登陆时间
        last_time = request.COOKIES.get("last_visited_time", "")

        # 将本次登陆时间封装到 cookie
        date = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        response = render(request, "index.html", locals())
        response.set_cookie("last_visited_time", date)

        return response
    else:
        return redirect("/login/")


def test(request):
    print("test", request.COOKIES)

    return HttpResponse("test")


def exit(request):
    # 删除 cookie
    response = HttpResponse("账户已经注销")
    response.delete_cookie("is_login")
    response.delete_cookie("user")
    response.delete_cookie("last_visited_time")

    return response
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
    <h3>欢迎登录，{{ user }}</h3>
    <p>上次登陆时间 {{ last_time }}</p>

    <form action="/exit/">
        <input type="submit" value="退出">
    </form>
</body>
</html>
```

## 四、session

​		使用 cookie 实现的会话跟踪实际上会存在很多问题，由于 cookie 是存储在浏览器端的，所以 cookie 内容都死明文可见的，对于用户的一些私密信息来说，也很容易遭到泄露。		

​		Session是服务器端技术，利用这个技术，服务器在运行时可以 为每一个用户的浏览器创建一个其独享的session对象，由于 session为用户浏览器独享，所以用户在访问服务器的web资源时 ，可以把各自的数据放在各自的session中，当用户再去访问该服务器中的其它web资源时，其它web资源再从用户各自的session中 取出数据为用户服务。Session 本质上也是基于 cookie 实现的，只是和 cookie 相比，session 操作多了一些额外的操作。

### **4.1 设置 session** 

```python
# 直接在request 中设置，添加一个登录成功后的标识
request.session["is_login"] = True
```

在设置 session 的过程中，实际上做了三件事情：

- 第一步，生成一个随机的字符串，例如：ltv8zy1kh5lxj1if1fcs2pqwodumr45t
- 第二步，在响应体中设置 cookie，添加一个键值对，键名固定为 session_id，值为随机生成的那个字符串，如：{"session_id":"ltv8zy1kh5lxj1if1fcs2pqwodumr45t"}
- 第三步，在 django_session表中创建一条记录，其中 seesion_key 字段的值为生成的那个随机字符串，seesion_data 字段的值为 {"is_login":True} 序列化后的值

### **4.2 获取 session**

```python
# 直接从request中获取
request.session.get("is_login")
```

在获取session的过程中，实际上做了三件事情：

- 第一步，从请求的 cookie 中获取 session_id，即那段随机的字符串
- 第二步，拿着字符串去 django_session 表中根据 seesion_key 过滤，取出对应的那条记录
- 第三步，从记录中取出真实的 cookie 键值对

下面我们就使用一个 session 版本的判断用户是否登录的示例来演示一下

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^session_login/', views.session_login),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from app01.models import UserInfo
import datetime

def session_login(request):

    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        ret = UserInfo.objects.filter(user=user, pwd=pwd).first()

        if ret:
        	
        	# 设置 session
            request.session["is_login"] = True
            request.session["name"] = ret.user

            return HttpResponse("登录成功")

    return render(request, "login.html")


def session_index(request):
    print("是否已经登录", request.session.get("is_login"))  # True
    print("登录的用户是", request.session.get("name"))  # cdc

    is_login = request.session.get("is_login")

    if is_login:
        return HttpResponse("这是首页")
    else:
        return redirect("/session_login/")
```

启动项目，进行测试

- 第一次访问 http://127.0.0.1:8000/session_login/ 时，浏览器发送的是 get 请求，此时观察 cookie

<img src="/static/img/Django专题/session1.png" style="zoom:50%;" /> 

- 输入用户名密码登陆成功后，观察cookie信息

<img src="/static/img/Django专题/session2.png" style="zoom:50%;" /> 

此时数据库中 django_session 表中的信息

<img src="/static/img/Django专题/session3.png" style="zoom: 67%;" /> 

在登录成功的情况下，我们在访问一下  http://127.0.0.1:8000/session_index/，观察后台打印出的 session 信息

```python
# 是否已经登录 True
# 登录的用户是 cdc
```

### **4.3 删除 session**

```python
# 方式一：只能删除某一个键值对
del request.session["session_name"]

# 方式二：删除整个内容
flush()    删除当前的会话数据并删除会话的Cookie。这用于确保前面的会话数据不可以再次被用户的浏览器访问
```

使用 session 的删除技术，实现一个注销用户的功能

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [

    url(r'^session_login/', views.session_login),
    url(r'^session_index/', views.session_index),
    url(r'^logout/', views.logout),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse, redirect
from app01.models import UserInfo
import datetime


def session_login(request):
    if request.method == "POST":
        user = request.POST.get("user")
        pwd = request.POST.get("pwd")
        ret = UserInfo.objects.filter(user=user, pwd=pwd).first()

        if ret:
            request.session["is_login"] = True
            request.session["name"] = ret.user

            return HttpResponse("登录成功")

    return render(request, "login.html")


def session_index(request):
    print("是否已经登录", request.session.get("is_login"))  # True
    print("登录的用户是", request.session.get("name"))  # cdc

    is_login = request.session.get("is_login")

    if is_login:
        return render(request, "index_session.html")
    else:
        return redirect("/session_login/")


def logout(request):
    # del request.session["is_login"]  # 不建议使用此方式，虽然可以实现，但是其他信息还是会保留
    
    request.session.flush()
	"""
	相当于做了三件事：
	 	1.从cookie中获取随机字符串	randon_str=request.COOKIE.get("sessionid")

    	2.使用字符串去数据库表中进行匹配并删除该记录		django-session.objects.filter(session-key=randon_str).delete()

    	3.删除对应的cookie	 response.delete_cookie("sessionid",randon_str)
	"""
    
    return redirect("/login/")
```

```html
<!-- index_session -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h2>这是首页</h2>

    <a href="/logout/">注销</a>
</body>
</html>
```

### **4.4 session 其他操作**

```python
get(key, default=None)
  
fav_color = request.session.get('fav_color', 'red')
  
pop(key)
  
		fav_color = request.session.pop('fav_color')
  
keys()
  
items()
  
setdefault()
  
  
用户session的随机字符串
        request.session.session_key
   
        # 将所有Session失效日期小于当前日期的数据删除
        request.session.clear_expired()
   
        # 检查 用户session的随机字符串 在数据库中是否
        request.session.exists("session_key")
   
        # 删除当前用户的所有Session数据
        request.session.delete("session_key")
   
        request.session.set_expiry(value)
            * 如果value是个整数，session会在些秒数后失效。
            * 如果value是个datatime或timedelta，session就会在这个时间后失效。
            * 如果value是0,用户关闭浏览器session就会失效。
            * 如果value是None,session会依赖全局session失效策略。
```

### **4.5 session 参数设置**

```
Django默认支持Session，并且默认是将Session数据存储在数据库中，即：django_session 表中。
   
session 相关参数配置要在项目中的配置文件中 settings.py
   
    SESSION_ENGINE = 'django.contrib.sessions.backends.db'   # 引擎（默认）
       
    SESSION_COOKIE_NAME ＝ "sessionid"                       # Session的cookie保存在浏览器上时的key，即：sessionid＝随机字符串（默认），可以自定义键的名称
    SESSION_COOKIE_PATH ＝ "/"                               # Session的cookie保存的路径（默认）
    SESSION_COOKIE_DOMAIN = None                             # Session的cookie保存的域名（默认）
    SESSION_COOKIE_SECURE = False                            # 是否Https传输cookie（默认）
    SESSION_COOKIE_HTTPONLY = True                           # 是否Session的cookie只支持http传输（默认）
    SESSION_COOKIE_AGE = 1209600                             # Session的cookie失效日期（2周）（默认）
    SESSION_EXPIRE_AT_BROWSER_CLOSE = False                  # 是否关闭浏览器使得Session过期（默认）
    SESSION_SAVE_EVERY_REQUEST = False                       # 是否每次请求都保存Session，默认修改之后才保存（默认）
```

