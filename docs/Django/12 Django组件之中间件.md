# 中间件

## 一、中间件介绍

​		我们在最开始介绍 django 生命周期的时候提及到过，浏览器发出的请求并不是直接到达路由层，视图层处理完的结果也不是直接返回浏览器的，都要经过中间件的处理。中间件顾名思义，是介于request与response处理之间的一道处理过程，相对比较轻量级，并且在全局上改变django的输入与输出。因为改变的是全局，所以需要谨慎实用，用不好会影响到性能。

对于中间件的配置可以在项目中的 settings.py 中修改，django 为我们默认提供了七个中间件，每一个中间件都有自己特殊的功能。中间件的执行顺序是按照他们在配置文件中的先后顺序来的。

```python
# settings.py

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

我们可以简单选择两个中间件观察一下他们的源码

```python
from django.middleware.security import SecurityMiddleware
```

```python
import re

from django.conf import settings
from django.http import HttpResponsePermanentRedirect
from django.utils.deprecation import MiddlewareMixin


class SecurityMiddleware(MiddlewareMixin):
    def __init__(self, get_response=None):
        self.sts_seconds = settings.SECURE_HSTS_SECONDS
        self.sts_include_subdomains = settings.SECURE_HSTS_INCLUDE_SUBDOMAINS
        self.sts_preload = settings.SECURE_HSTS_PRELOAD
        self.content_type_nosniff = settings.SECURE_CONTENT_TYPE_NOSNIFF
        self.xss_filter = settings.SECURE_BROWSER_XSS_FILTER
        self.redirect = settings.SECURE_SSL_REDIRECT
        self.redirect_host = settings.SECURE_SSL_HOST
        self.redirect_exempt = [re.compile(r) for r in settings.SECURE_REDIRECT_EXEMPT]
        self.get_response = get_response

    def process_request(self, request):
        path = request.path.lstrip("/")
        if (self.redirect and not request.is_secure() and
                not any(pattern.search(path)
                        for pattern in self.redirect_exempt)):
            host = self.redirect_host or request.get_host()
            return HttpResponsePermanentRedirect(
                "https://%s%s" % (host, request.get_full_path())
            )

    def process_response(self, request, response):
        if (self.sts_seconds and request.is_secure() and
                'strict-transport-security' not in response):
            sts_header = "max-age=%s" % self.sts_seconds
            if self.sts_include_subdomains:
                sts_header = sts_header + "; includeSubDomains"
            if self.sts_preload:
                sts_header = sts_header + "; preload"
            response["strict-transport-security"] = sts_header

        if self.content_type_nosniff and 'x-content-type-options' not in response:
            response["x-content-type-options"] = "nosniff"

        if self.xss_filter and 'x-xss-protection' not in response:
            response["x-xss-protection"] = "1; mode=block"

        return response

```

```python
from django.contrib.sessions.middleware import SessionMiddleware
```

```python
import time
from importlib import import_module

from django.conf import settings
from django.contrib.sessions.backends.base import UpdateError
from django.core.exceptions import SuspiciousOperation
from django.utils.cache import patch_vary_headers
from django.utils.deprecation import MiddlewareMixin
from django.utils.http import cookie_date


class SessionMiddleware(MiddlewareMixin):
    def __init__(self, get_response=None):
        self.get_response = get_response
        engine = import_module(settings.SESSION_ENGINE)
        self.SessionStore = engine.SessionStore

    def process_request(self, request):
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME)
        request.session = self.SessionStore(session_key)

    def process_response(self, request, response):
        """
        If request.session was modified, or if the configuration is to save the
        session every time, save the changes and set a session cookie or delete
        the session cookie if the session has been emptied.
        """
        try:
            accessed = request.session.accessed
            modified = request.session.modified
            empty = request.session.is_empty()
        except AttributeError:
            pass
        else:
            # First check if we need to delete this cookie.
            # The session should be deleted only if the session is entirely empty
            if settings.SESSION_COOKIE_NAME in request.COOKIES and empty:
                response.delete_cookie(
                    settings.SESSION_COOKIE_NAME,
                    path=settings.SESSION_COOKIE_PATH,
                    domain=settings.SESSION_COOKIE_DOMAIN,
                )
            else:
                if accessed:
                    patch_vary_headers(response, ('Cookie',))
                if (modified or settings.SESSION_SAVE_EVERY_REQUEST) and not empty:
                    if request.session.get_expire_at_browser_close():
                        max_age = None
                        expires = None
                    else:
                        max_age = request.session.get_expiry_age()
                        expires_time = time.time() + max_age
                        expires = cookie_date(expires_time)
                    # Save the session data and refresh the client cookie.
                    # Skip session save for 500 responses, refs #3881.
                    if response.status_code != 500:
                        try:
                            request.session.save()
                        except UpdateError:
                            raise SuspiciousOperation(
                                "The request's session was deleted before the "
                                "request completed. The user may have logged "
                                "out in a concurrent request, for example."
                            )
                        response.set_cookie(
                            settings.SESSION_COOKIE_NAME,
                            request.session.session_key, max_age=max_age,
                            expires=expires, domain=settings.SESSION_COOKIE_DOMAIN,
                            path=settings.SESSION_COOKIE_PATH,
                            secure=settings.SESSION_COOKIE_SECURE or None,
                            httponly=settings.SESSION_COOKIE_HTTPONLY or None,
                        )
        return response

```

我们会发现，实际上中间件就是一个类，并且类中要包含一些固定的方法。将类再添加到配置文件的 MIDDLEWARE 列表中，该类就会变为一个中间件。中间件有以下四种方法，下面会一一介绍：

```
process_request

process_view

process_exception

process_response
```

## 二、自定义中间件

### **2.1 process_request**

请求会先依次触发中间件中的 process_request 方法，再去执行对应的视图方法。我们通过一个简单的例子来验证一下：

- 第一步，先自定义两个中间件

```python
# app01/MyMiddleware.py

from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")


class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")
```

- 第二步，注册中间件

```python
# settings.py

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'app01.MyMiddleware.MyMiddleware_1',
    'app01.MyMiddleware.MyMiddleware_2',
]
```

- 第三步，编写视图函数进行测试

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^index/', views.index),
]
```

```python
# app01/views.py

from django.shortcuts import render, HttpResponse


def index(request):
    print("index.......")
    return HttpResponse("INDEX")
```

启动浏览器访问 http://127.0.0.1:8000/index/，观察后端打印结果

```
MyMiddleware_1 request
MyMiddleware_2 request
index.......
```

可见，请求是先按照中间件的顺序，**从上而下**执行了所有中间件中的 process_request ，最后才执行视图中的 index。需要注意的是，不能在 process_request 设置 return 值，对于 process_request 方法来说，执行流程是这样的：首先后端接收到请求，接着所有中间件中的 process_request 会对请求进行进一步处理，最后将处理好的结果再传给视图。如果 process_request 中出现了response 相关的 return，后端就会直接将 process_request 中的返回值返回给浏览器，**不会继续执行后面的中间件和视图部分**。

```python
# 自定义的中间件的process_request中含有返回值

from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")
        return HttpResponse("middleware1")


class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")
```

修改中间件后，重新访问 http://127.0.0.1:8000/index/，此时页面显示的是 "middleware1"，后台中打印的结果为 "MyMiddleware_1 request"，后面的中间件 middleware_2 和 视图 index 都没有执行（如果在settings.py中把 middleware_1 和middleware_2的顺序换一下，middleware_2 是可以执行的 ），即

​                            <img src="/static/img/Django专题/process_request.png" style="zoom:67%;" /> 

### **2.2 process_response**

​		视图函数再处理完对应的业务逻辑后，会返回一个 HttpResponse 对象，该对象并不是直接返回给浏览器的，而是先**自下而上**传给每一个中间件中的 process_response 方法，进一步处理后再返回给浏览器。

```python
# 修改自定义的中间件
from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")

    def process_response(self, request, response):
        print("MyMiddleware_1 response")
        return response  # 将返回结果传给上一个中间件


class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")

    def process_response(self, request, response):
        # response 参数就是从视图层获得返回体对象，在这个中间件中处理完以后，继续传给上一个中间件
        print("MyMiddleware_2 response")
        return response  # 将返回结果传给上一个中间件
```

```
# 后台打印结果如下：

MyMiddleware_1 request
MyMiddleware_2 request
index.......
MyMiddleware_2 response
MyMiddleware_1 response

# 可见对于 process_request，执行顺序是按照配置文件中中间件从上到下的顺序；对于 process_response 则是按照从下往上的顺序。
```

​		需要注意的是，对于 process_response 方法，必须要有一个返回值，且返回值一般是自己接收到的那个 response 对象；如果 process_response 返回一个自定义的 HttpResponse 对象也是可以的，这样的话浏览器渲染的就是返回的自定义的对象

```python
from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")

    def process_response(self, request, response):
        print("MyMiddleware_1 response")
        # return response  # 将返回结果传给上一个中间件
        return HttpResponse("OK")  # 不再返回 response 对象，返回自己自定义的内容


class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")

    def process_response(self, request, response):
        # response 参数就是从视图层获得返回体对象，在这个中间件中处理完以后，继续传给上一个中间件
        print("MyMiddleware_2 response")
        return response  # 将返回结果传给上一个中间件

```

```
# 测试结果
此时访问 http://127.0.0.1:8000/index/，页面显示的内容是 "OK"
```

当 process_request 中设有返回值时，process_response 返回的对象将是 process_request 中设置的返回值

```python
from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")
        return HttpResponse("middleware1")  # 自定义一个返回值

    def process_response(self, request, response):
        print("MyMiddleware_1 response")
        return response  # 将返回结果传给上一个中间件
      

class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")

    def process_response(self, request, response):
        # response 参数就是从视图层获得返回体对象，在这个中间件中处理完以后，继续传给上一个中间件
        print("MyMiddleware_2 response")
        return response  # 将返回结果传给上一个中间件
```

```
# 测试结果
此时访问 http://127.0.0.1:8000/index/，页面显示的内容是 "middleware1"
```

总结：

- 只要 process_response 中有自定义的返回值时，最终返回给浏览器的都是该值
- 当 process_response 中没有自定义的返回值，但是 process_request 中有设置的返回值时，最终返回给浏览器的是 process_request 中的返回值
- 当 process_response 中没有自定义的返回值， process_request 也没有设置的返回值时，最终返回给浏览器的是视图的结果

​                            <img src="/static/img/Django专题/process_response.png" style="zoom:67%;" />   

### **2.3 process_view**

```python
from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")

    def process_response(self, request, response):
        print("MyMiddleware_1 response")
        return response  # 将返回结果传给上一个中间件

    def process_view(self, request, callback, callback_args, callback_kwargs):
        print("MyMiddleware_1 view")


class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")

    def process_response(self, request, response):
        # response 参数就是从视图层获得返回体对象，在这个中间件中处理完以后，继续传给上一个中间件
        print("MyMiddleware_2 response")
        return response  # 将返回结果传给上一个中间件

    def process_view(self, request, callback, callback_args, callback_kwargs):
        print("MyMiddleware_2 view")
```

相关参数介绍：

- callback		通过路由层映射找到的要执行的视图函数
- callback_args/callback_kwargs         视图函数的参数

```
# 测试时后端打印结果

MyMiddleware_1 request
MyMiddleware_2 request
MyMiddleware_1 view
MyMiddleware_2 view
index.......
MyMiddleware_2 response
MyMiddleware_1 response
```

可见 process_view 是在 process_request 之后且在视图函数之前执行的，即

​                         <img src="/static/img/Django专题/process_view.png" style="zoom:67%;" />  

当最后一个中间的 process_request 到达路由关系映射之后，返回到中间件1的 process_view，然后依次往下，到达 views 函数，最后通过 process_response 依次返回到达浏览器。

由于参数 callback 实际上就是对应的视图函数的名称空间，我们也可以在 process_view 中直接提前执行视图函数

```python
def process_view(self, request, callback, callback_args, callback_kwargs):
	print(">>>>>>>>>>>>", callback)
	print("MyMiddleware_2 view")
    
    response=callback(request,*callback_args,**callback_kwargs)
    return response
```

同样的，由流程图可以看出，process_view 如果有返回值，会越过其他的 process_view 以及视图函数，但是所有的 process_response 都还会执行。

```python
def process_view(self, request, callback, callback_args, callback_kwargs):
	print("MyMiddleware_2 view")
	return HttpResponse("ok")
```

### **2.4 process_exception**

只有视图中出现错误时，才会触发该方法

```python
from django.utils.deprecation import MiddlewareMixin
from django.shortcuts import HttpResponse


class MyMiddleware_1(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_1 request")

    def process_response(self, request, response):
        print("MyMiddleware_1 response")
        return response  # 将返回结果传给上一个中间件

    def process_view(self, request, callback, callback_args, callback_kwargs):
        print("MyMiddleware_1 view")

    def process_exception(self, request, exception):
        print("CustomerMiddleware1 process_exception")


class MyMiddleware_2(MiddlewareMixin):

    def process_request(self, request):
        print("MyMiddleware_2 request")

    def process_response(self, request, response):
        # response 参数就是从视图层获得返回体对象，在这个中间件中处理完以后，继续传给上一个中间件
        print("MyMiddleware_2 response")
        return response  # 将返回结果传给上一个中间件

    def process_view(self, request, callback, callback_args, callback_kwargs):
        print("MyMiddleware_2 view")
      
    def process_exception(self, request, exception):
        print("CustomerMiddleware2 process_exception")
```

```python
# app01/views.py

from django.shortcuts import render, HttpResponse

def index(request):
    print("index.......")
    aaaa  # 执行到这一行会报错
    return HttpResponse("INDEX")
```

```
# 后台打印结果

MyMiddleware_1 request
MyMiddleware_2 request
MyMiddleware_1 view
MyMiddleware_2 view
index.......
CustomerMiddleware2 process_exception
CustomerMiddleware1 process_exception
MyMiddleware_2 response
MyMiddleware_1 response
```

​		视图函数执行到 aaaa 时会报错，此时不再继续执行后面的代码，而是去执行中间件中的 process_exception 方法，执行顺序是按照中间件从下往上的顺序，如果当前中间件中的 process_exception  没有对异常进行处理，机会继续向上走，直到某一个中间件对异常进行了处理，那么该中间件上面的中间件的 process_exception  就不会执行了，次数会回过头去执行最后一个中间件中的 process_response方法；如果一直没有中间件对异常进行处理，就会返回默认的错响应。

​                  <img src="/static/img/Django专题/process_exception.png" style="zoom:67%;" /> 

```python
# 对异常进行处理

 def process_exception(self, request, exception):
	 print("CustomerMiddleware2 process_exception")
 	# 简单的返回异常
 	return HttpResponse(exception)
```

