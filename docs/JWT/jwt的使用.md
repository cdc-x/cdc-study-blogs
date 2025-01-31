# JWT 技术介绍

## 一、jwt 简介

`Json web token` （简称 jwt）, 是为了在网络应用环境间传递声明而执行的一种基于 `JSON` 的开放标准（RFC 7519）。该 token 被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）场景。`jwt` 的声明一般被用来在身份提供者（客户端）和服务提供者（服务端）间传递被认证的用户身份信息，以便于从资源服务器获取资源，也可以增加一些额外的其它业务逻辑所必须的声明信息，该 token 也可直接被用于身份认证和数据加密传输。

jwt 官网：https://jwt.io/

<img src="./static/img/jwt/jwt_1.png" style="zoom: 50%;" />  

## 二、jwt 的实现原理

### 1. jwt 的构成

`jwt` 本质上就是一段字符串，该字符串由三段加密字符串通过 `.` 号连接而成，例如：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

每段加密信息的作用及生成方式如下：

#### 1.1 HEADER

第一段信息称为 **HEADER**，即头部信息，里面主要声明了加密的算法和 token 的类型

- typ：声明 token 类型，默认是 jwt ，也可以是 Bear 等其他类型；
- alg：声明签证的加密的算法，通常直接使用 HMAC 或 SHA256

完整的头部信息定义如下：

```json
{
  'typ': 'JWT',
  'alg': 'HS256'
}
```

对头部信息进行 `base64url` 加密就可以得到 jwt 的第一段加密信息部分

```python
import base64
import json

header_data = {
    "typ": "jwt",
    "alg": "HS256"
}

header = base64.urlsafe_b64encode(json.dumps(header_data).encode()).replace(b'=', b'').decode()

# eyJ0eXAiOiAiand0IiwgImFsZyI6ICJIUzI1NiJ9
print(header)
```

#### 1.2 payload

第二段信息称为 **payload** ，即载荷信息，里面可以包含一些有效信息，这些信息大致可以分为三种：

- **标准声明**：实现 jwt 规范中要求的属性。 (官方建议但不强制使用)
  - iss：jwt 签发者
  - sub：jwt 所面向的用户
  - **aud**：接收 jwt 的一方
  - **exp**：jwt 的过期时间，这个过期时间必须要大于签发时间
  - **nbf**：在什么时间之后，该 jwt 才可以使用
  - **iat**：jwt 的签发时间
  - **jti**：jwt 的唯一身份标识，主要用来作为一次性 token，从而回避重放攻击
- **公共声明**：公共声明可以添加任何的公开信息，一般添加用户的相关信息或其他业务需要的必要信息（如用户名、用户ID 等）。但不建议添加敏感信息，因为该部分在客户端可直接读取。
- **私有声明**：私有声明是服务端和客户端所共同定义的声明，一般不建议存放敏感信息，里面存放的是一些可以在服务端或者客户端通过秘钥进行加密和解密的加密信息，往往采用的 RSA 非对称加密算法。

完整的载荷信息如下：

```json
{
    "sub": "root",
    "exp": 1717399000,
    "iat": 1717397694,
    "name": "wangxiaoming",
    "avatar": "1.png",
    "user_id": 1,
    "admin": true,
    "acc_pwd": "QiLCJhbGciOiJIUzI1NiJ9QiLCJhbGciOiJIUzI1NiJ9QiLCJhbGciOiJIUzI1NiJ9",
}
```

对载荷信息进行 `base64url` 加密可以得到 `jwt` 的第二部分加密信息

```python
import base64
import json
import time

# 载荷信息
iat = int(time.time())
payload = {
    "sub": "root",
    "exp": iat + 3600,  # 假设一小时过期
    "iat": iat,
    "name": "cdc",
    "avatar": "1.png",
    "user_id": 1,
    "admin": True,
    "acc_pwd": "QiLCJhbGciOiJIUzI1NiJ9QiLCJhbGciOiJIUzI1NiJ9QiLCJhbGciOiJIUzI1NiJ9",
}
payload = base64.urlsafe_b64encode(json.dumps(payload).encode()).replace(b'=', b'').decode()

# eyJzdWIiOiAicm9vdCIsICJleHAiOiAxNzE3NDAxNDczLCAiaWF0IjogMTcxNzM5Nzg3MywgIm5hbWUiOiAiY2RjIiwgImF2YXRhciI6ICIxLnBuZyIsICJ1c2VyX2lkIjogMSwgImFkbWluIjogdHJ1ZSwgImFjY19wd2QiOiAiUWlMQ0poYkdjaU9pSklVekkxTmlKOVFpTENKaGJHY2lPaUpJVXpJMU5pSjlRaUxDSmhiR2NpT2lKSVV6STFOaUo5In0=
print(payload)
```

#### 1.3 signature

第二段信息称为 **signature** ，即签证信息，用于辨别 token 的真伪，防止被篡改。签证信息实现的过程比较负载，步骤如下：

- 第一步，将头部信息和载荷信息的密文使用 `.` 拼接
- 第二步，通过头部信息中声明的加密方式，结合密钥进行加盐组合加密（秘钥必须保存在服务端，不提供给客户端，这样可以保证客户端没有签发token的能力）

### 2. jwt 的校验

对客户端传来的 jwt ，服务端要进行合法校验，主要分为以下三个步骤：

- 第一步，对 token 进行切割，得到三段密文信息
- 第二步，对第二段信息进行解密，校验认证信息是否过期；对第一段信息进行解密，获取加密算法方式
- 第三步，将第一段和第二段密文，使用本地密钥，结合第一段中解析出来的加密算法进行加盐组合加密，重新得到一份密文
- 第四步，将重新得到的密文与第三段密文进行对比，如果一样则认证通过，不一样则证明 token 被篡改，认证失败

## 三、jwt 的使用

### 1. 基于传统Token机制实现用户认证

#### 1.1 用户认证流程

<img src="./static/img/jwt/jwt_2.png" style="zoom: 80%;" /> 

#### 1.2 用户认证流程实现

- 路由

```python
# JwtProject/urls.py

from django.contrib import admin
from django.urls import path
from api.views import *

urlpatterns = [
    # 基于传统的token认证
    path('api/login/', LoginView.as_view()),
    path('api/order/', OrderView.as_view()),
]
```

- 用户表模型

```python
# JwtProject/api/models.py

from django.db import models

class UserInfo(models.Model):
    username = models.CharField(max_length=32, primary_key=True)
    password = models.CharField(max_length=16, null=False)
    token = models.CharField(max_length=64, null=True)
    last_login_time = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.username

    class Meta:
        db_table = "user"
        verbose_name_plural = db_table
```

- 登录和查询的视图

```python
# JwtProject/api/views.py

import uuid
from django.http import JsonResponse
from django.views import View
from .models import UserInfo


class LoginView(View):

    def post(self, request):
        user_name = request.POST.get("username")
        pwd = request.POST.get("password")

        # 校验用户是否存在
        user = UserInfo.objects.filter(username=user_name, password=pwd).first()

        if not user:
            return JsonResponse({"code": 1001, "error": "用户名或密码不正确"})

        # 如果用户存在，创建token
        user.token = str(uuid.uuid4())
        user.save()

        return JsonResponse({"code": 1000, "token": user.token})


class OrderView(View):

    def get(self, request):
        # 获取token，校验用户身份
        token = request.GET.get("token")
        user = UserInfo.objects.filter(token=token).first()

        if not user:
            return JsonResponse({"code": 1001, "error": "用户未登录"})

        # 校验 token 是否过期
        exp = datetime.datetime.now() - user.last_login_time.replace(tzinfo=None)
        if exp.days > 1:
            return JsonResponse({"code": 1001, "message": "认证信息过期"})

        return JsonResponse({"code": 1000, "message": "订单列表"})
```

### 2. 基于 jwt 机制实现用户认证

#### 2.1 用户认证流程

<img src="./static/img/jwt/jwt_3.png" style="zoom: 70%;" />  



与传统的 token 认证机制相比，jwt 生成的 token 是不需要存储到服务端数据库中的，且也不用手动校验认证信息是否过期。

#### 2.2 用户认证流程实现

- 安装三方库

```powershell
pip install pyJWT
```

- 登录和查询的视图

```python
# JwtProject/api/views.py

import uuid
import jwt
import datetime
from jwt import exceptions
from django.http import JsonResponse
from django.views import View
from django.conf import settings
from .models import UserInfo

class JwtLoginView(View):

    def post(self, request):
        user_name = request.POST.get("username")
        pwd = request.POST.get("password")

        # 校验用户是否存在
        user = UserInfo.objects.filter(username=user_name, password=pwd).first()

        if not user:
            return JsonResponse({"code": 1001, "error": "用户名或密码不正确"})

        # 头部信息
        header = {
            "typ": "jwt",
            "alg": "HS256"
        }

        # 载荷信息
        payload = {
            "name": user.username,
            "exp": datetime.datetime.utcnow() + datetime.timedelta(minutes=5)
        }

        token = jwt.encode(payload=payload, key=settings.SECRET_KEY, algorithm="HS256", headers=header).decode("utf-8")

        return JsonResponse({"code": 1000, "token": token})


class JwtOrderView(View):

    def get(self, request):
        # 获取token，校验用户身份
        token = request.GET.get("token")

        # 第一步，切割 jwt
        # 第二步，解密第二段，校验是否过期
        # 第三步，校验第三段是否正确

        payload = None
        msg = None

        try:
            payload = jwt.decode(token, settings.SECRET_KEY, True)
            print(payload)
        except exceptions.ExpiredSignatureError:
            msg = "签证已失效"
        except exceptions.DecodeError:
            msg = "token认证失败"
        except exceptions.InvalidTokenError:
            msg = "非法token"

        if not payload:
            return JsonResponse({"code": 1001, "message": msg})

        return JsonResponse({"code": 1000, "message": "订单列表"})
```

### 3. drf 集成 jwt

在 `DRF` 中使用 jwt 的方式和上述流程差不多，一般会在 `DRF` 的用户认证过程中集成使用 jwt 。

- 配置文件修改

```python
# JwtProject/JwtProject/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'api.apps.ApiConfig',
    'rest_framework'
]


REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": ["extensions.auth.JwtTokenAuth", ]
}
```

- 新增认证组件

```python
# JwtProject/extensions/auth.py

import jwt
from rest_framework.authentication import BaseAuthentication
from rest_framework.exceptions import AuthenticationFailed
from django.conf import settings
from jwt import exceptions


class JwtTokenAuth(BaseAuthentication):

    def authenticate(self, request):
        # 获取token，校验用户身份
        token = request.GET.get("token")
        payload = None

        try:
            payload = jwt.decode(token, settings.SECRET_KEY, True)
        except exceptions.ExpiredSignatureError:
            raise AuthenticationFailed({"code": 1001, "error": "签证已失效"})
        except exceptions.DecodeError:
            raise AuthenticationFailed({"code": 1001, "error": "token认证失败"})
        except exceptions.InvalidTokenError:
            raise AuthenticationFailed({"code": 1001, "error": "非法token"})

        return payload, token

    def authenticate_header(self, request):
        return 'Bearer realm="API"'
```

- 将生成 token 的方法抽成公共方法

```python
# JwtProject/extensions/utils.py

import jwt
from django.conf import settings


def create_token(payload, timeout):
    # 头部信息
    header = {
        "typ": "jwt",
        "alg": "HS256"
    }

    payload["exp"] = timeout

    token = jwt.encode(
        payload=payload,
        key=settings.SECRET_KEY,
        algorithm="HS256",
        headers=header).decode("utf-8")

    return token
```

- 编写对应的视图类

```python
import datetime
from .models import UserInfo
from extensions.utils import create_token
from rest_framework.views import APIView
from rest_framework.response import Response

class LoginView(APIView):

    authentication_classes = []

    def post(self, request):
        user_name = request.data.get("username")
        pwd = request.data.get("password")

        # 校验用户是否存在
        user = UserInfo.objects.filter(username=user_name, password=pwd).first()

        if not user:
            return JsonResponse({"code": 1001, "error": "用户名或密码不正确"})

        # 载荷信息
        payload = {
            "name": user.username,
        }

        # 生成token
        token = create_token(payload, datetime.datetime.utcnow() + datetime.timedelta(minutes=1))

        return Response({"code": 1000, "token": token})


class OrderView(APIView):

    def get(self, request):
        return Response({"code": 1000, "message": "订单列表"})
```

### 4. drf 拓展的 jwt 组件

除了集成 `pyJWT` 三方库，`DRF` 自身也提供了 jwt 相关组件 `djangorestframework-jwt`，该组件底层也是通过 `pyJWT` 实现的，且应用场景比较局限（例如只能使用在 `DRF` 框架中，普通的 `Django` 无法使用，且必须搭配 `Django` 的 `Auth` 认证组件使用），因此一般项目中很少会使用，推荐使用集成 `pyJWT` 方式。

官方文档：https://jpadilla.github.io/django-rest-framework-jwt/

#### 4.1 drf_jwt 的使用

- 第一步，安装组件

```powershell
pip install djangorestframework-jwt
```

- 第二步，组件配置

```python
# settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    "app01.apps.App01Config",
    
    # 必须注册这两个app
    "rest_framework",
    "rest_framework_jwt",
]

# jwt认证相关配置项
# 更多配置项可以参考官网
JWT_AUTH = {
    # 设置jwt的有效期
    'JWT_EXPIRATION_DELTA': datetime.timedelta(weeks=1),  # 一周有效，
    # 允许刷新token
    'JWT_ALLOW_REFRESH': True,
    # 生成 token 后，最大的有效时间：在有效期内通过刷新可以保持token有效；超过这个时间后，token失效，刷新也不起作用
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=30),
}

# djangorestframework-jwt 必须搭配 Auth 组件使用
# 设置用户认证组件操作的表
AUTH_USER_MODEL = "app01.UserInfo"
```

- 第三步，构建用户表模型

```python
# models.py

from django.contrib.auth.models import AbstractUser

# 直接继承认证模块中提供的用户表基类
# 缺点：认证模块用户表中的字段太多，对实际业务来说比较冗余
class UserInfo(AbstractUser):
    class Meta:
        db_table = "user_info"
        verbose_name_plural = db_table
```

- 第四步，执行数据库迁移操作

```powershell
python manage.py makemigrations
python manage.py migrate  
```

- 第五步，先创建一个用户，用于测试使用

```powershell
# 执行命令
python manage.py createsuperuser

# 输入相关信息
Username: cdc
Email address:
Password:
Password (again):
This password is too short. It must contain at least 8 characters.
This password is too common.
This password is entirely numeric.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.
```

<img src="./static/img/jwt/jwt_4.png" style="zoom: 50%;" /> 

- 第六步，登录路由

```python
# urls.py

from django.urls import path
from app01 import views

urlpatterns = [
    path('login/', views.LoginView.as_view()),
]
```

- 第七步，编写用户登录校验视图

```python
# views.py

from rest_framework_jwt.settings import api_settings
from rest_framework.views import APIView
from rest_framework.response import Response
from django.contrib import auth


class LoginView(APIView):

    def post(self, request):
        user_name = request.data.get("username")
        pwd = request.data.get("password")

        # 使用auth组件进行认证
        # 认证成功会返回一个user对象，失败返回一个None
        user = auth.authenticate(username=user_name, password=pwd)

        if user:
            # 使用组件生成 jwt
            jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
            # 获取token生成函数
            jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER
            # user用户模型对象
            payload = jwt_payload_handler(user)
            # 生成token
            token = jwt_encode_handler(payload)

            ret_data = {"code": 1000, "token": token}
        else:
            ret_data = {"code": 1001, "error": "登录失败"}

        return Response(ret_data)
```

注意：由于用户模型是继承了认证组件中的模型表，因此前端传参的字段名必须和表中一致，即用户名字段名只能是 `username`，密码字段名必须是 `password`

<img src="./static/img/jwt/jwt_5.png" style="zoom: 60%;" /> 

#### 4.2 drf_jwt 的常用视图接口

`djangorestframework-jwt` 组件的视图层已经帮我们封装好了一系列的接口，方便我们快速对 token 进行操作

##### 4.2.1 获取token

```python
# urls.py

from django.urls import path
from rest_framework_jwt.views import obtain_jwt_token

urlpatterns = [
    path('login/', obtain_jwt_token),
]
```

`obtain_jwt_token` 接口包含了对用户登录进行校验，如果登录成功，则直接返回 token，否则返回报错信息。此时我们不用再手动编写校验登录的视图了，直接访问即可：

<img src="./static/img/jwt/jwt_6.png" style="zoom: 60%;" /> 

##### 4.2.2 刷新 token

依靠旧的 token 生成新的 token，因此前端传参时必须要将旧的 token 传过来：

```python
# urls.py

from django.urls import path
from rest_framework_jwt.views import refresh_jwt_token

urlpatterns = [
    path('refresh/', refresh_jwt_token),
]
```

<img src="./static/img/jwt/jwt_7.png" style="zoom: 60%;" /> 

##### 4.2.3 认证 token 是否有效

```python
# urls.py

from django.urls import path
from rest_framework_jwt.views import verify_jwt_token

urlpatterns = [
    path('verify/', verify_jwt_token),
]
```

<img src="./static/img/jwt/jwt_8.png" style="zoom: 60%;" /> 



#### 4.3 drf_jwt 用于接口认证

- 第一步，修改配置

```python
# settings.py

# drf全局配置
REST_FRAMEWORK = {
    # 由于使用了 Django 的 Auth 组件
    # 身份认证必须和权限认证一起使用
    'DEFAULT_AUTHENTICATION_CLASSES': ['rest_framework_jwt.authentication.JSONWebTokenAuthentication'],
    'DEFAULT_PERMISSION_CLASSES': ["rest_framework.permissions.IsAuthenticated"]
}
```

- 第二步，添加路由

```python
# urls.py

from django.urls import path
from app01 import views
from rest_framework_jwt.views import obtain_jwt_token

urlpatterns = [
    # 用户登录
    path('login/', obtain_jwt_token),
    # 访问订单列表
    path('order/', views.ListOrderView.as_view()),
]
```

- 第三步，编写视图

```python
# views.py

from rest_framework.views import APIView
from rest_framework.response import Response

class ListOrderView(APIView):

    def get(self, request):
        return Response("订单列表")
```

- 请求接口时未携带 token，访问失败

<img src="./static/img/jwt/jwt_9.png" style="zoom: 60%;" /> 

- 请求接口时，必须要在请求头中添加 `Authorization` 字段，值为 `jwt + 空格 + 用户登录后返回的token` （jwt 大小写都行）

<img src="./static/img/jwt/jwt_10.png" style="zoom: 60%;" /> 

注意：不用担心设置全局身份验证会对 `djangorestframework-jwt` 提供的视图接口造成影响，源码中接口已屏蔽了认证功能

<img src="./static/img/jwt/jwt_11.png" style="zoom: 80%;" /> 

#### 4.4 drf_jwt 接口的拓展

##### 4.4.1 重写接口方法

`djangorestframework-jwt` 提供的视图接口的确可以帮助我们减少开发量，但在一些业务场景中，这些接口功能过于简单，无法满足我们的业务诉求。以登录接口 `obtain_jwt_token` 为例，只是对用户的账号和密码做了校验，实际场景中，我们在对用户账号密码进行校验前，还有可能会进行其他的额外校验，防止网络攻击和爬虫（例如图片、短信、验证码校验）

```python
# rest_framework_jwt/views.py 部分源码

class JSONWebTokenAPIView(APIView):
    permission_classes = ()
    authentication_classes = ()
    
    ......

class ObtainJSONWebToken(JSONWebTokenAPIView):
    serializer_class = JSONWebTokenSerializer


class VerifyJSONWebToken(JSONWebTokenAPIView):
    serializer_class = VerifyJSONWebTokenSerializer


class RefreshJSONWebToken(JSONWebTokenAPIView):
    serializer_class = RefreshJSONWebTokenSerializer


obtain_jwt_token = ObtainJSONWebToken.as_view()
refresh_jwt_token = RefreshJSONWebToken.as_view()
verify_jwt_token = VerifyJSONWebToken.as_view()
```

因此我们可以重写这些接口，丰富我们的功能：

- 路由

```python
# urls.py

from django.urls import path
from app01 import views

urlpatterns = [
    # 重写登录接口
    path('login2/', views.MyLoginView.as_view()),
]
```

- 自定义视图类

```python
# views.py

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework_jwt.views import ObtainJSONWebToken
from rest_framework import status

# 视图类直接继承 ObtainJSONWebToken
class MyLoginView(ObtainJSONWebToken):

    def sms_code_validate(self):
        """
        模拟发送短信验证
        :return:
        """

        print("发送短信验证码")
        return True

    def post(self, request, *args, **kwargs):
        try:
            # 模拟发送短信验证码校验
            validate_result = self.sms_code_validate()

            if validate_result:
                # 验证通过
                # 登录实现代码，调用父类实现的登录视图方法
                return super().post(request, *args, **kwargs)
            else:
                # 如果返回值不是True，则表示验证失败
                raise ValueError("验证码错误")
        except Exception as err:
            return Response({"errmsg": "验证码校验失败！"}, status=status.HTTP_400_BAD_REQUEST)
```

##### 4.4.2 自定义载荷

使用 `djangorestframework-jwt` 生成 token 时，默认调用了组件内置的 `jwt_payload_handler` 方法，生成的载荷信息也是固定的

<img src="./static/img/jwt/jwt_12.png" style="zoom: 90%;" /> 

我们也可以重写 `jwt_payload_handler`  接口，将我们需要的额外信息放入载荷

- 重写 `jwt_payload_handler` 方法

```python
# app01/extensions/utils.py

from rest_framework_jwt.utils import jwt_payload_handler as payload_handler

def jwt_payload_handler(user):
    """
    自定义载荷信息
    :params user  用户模型实例对象
    """
    # 先让jwt模块生成自己的载荷信息
    payload = payload_handler(user)

    # 追加自己要返回的内容
    payload["sms"] = 1234
    payload["status"] = True
    payload["content"] = "哈哈哈哈"

    return payload
```

- 修改配置文件

```python
# settings.py

JWT_AUTH = {
    # 设置jwt的有效期
    # 如果内部站点，例如：运维开发系统，OA，往往配置的access_token有效期基本就是15分钟，30分钟，1~2个小时
    'JWT_EXPIRATION_DELTA': datetime.timedelta(weeks=1),  # 一周有效，
    # 允许刷新token
    'JWT_ALLOW_REFRESH': True,
    # 生成 token 后，最大的有效时间：在有效期内通过刷新可以保持token有效；超过这个时间后，token失效，刷新也不起作用
    'JWT_REFRESH_EXPIRATION_DELTA': datetime.timedelta(days=30),
    
    # 自定义载荷
    'JWT_PAYLOAD_HANDLER': 'app01.extensions.utils.jwt_payload_handler',
}
```

- 重新登录，获取 token，解析载荷部分

<img src="./static/img/jwt/jwt_13.png" style="zoom: 100%;" /> 

