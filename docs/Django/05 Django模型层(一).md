# ORM之单表操作

## 一、ORM简介

### **1.1 简介**

​		我们在使用 Django 框架开发web应用的过程中，不可避免地会涉及到数据的管理操作（增、删、改、查），而一旦谈到数据的管理操作，就需要用到数据库管理软件，例如 mysql、oracle 等。如果应用程序需要操作数据（比如将用户注册信息永久存放起来），那么我们需要在应用程序中编写原生 sql 语句，然后使用 pymysql 模块远程操作 mysql 数据库，但是直接编写原生 sql 语句会存在两方面的问题，严重影响开发效率：

- sql 语句的执行效率：应用开发程序员需要耗费一大部分精力去优化 sql 语句；
- 数据库迁移：针对 mysql 开发的sql语句无法直接应用到oracle数据库上，一旦需要迁移数据库，便需要考虑跨平台问题

为了解决上述问题，django引入了ORM的概念，ORM全称Object Relational Mapping，即对象关系映射，是在pymysq之上又进行了一层封装，对于数据的操作，我们无需再去编写原生sql，取代代之的是基于面向对象的思想去编写类、对象、调用相应的方法等，ORM会将其转换/映射成原生SQL然后交给pymysql执行。

原生SQL和ORM的对应关系：

​                                 <img src="/static/img/Django专题/ORM与DB的映射关系.png" style="zoom:67%;" /> 

### **1.2 ORM和SQL的比较**

```
 ===================  sql中的表  =========================                                                      

 # 创建表:
     CREATE TABLE employee(                                     
                id INT PRIMARY KEY auto_increment ,                    
                name VARCHAR (20),                                      
                gender BIT default 1,                                  
                birthday DATA ,                                         
                department VARCHAR (20),                                
                salary DECIMAL (8,2) unsigned,                          
              );


  =================  sql中的表记录  ======================                                                   

  # 添加一条表记录:                                                          
      INSERT employee (name,gender,birthday,salary,department)            
             VALUES   ("cdc",1,"1999-12-12",8000,"IT部");               

  # 查询一条表记录:                                                           
      SELECT * FROM employee WHERE age=24;                               

  # 更新一条表记录:                                                           
      UPDATE employee SET birthday="1989-10-24" WHERE id=1;              

  # 删除一条表记录:                                                          
      DELETE FROM employee WHERE name="alex"                             





=====================  python的类  ========================
class Employee(models.Model):
     id=models.AutoField(primary_key=True)
     name=models.CharField(max_length=32)
     gender=models.BooleanField()
     birthday=models.DateField()
     department=models.CharField(max_length=32)
     salary=models.DecimalField(max_digits=8,decimal_places=2)


 =================  python的类对象  =========================
      # 添加一条表记录:
          emp=Employee(name="cdc",gender=True,birthday="1999-12-12",epartment="IT部")
          emp.save()
      # 查询一条表记录:
          Employee.objects.filter(age=24)
      # 更新一条表记录:
          Employee.objects.filter(id=1).update(birthday="1989-10-24")
      # 删除一条表记录:
          Employee.objects.filter(name="alex").delete()
```

### **1.3 ORM的使用**

#### **1.3.1 数据库信息设置**

在 Django 中默认使用的是文件类型数据库 sqlite，如果想要使用 mysql 或者其他类别的数据库，需要在 settings.py 中进行相关的配置

```python
# settings.py

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': "数据库名字",
        "USER": "登录的用户名",
        "PASSWORD": "密码",
        "HOST": "mysql数据库的连接IP",
        "PORT": 3306

    }
}
```

#### **1.3.2 更换 mysql 操作模块**

Django中默认对mysql的操作使用的是 MySQLdb 模块，而对于 python3 而言，是使用 pymysql 模块来对数据库进行操作的，因此需要在项目名下的 \_\_init\_\_.py 中进行操作模块的替换

```python
# ORM1/__init__.py

import pymysql
pymysql.install_as_MySQLdb()
```

#### **1.3.3 模块注册检查**

检查新创建的 app 是否已经注册，否则在 app 中的 ORM 操作将不会生效

#### **1.3.4 数据库迁移**

​		需要注意的是，ORM 只能操作数据表和数据记录，对于数据库是无法操作的，因此我们需要手动建立一个数据库。此外，只要涉及到数据表的操作（新建、删除表）或者对数据表的结构进行了修改，都需要执行数据库迁移的操作，对于数据的增删改查则不需要。数据库迁移命令如下：

```
python manage.py makemigrations   # 这一步是将对表的操作记录一下，执行后会在对应模块下的migrations文件夹中依次多出一条记录
python manage.py migrate	# 这一步是执行数据表的相关操作，即把我们写的类翻译成sql语句去执行
```

#### **1.3.5 其他操作**

- 如果报错如下：

```python
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.3 or newer is required; you have 0.7.11.None
```

MySQLclient 目前只支持到 python3.4，因此如果使用的更高版本的python，需要修改如下：

```python
# 通过查找路径C:\Programs\Python\Python36-32\Lib\site-packages\Django-2.0-py3.6.egg\django\db\backends\mysql 这个路径里的文件，把下行注释掉 就OK了。

if version < (1, 3, 3):   raise` `ImproperlyConfigured("mysqlclient 1.3.3 or newer is required; you have %s"` `%` `Database.__version__)
```

- 如果想打印 orm 转换过程中的 sql，需要在 settings.py 中进行如下配置：

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
        },
    },
    'loggers': {
        'django.db.backends': {
            'handlers': ['console'],
            'propagate': True,
            'level':'DEBUG',
        },
    }
}　　
```

## 二、单表操作

### **2.1 创建表**

所有对表的创建，都要写在对应功能模块中的 models.py 中，对表中记录的增删改查可以写在对应模块的视图中。

```python
# app01/models.py

from django.db import models


class Book(models.Model):
    id = models.AutoField(primary_key=True)
    title = models.CharField(max_length=32)
    state = models.BooleanField()
    pub_date = models.DateField()
    price = models.DecimalField(max_digits=4, decimal_places=2)  # 浮点数类型（高精度）
    publish = models.CharField(max_length=32)
```

执行数据库迁移操作后，去数据库中查看，多了一张表 app01_book，这就是我们刚刚建立的表，django会将表名统一转换成小写后在最前面加一个模块名的前缀。

- 更多数据类型

```
<1> CharField
        字符串字段, 用于较短的字符串.
        CharField 要求必须有一个参数 maxlength, 用于从数据库层和Django校验层限制该字段所允许的最大字符数.
 
<2> IntegerField
       #用于保存一个整数.
 
<3> FloatField
        一个浮点数. 必须 提供两个参数:
         
        参数    描述
        max_digits    总位数(不包括小数点和符号)
        decimal_places    小数位数
                举例来说, 要保存最大值为 999 (小数点后保存2位),你要这样定义字段:
                 
                models.FloatField(..., max_digits=5, decimal_places=2)
                要保存最大值一百万(小数点后保存10位)的话,你要这样定义:
                 
                models.FloatField(..., max_digits=19, decimal_places=10)
                admin 用一个文本框(<input type="text">)表示该字段保存的数据.
 
<4> AutoField
        一个 IntegerField, 添加记录时它会自动增长. 你通常不需要直接使用这个字段;
        自定义一个主键：my_id=models.AutoField(primary_key=True)
        如果你不指定主键的话,系统会自动添加一个主键字段到你的 model.
 
<5> BooleanField
        A true/false field. admin 用 checkbox 来表示此类字段.
 
<6> TextField
        一个容量很大的文本字段.
        admin 用一个 <textarea> (文本区域)表示该字段数据.(一个多行编辑框).
 
<7> EmailField
        一个带有检查Email合法性的 CharField,不接受 maxlength 参数.
 
<8> DateField
        一个日期字段. 共有下列额外的可选参数:
        Argument    描述
        auto_now    当对象被保存时,自动将该字段的值设置为当前时间.通常用于表示 "last-modified" 时间戳.
        auto_now_add    当对象首次被创建时,自动将该字段的值设置为当前时间.通常用于表示对象创建时间.
        （仅仅在admin中有意义...)
 
<9> DateTimeField
         一个日期时间字段. 类似 DateField 支持同样的附加选项.
 
<10> ImageField
        类似 FileField, 不过要校验上传对象是否是一个合法图片.#它有两个可选参数:height_field和width_field,
        如果提供这两个参数,则图片将按提供的高度和宽度规格保存.    
<11> FileField
     一个文件上传字段.
     要求一个必须有的参数: upload_to, 一个用于保存上载文件的本地文件系统路径. 这个路径必须包含 strftime #formatting,
     该格式将被上载文件的 date/time
     替换(so that uploaded files don't fill up the given directory).
     admin 用一个<input type="file">部件表示该字段保存的数据(一个文件上传部件) .
 
     注意：在一个 model 中使用 FileField 或 ImageField 需要以下步骤:
            （1）在你的 settings 文件中, 定义一个完整路径给 MEDIA_ROOT 以便让 Django在此处保存上传文件.
            (出于性能考虑,这些文件并不保存到数据库.) 定义MEDIA_URL 作为该目录的公共 URL. 要确保该目录对
             WEB服务器用户帐号是可写的.
            （2） 在你的 model 中添加 FileField 或 ImageField, 并确保定义了 upload_to 选项,以告诉 Django
             使用 MEDIA_ROOT 的哪个子目录保存上传文件.你的数据库中要保存的只是文件的路径(相对于 MEDIA_ROOT).
             出于习惯你一定很想使用 Django 提供的 get_<#fieldname>_url 函数.举例来说,如果你的 ImageField
             叫作 mug_shot, 你就可以在模板中以 {{ object.#get_mug_shot_url }} 这样的方式得到图像的绝对路径.
 
<12> URLField
      用于保存 URL. 若 verify_exists 参数为 True (默认), 给定的 URL 会预先检查是否存在( 即URL是否被有效装入且
      没有返回404响应).
      admin 用一个 <input type="text"> 文本框表示该字段保存的数据(一个单行编辑框)
 
<13> NullBooleanField
       类似 BooleanField, 不过允许 NULL 作为其中一个选项. 推荐使用这个字段而不要用 BooleanField 加 null=True 选项
       admin 用一个选择框 <select> (三个可选择的值: "Unknown", "Yes" 和 "No" ) 来表示这种字段数据.
 
<14> SlugField
       "Slug" 是一个报纸术语. slug 是某个东西的小小标记(短签), 只包含字母,数字,下划线和连字符.#它们通常用于URLs
       若你使用 Django 开发版本,你可以指定 maxlength. 若 maxlength 未指定, Django 会使用默认长度: 50.  #在
       以前的 Django 版本,没有任何办法改变50 这个长度.
       这暗示了 db_index=True.
       它接受一个额外的参数: prepopulate_from, which is a list of fields from which to auto-#populate
       the slug, via JavaScript,in the object's admin form: models.SlugField
       (prepopulate_from=("pre_name", "name"))prepopulate_from 不接受 DateTimeFields.
 
<13> XMLField
        一个校验值是否为合法XML的 TextField,必须提供参数: schema_path, 它是一个用来校验文本的 RelaxNG schema #的文件系统路径.
 
<14> FilePathField
        可选项目为某个特定目录下的文件名. 支持三个特殊的参数, 其中第一个是必须提供的.
        参数    描述
        path    必需参数. 一个目录的绝对文件系统路径. FilePathField 据此得到可选项目.
        Example: "/home/images".
        match    可选参数. 一个正则表达式, 作为一个字符串, FilePathField 将使用它过滤文件名. 
        注意这个正则表达式只会应用到 base filename 而不是
        路径全名. Example: "foo.*\.txt^", 将匹配文件 foo23.txt 却不匹配 bar.txt 或 foo23.gif.
        recursive可选参数.要么 True 要么 False. 默认值是 False. 是否包括 path 下面的全部子目录.
        这三个参数可以同时使用.
        match 仅应用于 base filename, 而不是路径全名. 那么,这个例子:
        FilePathField(path="/home/images", match="foo.*", recursive=True)
        ...会匹配 /home/images/foo.gif 而不匹配 /home/images/foo/bar.gif
 
<15> IPAddressField
        一个字符串形式的 IP 地址, (i.e. "24.124.1.30").
<16> CommaSeparatedIntegerField
        用于存放逗号分隔的整数值. 类似 CharField, 必须要有maxlength参数.
```

- 更多参数

```
(1)null
 
如果为True，Django 将用NULL 来在数据库中存储空值。 默认值是 False.
 
(1)blank
 
如果为True，该字段允许不填。默认为False。
要注意，这与 null 不同。null纯粹是数据库范畴的，而 blank 是数据验证范畴的。
如果一个字段的blank=True，表单的验证将允许该字段是空值。如果字段的blank=False，该字段就是必填的。
 
(2)default
 
字段的默认值。可以是一个值或者可调用对象。如果可调用 ，每有新对象被创建它都会被调用。
 
(3)primary_key
 
如果为True，那么这个字段就是模型的主键。如果你没有指定任何一个字段的primary_key=True，
Django 就会自动添加一个IntegerField字段做为主键，所以除非你想覆盖默认的主键行为，
否则没必要设置任何一个字段的primary_key=True。
 
(4)unique
 
如果该值设置为 True, 这个数据字段的值在整张表中必须是唯一的
 
(5)choices
由二元组组成的一个可迭代对象（例如，列表或元组），用来给字段提供选择项。 如果设置了choices ，默认的表单将是一个选择框而不是标准的文本框，<br>而且这个选择框的选项就是choices 中的选项。
```

### **2.2 插入记录**

- 方式一：save 方式，此时book_object是Book的一个实例化对象

```python
book_object = Book(title="Python", price=75, state=True, pub_date="2012-12-12", publish="人民出版社")
print(book_object)
book_object.save()
```

- 方式二：create 方式，此时book_object就是要插入的模型对象，我们可以获取该对象的相关属性

```python
book_object = Book.objects.create(title="Java", price=75, state=True, pub_date="2012-12-12", publish="人民出版社")
print(book_object)
print(book_object.title)
print(book_object.price)
print(book_object.pub_date)
print(book_object.publish)
```

### **2.3 查询记录**

#### **2.3.1 常用查询 API**

- all		查询所有结果

```python
"""
调用者：管理器，即示例中的Book类
返回值：QuerySet类型数据，类似一个列表，元素为一个个模型对象，可以进行类似列表的操作（遍历、索引取值等）
"""

book_list = Book.objects.all()
print(book_list)  # <QuerySet [<Book: Book object>, <Book: Book object>]>

for book in book_list:
    print(book)  # Book object
    print(book.title)
```

- filter	包含了与所给筛选条件相匹配的对象

```python
"""
调用者：管理器，即示例中的Book类
返回值：QuerySet类型数据
可以根据多个条件过滤，条件之间用逗号隔开
"""

book_list = Book.objects.filter(title="Python")
book_list = Book.objects.filter(title="Python", price=85.00)
```

- get	返回与所给筛选条件相匹配的对象，返回结果有且只有一个；如果符合筛选条件的对象超过一个或者没有都会抛出错误

```python
"""
调用者：管理器，即示例中的Book类
返回值：模型对象
可以根据多个条件过滤，条件之间用逗号隔开
"""

book = Book.objects.get(title="Python")
```

- exclude	包含了与所给筛选条件不匹配的对象，即找到的是不符合删选条件的数据

```python
"""
调用者：管理器，即示例中的Book类
返回值：QuerySet类型数据
可以根据多个条件过滤，条件之间用逗号隔开
"""

book = Book.objects.exclude(title="Python")
```

- order_by	对查询结果排序

```python
"""
调用者：QuerySet
返回值：QuerySet
"""

book_list = Book.objects.all().order_by("pirce")  # 根据查询结果的price字段排序，默认是从小到大排序
book_list = Book.objects.all().order_by("-pirce")  # 根据查询结果的price字段排序，从大到小排序
book_list = Book.objects.all().order_by("pirce", "id")  # 先根据价格排序，如果价格相同再根据id排序
```

- reverse	对查询结果进行反向排序

```python
"""
调用者：QuerySet
返回值：QuerySet
"""

book_list = Book.objects.all().reverse()
```

- count	返回数据库中匹配查询QuerySet的对象数量

```python
"""
调用者：QuerySet
返回值：数字
"""

book_num = Book.objects.all().count()
```

- first	返回第一条记录

```python
"""
调用者：QuerySet
返回值：模型对象
"""
book = Book.objects.all().first()
```

- last	返回最后一条记录

```python
"""
调用者：QuerySet
返回值：模型对象
"""
book = Book.objects.all().last()
```

- values	查询某个字段的所有的值

```python
"""
调用者：QuerySet
返回值: 特殊的QuerySet，是一个可迭代的字典序列
"""
books = Book.objects.all().values("title")
```

- values_list

```python
"""
调用者：QuerySet
返回值: 特殊的QuerySet，回的是一个元组序列
"""
books = Book.objects.all().values_list("title")
```

- distinct	去重

```python
"""
调用者：QuerySet
返回值: QuerySet
需要注意的是，对于包含主键的记录进行去重是没有意义的，因为主键永远不可能一样；所以distinct往往和values联合使用
"""
res = Book.objects.filter(author="cdc").values("price").distinct()
```

#### **2.3.2 基于双下划线的模糊查询**

```python
# 价格在 100，200，300三者之一的书籍
Book.objects.filter(price__in=[100,200,300])

# 价格大于100的书籍
Book.objects.filter(price__gt=100)

# 价格小于100的书籍
Book.objects.filter(price__lt=100)

# 价格在100到200之间的书籍
Book.objects.filter(price__range=[100,200])

# 书名字包含 python 的书籍
Book.objects.filter(title__contains="python")

# 书名忽略大小写后包含 python 的书籍
Book.objects.filter(title__icontains="python")

# 书名是以py开头的书籍
Book.objects.filter(title__startswith="py")

# 发布时间是2012年的书籍（注意此方法只对DateField类型字段有作用）
Book.objects.filter(pub_date__year=2012)
```

### **2.4 删除记录**

删除方法就是 delete()。它运行时立即删除对象而不返回任何值。

```python
"""
调用者：QuerySet或者model对象
"""
Book.objects.filter(author="cdc").delete()  # 删除作者是cdc的书籍
Book.objects.filter(author="cdc").first().delete()  # 删除作者是cdc的第一本书籍
```

要注意的是： delete() 方法是 QuerySet 上的方法，但并不适用于 Manager 本身。这是一种保护机制，是为了避免意外地调用 Book.objects.delete() 方法导致 所有的 记录被误删除。如果你确认要删除所有的对象，那么你必须显式地调用：

```python
Book.objects.all().delete()  # 删除所有书籍
```

在 Django 删除对象时，会模仿 SQL 约束 ON DELETE CASCADE 的行为，换句话说，删除一个对象时也会删除与它相关联的外键对象。如果不想级联删除，可以设置为:

```python
pub = models.ForeignKey(to='Publisher', on_delete=models.SET_NULL, blank=True, null=True)
```

### **2.5 修改记录**

```python
"""
调用者：QuerySet
返回值：一个整型的数值，表示受影响的行数
"""
Book.objects.filter(title__startswith="py").update(price=120)  # 将所有书名是以py开头的书籍的价格修改成120
```

