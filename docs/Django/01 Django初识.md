# Django初识

## 一、MVC框架和MTV框架

​		MVC（Model View Controller）是软件工程中的一种软件架构模式，它把软件系统分为三个基本部分：模型(Model)、视图(View)、控制器(Controller)。MVC具有耦合性低、重用性高、生命周期成本低等优点。其中，模型负责业务对象与数据库的对象(ORM)，视图负责与用户的交互(页面)，控制器(C)接受用户的输入调用模型和视图完成用户的请求。

​                                                    <img src="/static/img/Django专题/MVC框架.png" style="zoom:50%;" /> 

## 二、Django的MTV框架

Django的MTV模式本质上与MVC模式没有什么差别，也是各组件之间为了保持松耦合关系，只是定义上有些许不同，Django的MTV分别代表：

- Model(模型)：负责业务对象与数据库的对象(ORM) -------- 相当于MVC中的M

- Template(模版)：负责如何把页面展示给用户 -------- 相当于MVC中的V

- View(视图)：负责业务逻辑，并在适当的时候调用Model和Template -------- 相当于MVC中的C

此外，Django还有一个url分发器，它的作用是将一个个URL的页面请求分发给不同的view处理，view再调用相应的Model和Template

<img src="/static/img/Django专题/MTV模式.png" style="zoom: 67%;" /> 

**MTV模式和MVC模式的比对**

​                                          <img src="/static/img/Django专题/MTV模式和MVC模式的比对.png" style="zoom:67%;" />   

## 三、Django的使用

### **3.1 创建项目**

创建一个名为 `mysit` 的 Django 项目：

- 进入 cmd 终端，切换到想要存放项目的目录
- 输入创建项目命令：django-admin startproject mysite

### **3.2 项目的目录结构**

<img src="/static/img/Django专题/django项目目录.png" style="zoom:67%;" /> 

### **3.3 创建APP**

​		对于一个项目而言，我们不可能把所有的功能函数都写在一个文件里面，我们需要根据不同的功能进行app的划分（相当于多分几个模块），比如对于一个学生管理的系统，我们可以把用户登录注册的功能放到一起写，把老师的相关操作的功能划分到一起，把学生的相关功能划分到一起等等，这就需要创建多个app了

#### **3.3.1 创建app的命令**

```python
python manage.py startapp app的名字
```

**例如：**

```
# 创建用户相关的app
python manage.py startapp user

# 创建学生相关的app
python manage.py startapp student

# 创建老师相关的app
python manage.py startapp teacher
```

此时我们可以发现项目目录变成了

<img src="/static/img/Django专题/app创建.png" style="zoom:80%;" /> 

#### **3.3.2 app注册**

我们再创建完app后，必须要让django知道我们添加了新的app，所以要在 settings.py中对新创建的app进行注册

<img src="/static/img/Django专题/app注册.png" style="zoom: 67%;" /> 

### **3.4 运行项目**

**运行项目的命令：**

```python
python manage.py runserver  ip:端口号

# 例如：python manage.py runserver  127.0.0.1:8080
```

### **3.5 Django项目的配置文件**

创建的django项目中的 settings.py 中存放着项目相关的一些配置信息，我们可以根据实际的需求进行修改

- **ALLOWED_HOSTS**		允许访问该项目的IP信息的相关设置（即设置可以访问的白名单）

```python
# ALLOWED_HOSTS = []  # 初识设置
# ALLOWED_HOSTS = ["127.0.0.1", "192.168.1.2"]  # 只允许这些IP访问项目
ALLOWED_HOSTS = ["*"]  # 允许所有IP访问项目
```

- **INSTALLED_APPS**		用于注册创建的app

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    "user.apps.UserConfig",        # 注册用户app
    "student.apps.StudentConfig",  # 注册学生app
    "teacher.apps.TeacherConfig"   # 注册教师app
]
```

- **MIDDLEWARE**		用于管理django的中间件，如果我们自己编写了新的中间件，也需要在这边注册

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',  # 如果发生跨域问题，将此行注释掉即可
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

- **ROOT_URLCONF**		规定寻找路由和视图映射的入口

```python
ROOT_URLCONF = 'Django初识.urls'
# ROOT_URLCONF = 'student.urls'  # 此时接收到浏览器请求后将会去student.urls寻找路由和视图的映射关系
```

- **TEMPLATES**		存放模板相关的操作

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')]
        ,
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

- **DATABASES**		数据库相关操作

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

- **STATIC_URL**		用于设置项目静态文件相关存储，它会给静态文件的存储路径起一个别名，当使用到静态文件时，引用的路径必须以别名开头。别名只是告知要开始查找应用静态文件，而并非是静态文件的实际存储位置

```python
STATIC_URL = '/static/'
```

- **STATICFILES_DIR**		静态文件实际的存储位置，需要手动建立该变量

```python
STATICFILES_DIR = [
    os.path.join(BASE_DIR, "static")
]
```

## 四、Django的生命周期

<img src="/static/img/Django专题/django的生命周期.png" style="zoom: 67%;" />  

浏览器发送请求，django接收请求并进行相应的处理，最后将结果返回给浏览的这个过程如上图所示。

1. 由wsgiref模块接收到浏览器的请求
2. 在请求到达路由层（urls.py）之前会经过中间件，中间件会先对请求进行一个预处理
3. 请求到达路由层，通过路由层找到对应的视图函数
4. 视图函数根据请求进行处理，如果请求涉及到模板和数据库，视图函数会将对应的模板和数据库中的数据进行组合渲染，最后将结果返回
5. 再最后的结果返回给wsgiref模块之前还是会先经过中间件，中间件会对返回结果进行进一步的处理
6. 返回结果处理完后，返回给wsgiref模块
7. wsgiref模块将最后的结果返回给浏览器，浏览器对结果进行解析和展示