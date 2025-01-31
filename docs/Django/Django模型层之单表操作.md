# ORM初识

## 一	ORM简介

​		我们在使用Django框架开发web应用的过程中，不可避免地会涉及到数据的管理操作（增、删、改、查），而一旦谈到数据的管理操作，就需要用到数据库管理软件，例如mysql、oracle等。如果应用程序需要操作数据（比如将用户注册信息永久存放起来），那么我们需要在应用程序中编写原生sql语句，然后使用pymysql模块远程操作mysql数据库，但是直接编写原生sql语句会存在两方面的问题，严重影响开发效率：

- sql语句的执行效率：应用开发程序员需要耗费一大部分精力去优化sql语句
- 数据库迁移：针对mysql开发的sql语句无法直接应用到oracle数据库上，一旦需要迁移数据库，便需要考虑跨平台问题

为了解决上述问题，django引入了ORM的概念，ORM全称Object Relational Mapping，即对象关系映射，是在pymysq之上又进行了一层封装，对于数据的操作，我们无需再去编写原生sql，取代代之的是基于面向对象的思想去编写类、对象、调用相应的方法等，ORM会将其转换/映射成原生SQL然后交给pymysql执行。

原生SQL和ORM的对应关系：

 <img src="/static/img/ORM与DB的映射关系.png" style="zoom:67%;" />

## 二	ORM的简单使用

#### **1、创建app并在配置项中注册**

app创建后必须注册，否则django无法识别该app，在该app中进行的ORM相关操作也不会生效

```
# 在终端创建app01
python manage.py startapp app01
```

```python
# 在 settings.py 中注册app

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app01.apps.App01Config',
]
```

#### **2、配置数据库连接信息**

django中默认使用的数据库是sqlite，如果想要使用其他的数据库，需要在配置文件中对数据库配置进行修改

```python
# 在 settings.py 中修改数据库相关配置信息

# 默认的配置
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',  # 数据库的连接类型
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}


# 修改成使用mysql数据库
DATABASES = {
    "default": {
        'ENGINE': 'django.db.backends.mysql',  # 连接的数据库类型
        "NAME": "orm_db",  # 库的名字，需要手动创建该数据库
        "USER": "root",  # 登录的用户名
        "PASSWORD": "",  # 登录密码
        "HOST": "127.0.0.1",  # 数据库的主机地址
        "PORT": 3306  # 数据库的连接端口
    }
}
```

注意：记住一定要手动建立数据库，且数据库的名字要和配置文件中的一样

#### **3、修改数据库操作模块**

在上面的简介中我们知道，ORM是将相关的操作转换/映射成原生SQL然后交给pymysql执行，但是django中默认使用的是python2中的MQLdb模块来操作数据库的，我们需要手动更换成pymysql模块。

```python
# django项目中的 __init__.py

import pymysql
pymysql.install_as_MySQLdb()
```

#### **4、创建表**

在ORM中，通过类的方式来创建数据表，如果有多个模块，每个模块自己的表操作要写在自己的models.py中

```python
# app01/models.py

from django.db import models

class User(models):  # 创建的类必须继承 models.Model
    id = models.AutoField(primary_key=True)  # 创建一个自增的ID作为主键
    name = models.CharField(max_length=32)  # 创建一个长度为32的字符串，相当于原生的sql varchar(32)
    pwd = models.CharField(max_length=16)  
```

#### **5、数据库记录变更**

##### **5.1、记录数据库的变更**

```
# 这个命令做的事情就是，找个小本本把models.py的变更记录一下，执行后会在模块下的对应的migrations文件夹下新增一条记录
python manage.py makemigrations
```

##### **5.2、执行变更**

```
# 这条命令会把上面的变更记录翻译成SQL语句，去数据库执行
python manage.py migrate
```

注意：

- migrations文件夹中记录的变更文件都是带序号的且递增的，这些记录都是再上一条记录的基础上再次记录的；
- 一旦使用了orm方式来操作数据库，涉及到表结构相关的操作时，最好都用orm方式来操作，否则会出错。例如：先通过ORM方式建了一张user表，此时就会有对应的建表的变更记录；接着手动去数据库中将该表删除，再回过头来使用ORM方式来往表里面插入数据，此时会报错。因为ORM的变更记录里只有建表的记录，删表操作不是通过ORM来实现的，因此没有删表的变更记录；但是实际上该表已经手动被删除掉了，但是ORM并不知道，所以再通过ORM操作的时候，会报错。（只有涉及到表结构的时候要注意，只是对表中数据的修改时不用）

按照上述步骤执行后，检查数据库，发现建表成功。

<img src="/static/img/orm初识建表.png" style="zoom:80%;" /> 

## 三	ORM的字段

#### **常用字段**

|            字段            | 描述                                                         | 和原生的SQL的对应关系  |
| :------------------------: | :----------------------------------------------------------- | :--------------------: |
|         AutoField          | 自增的整形字段，一般和参数primary_key=True配合使用，成为数据库的主键。<br>当model中如果没有自增列，则自动会创建一个列名为id的列。一个model不能有两个AutoField字段。 | integer AUTO_INCREMENT |
|        BigAutoField        | bigint自增列，必须填入参数 primary_key=True                  | bigint AUTO_INCREMENT  |
|        IntegerField        | 一个整数类型,范围在 -2147483648 to 2147483647                |        integer         |
|         CharField          | 字符类型，必须提供max_length参数， max_length表示字符长度    |        varchar         |
|         DateField          | 日期字段，日期格式 YYYY-MM-DD，相当于Python中的datetime.date()实例 |          date          |
|       DateTimeField        | 日期时间字段，格式 YYYY-MM-DD HH:MM\[:ss[.uuuuuu\]]\[TZ]，相当于Python中的datetime.datetime()实例。 |        datetime        |
|     SmallIntegerField      | 小整数 -32768 ～ 32767                                       |        smallint        |
| PositiveSmallIntegerField  | 正小整数 0 ～ 32767                                          |   smallint UNSIGNED    |
|    PositiveIntegerField    | 正整数 0 ～ 2147483647                                       |    integer UNSIGNED    |
|      BigIntegerField       | 长整型(有符号的) -9223372036854775808 ～ 9223372036854775807 |         bigint         |
|        BooleanField        | 布尔值类型                                                   |          bool          |
|      NullBooleanField      | 可以为空的布尔值                                             |          bool          |
|         TextField          | 文本类型                                                     |        longtext        |
|         EmailField         | 字符串类型，Django Admin以及ModelForm中提供验证机制          |                        |
|       IPAddressField       | 字符串类型，Django Admin以及ModelForm中提供验证 IPV4 机制    |                        |
|   GenericIPAddressField    | 字符串类型，Django Admin以及ModelForm中提供验证 Ipv4和Ipv6<br>参数：<br>            protocol，用于指定 Ipv4 或 Ipv6，取值可以是  'both',  "ipv4",  "ipv6" <br>            unpack_ipv4， 如果指定为True，则输入::ffff:192.0.2.1时候，可解析为192.0.2.1，开启此功能，需要protocol="both" |        char(39)        |
|          URLField          | 字符串类型，Django Admin以及ModelForm中提供验证 URL          |                        |
|         SlugField          | 字符串类型，Django Admin以及ModelForm中提供验证支持 字母、数字、下划线、连接符（减号） |        varchar         |
| CommaSeparatedIntegerField | 字符串类型，格式必须为逗号分割的数字                         |        varchar         |
|         UUIDField          | 字符串类型，Django Admin以及ModelForm中提供对UUID格式的验证  |        char(32)        |
|       FilePathField        | 字符串，Django Admin以及ModelForm中提供读取文件夹下文件的功能<br>参数：<br>            path                                      文件夹路径                 <br>            match=None,                     正则匹配                 <br>            recursive=False,                递归下面的文件夹                 <br>            allow_files=True,             允许文件                 <br>            allow_folders=False,       允许文件夹 |        varchar         |
|         FileField          | 字符串，路径保存在数据库，文件上传到指定目录 <br>参数：             <br>            upload_to = ""                   上传文件的保存路径             <br>             storage = None                  存储组件，默认django.core.files.storage.FileSystemStorage |        varchar         |
|         ImageField         | 字符串，路径保存在数据库，文件上传到指定目录<br>参数：            <br>           upload_to = ""             上传文件的保存路径             <br>           storage = None             存储组件，默认django.core.files.storage.FileSystemStorage             <br>            width_field=None     上传图片的高度保存的数据库字段名（字符串）             <br>            height_field=None   上传图片的宽度保存的数据库字段名（字符串） |                        |
|         TimeField          | 时间格式      HH:MM[:ss[.uuuuuu]]                            |          time          |
|       DurationField        | 长整数，时间间隔，数据库中按照bigint存储，ORM中获取的值为datetime.timedelta类型 |         bigint         |
|         FloatField         | 浮点型                                                       |    double precision    |
|        DecimalField        | 10进制小数         <br>参数：             <br>             max_digits                小数总长度             <br>              decimal_places       小数位长度 |        numeric         |
|        BinaryField         | 二进制类型                                                   |        longblob        |

#### **自定义字段**

自定义一个char字段

```python
# app01/models.py

class MyCharField(models.Field):
    """
    自定义的char类型的字段类
    """
    def __init__(self, max_length, *args, **kwargs):
        self.max_length = max_length
        super(MyCharField, self).__init__(max_length=max_length, *args, **kwargs)
 
    def db_type(self, connection):
        """
        限定生成数据库表的字段类型为char，长度为max_length指定的值
        """
        return 'char(%s)' % self.max_length
        
        
 class Class(models.Model):
    id = models.AutoField(primary_key=True)
    title = models.CharField(max_length=25)
    # 使用自定义的char类型的字段
    cname = MyCharField(max_length=25)
```

## 四 ORM字段的参数

#### **常用字段参数**

```
    null                数据库中字段是否可以为空
    db_column           数据库中字段的列名
    default             数据库中字段的默认值
    primary_key         数据库中字段是否为主键
    db_index            数据库中字段是否可以建立索引
    unique              数据库中字段是否可以建立唯一索引
    unique_for_date     数据库中字段【日期】部分是否可以建立唯一索引
    unique_for_month    数据库中字段【月】部分是否可以建立唯一索引
    unique_for_year     数据库中字段【年】部分是否可以建立唯一索引
 
    verbose_name        Admin中显示的字段名称
    blank               Admin中是否允许用户输入为空
    editable            Admin中是否可以编辑
    help_text           Admin中该字段的提示信息
    choices             Admin中显示选择框的内容，用不变动的数据放在内存中从而避免跨表操作
                        如：gf = models.IntegerField(choices=[(0, '何穗'),(1, '大表姐'),],default=1)
 
    error_messages      自定义错误信息（字典类型），从而定制想要显示的错误信息；
                        字典健：null, blank, invalid, invalid_choice, unique, and unique_for_date
                        如：{'null': "不能为空.", 'invalid': '格式错误'}
 
    validators          自定义错误验证（列表类型），从而定制想要的验证规则
                        from django.core.validators import RegexValidator
                        from django.core.validators import EmailValidator,URLValidator,DecimalValidator,\
                        MaxLengthValidator,MinLengthValidator,MaxValueValidator,MinValueValidator
                        如：
                            test = models.CharField(
                                max_length=32,
                                error_messages={
                                    'c1': '优先错信息1',
                                    'c2': '优先错信息2',
                                    'c3': '优先错信息3',
                                },
                                validators=[
                                    RegexValidator(regex='root_\d+', message='错误了', code='c1'),
                                    RegexValidator(regex='root_112233\d+', message='又错误了', code='c2'),
                                    EmailValidator(message='又错误了', code='c3'), ]
                            )
 
```

#### **时间字段独有的**

DatetimeField、DateField、TimeField这个三个时间字段，都可以设置如下属性。

```
auto_now_add      配置auto_now_add=True，创建数据记录的时候会把当前时间添加到数据库。
auto_now          配置上auto_now=True，每次更新数据记录的时候会更新该字段。
```

#### **Model Meta 字段参数**

ORM对应的类里面包含另一个Meta类，而Meta类封装了一些数据库的信息。

```python
class UserInfo(models.Model):
    nid = models.AutoField(primary_key=True)
    username = models.CharField(max_length=32)
 
    class Meta:
        # 数据库中生成的表名称 默认 app名称 + 下划线 + 类名
        db_table = "table_name"
 
        # admin中显示的表名称
        verbose_name = '个人信息'
 
        # verbose_name加s
        verbose_name_plural = '所有用户信息'
 
        # 联合索引 
        index_together = [
            ("pub_date", "deadline"),   # 应为两个存在的字段
        ]
 
        # 联合唯一索引
        unique_together = (("driver", "restaurant"),)   # 应为两个存在的字段
```

补充：django admin 的简单使用

1. 创建超级用户：python manage.py createsuperuser
2. 按照提示设置用户名密码等信息
3. 在模块中的 admin.py 中注册一下要操作的表

```python
from django.contrib import admin
from app01 import models
# Register your models here.

admin.site.register(models.User)
```

4. 启动项目，访问 http://127.0.0.1:8000/admin，输入用户名密码即可进入django自带的后台管理系统了，可以快速的对当前项目的数据库进行操作。