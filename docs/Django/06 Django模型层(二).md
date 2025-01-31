# ORM之多表操作

## 一、创建模型

### **1.1 需求分析**

为了方便演示，我们先来构造一下需求场景。现在有这么四张表，分别是：

- Book表	用于记录书籍的书名，价格，作者，出版社等信息
- Author表    用于记录作者的简要信息以及编写的书籍的相关信息
- AuthorDetail表   用于记录作者的详细信息
- Publish表    用于记录出版社的信息和出版书籍的相关信息

我们再为上述的四张表添加以下的对应关系：

- 一对一    每一条作者的简要信息都会有且仅有一条详细信息对应，即 Author表和AuthorDetail表是一对一的关系
- 一对多    一个出版社可以出版多本书籍，但是一本书籍不能由多个出版社联合出版，即Book表和Publish表是一对多的关系
- 多对多    一本书可以由多个作者联合编写，一个作者也可以编写多本书，即Book表和Author表是多对多的关系

在 SQL 中，对于一对一的关系，只需要通过一个关联字段即可将两张表进行关联；对于一对多的关系，我们需要通过外键来将两张表进行关联；对于多对多的关系，我们需要通过第三张表来作为中间表进行关联，而在 ORM 中，我们也可以通过上述方式来对多表做关联操作，但是 ORM 还为我们提供了更简单的方式，如下

```python
from django.db import models


# 书籍表
class Book(models.Model):
    nid = models.AutoField(primary_key=True)
    title = models.CharField(max_length=32)
    price = models.DecimalField(max_digits=5, decimal_places=2)

    # Book表和Publish建立一对多关系
    publish = models.ForeignKey(to="Publish", to_field="nid", on_delete=models.CASCADE)

    # Book表和Author建立多对多关系
    author = models.ManyToManyField(to="Author")


# 作者表
class Author(models.Model):
    nid = models.AutoField(primary_key=True)
    name = models.CharField(max_length=32)

    # Author表和AuthorDetail表建立一对一的表关系
    author_detail = models.OneToOneField(to="AuthorDetail", to_field="nid", on_delete=models.CASCADE)


# 作者详细信息表
class AuthorDetail(models.Model):
    nid = models.AutoField(primary_key=True)
    age = models.IntegerField()
    gender = models.CharField(max_length=10)
    phone = models.BigIntegerField()


# 出版社表
class Publish(models.Model):
    nid = models.AutoField(primary_key=True)
    name = models.CharField(max_length=32)
    address = models.CharField(max_length=32)
```

执行数据库迁移操作

```
python manage.py makemigrations
python manage.py migrate
```

此时我们可以看到在数据库中多出来五张表，且表中对应的字段分别如下：

<img src="/static/img/Django专题/app02_tables.png" style="zoom:80%;" /> 

- book表

<img src="/static/img/Django专题/app02_book.png" style="zoom: 80%;" /> 

- author_detail表

<img src="/static/img/Django专题/app02_authordetail.png" style="zoom:80%;" /> 

- author表

<img src="/static/img/Django专题/app02_author.png" style="zoom:80%;" /> 

- publish表

<img src="/static/img/Django专题/app02_publish.png" style="zoom:80%;" /> 

- book_author表

<img src="/static/img/Django专题/app02_book_author.png" style="zoom:80%;" /> 

​		对于 publish 表和 author_detail 表没有什么特别的地方，和我们在模型中定义的字段是一样的。对于author表，我们并没有看到在 Author模型中定义的一对一的关联字段 author_detail，反而多了一个author_detail_id 的属性，这是因为对于一对一的表关系，django 的 ORM 机制会把我们定义的关联字段自动加上 "_id" 后作为一个属性，存储在表中，该属性指向的就是关联表的 nid 属性。

​		同样的，对于一对多的外键关系，django 的 ORM 机制也会把我们定义的外键字段加上 "_id" 后作为一个属性，存储在表中，该属性指向的也是关联表的 nid 属性，即 book 表中的 publish_id 属性字段就是这么由来的。

​		最后，对于 book 和 author 这种多对多的关系，ORM 自动生成了第三张表来对两者的关系进行管理，表名就是这两张表的名字，中间加上下划线。表中的属性字段 book_id 和 author_id 分别对应book 表和 author 表中的 nid 字段。以上表关系的具体对应关系如下：

<img src="/static/img/Django专题/表对应关系.png" style="zoom: 67%;" /> 

补充说明：

​		对于一对一的表关系，关联字段无论写在哪个模型中都行，因为本质上一对一的表是完全可以合并成一张表的；而对于一对多的表关系，关联字段必须定义在多的那个模型中，如 Book 和 Publish 中，一个 publish 可以对应多个 book，多以外键字段写在了 Book 类中；最后对于多对多的关系而言，关联字段无论写在哪个模型中都可以。

### **1.2 正向查询与反向查询**

​		正向查询是指，从含有关联属性的表去跨表查询对应的表的过程；反向查询是指从其他表跨表查询含有关联属性的表的查询过程。举个例子，就 book 表 和 publish 表，在 book 表中定义了外键关联字段 publish，此时从 book 表出发去 publish 表中查询对应数据的过程就是正向查询，从  publish 表出发去 book 表中查询对应的数据的过程即为反向查询。

​		这里简单介绍正向查询和反向查询的概念，是因为在 ORM 中，正向跨表查询和反向跨表查询的方式是有区别的，我们会在下面介绍查询时具体研究。

## 二、添加记录

​		对于一对一的表关系来说，插入数据和单表的插入方式没有任何区别，只需要注意关联字段的值必须能在关联的表中找到对应的匹配值即可。因此我们可以简单的先来为 author，author_detail 和 publish 表插入一些数据，我们重点研究的是一对多和多对多的表关系的数据插入方式。

- author 表

![](/static/img/Django专题/author表内容.png) 

- author_detail 表

![](/static/img/Django专题/author_detail表内容.png) 

- publish 表

![](/static/img/Django专题/publish表内容.png) 

### **2.1 一对多插入**

#### **2.1.1 通过模型对象插入**

```python
# 新增一本书籍，出版社是北京出版社
publish_obj = Publish.objects.get(name="北京出版社")
Book.objects.create(title="跟我学Python", price=100, publish=publish_obj)
```

​		该方式是通过模型中定义的关联字段来实现数据插入的。首先查询到对应的出版社对象，再将出版社对象赋值给Book模型中定义的外键字段，此时 ORM 引擎会自动去 book 表关联的 publish 表中匹配这个publish_obj 对象，找到这条数据后将该数据对象的 nid 的值赋给 book 表中的 publish_id 属性字段。

#### **2.1.2 直接给 book 表绑定 publish_id**

```python
# 新增一本书籍，出版社是北京出版社
Book.objects.create(title="跟我一起学GoLang", price=200, publish_id=1)
```

​		该方式等价于直接对 book 表进行操作，给表中对应的每个字段赋值。

### **2.2 多对多插入**

```python
# 插入一本书籍，book_obj为插入的对象
book_obj = Book.objects.create(title="跟我一起学Linux", price=150, publish_id=2)

# 查询作者对象
cdc = Author.objects.get(name="cdc")
trr = Author.objects.get(name="trr")

# 通过关联字段为book绑定多对多的关系
book_obj.author.add(cdc, trr)
```

也可以通过指定主键的方式进行直接绑定

```python
book_obj = Book.objects.get(title="跟我学Python")
book_obj.author.add(1, 2, 3)   # 将关联表author中nid分别为 1、2、3的数据进行关联
# book_obj.author.add(*[1, 2, 3])  #  和上述方式一样
```

补充：

```python
book_obj.authors.remove()      # 将某个特定的对象从被关联对象集合中去除，如 book_obj.authors.remove(cdc)  或者 book_obj.authors.remove(1)
book_obj.authors.clear()       # 清空被关联对象集合，等价于 book_obj.authors.remove(所有作者)
book_obj.authors.set()         # 先清空再设置　　
```

```python
book_obj.authors.all()  # 获取与这本书关联的所有作者对象集合，是一个QuerySet类型
```

## 三、跨表查询

### **3.1 基于对象的跨表查询（子查询）**

#### **3.1.1 一对一查询**

- 正向查询：根据关联字段

```python
# 查询cdc的手机号
cdc = Author.objects.filter(name="cdc").first()
phone = cdc.author_detail.phone  # 根据关联字段查询
print(phone)
```

- 反向查询：根据表名小写

```python
# 查询手机号是13169857456的作者名字
per = AuthorDetail.objects.filter(phone=13169857456).first()
print(per.author.name)  # 根据表名小写反向查询
```

#### **3.1.2 一对多查询**

- 正向查询：根据关联字段

```python
# 查询《跟我学Python》这本书的出版社名字
book = Book.objects.filter(title="跟我学Python").first()
print(book.publish)  # 跟这本书有关的出版社对象
print(book.publish.name)  # 出版社的名字
```

- 反向查询：`表名小写_set.all()`

```python
# 查询北京出版社出版的所有书籍
pub = Publish.objects.filter(name="北京出版社").first()
books = pub.book_set.all()

for book in books:
    print(book.title)
```

#### **3.1.3 多对多查询**

- 正向查询：根据关联字段

```python
# 查询《跟我学Python》所有作者的名字
book = Book.objects.filter(title="跟我学Python").first()
author_list = book.author.all()
print(author_list)
for author in author_list:
	print(author.name)
```

- 反向查询：根据`表名小写_set.all()`

```python
# 查询cdc写过的所有书籍
cdc = Author.objects.filter(name="cdc").first()
book_list = cdc.book_set.all()
for book in book_list:
	print(book.title)
```

### **3.2 基于双下划线的跨表查询（join查询）**

基于双下划线的跨表查询，其本质是连表查询。正向查询主要通过在表中定义的关联字段来通知 ORM 引擎去 join 对应的关联表，然后进行连表查询；反向查询主要通过小写的表名来告诉 ORM 引擎，让当前表去 join 对应的表，然后进行连表查询。

#### **3.2.1 一对一查询**

- 正向查询

```python
# 查询cdc的手机号
phone = Author.objects.filter(name="cdc").values("author_detail__phone")
print(phone)  # <QuerySet [{'author_detail__phone': 13169857456}]>
```

- 反向查询

```python
# 查询cdc的手机号
phone = AuthorDetail.objects.filter(author__name="cdc").values("phone")
print(phone)  # <QuerySet [{'phone': 13169857456}]>
```

#### **3.2.2 一对多查询**

- 正常查询

```python
# 查询跟我学Python这本书的出版社
pub_name = Book.objects.filter(title="跟我学Python").values("publish__name")
print(pub_name)  # <QuerySet [{'publish__name': '北京出版社'}]>
```

- 反向查询

```python
# 查询跟我学Python这本书的出版社
pub_name = Publish.objects.filter(book__title="跟我学Python").values("name")
print(pub_name)  # <QuerySet [{'name': '北京出版社'}]>
```

#### **3.2.3 多对多查询**

- 正向查询

```python
# 查询跟我学Python这本书的所有作者
authors = Book.objects.filter(title="跟我学Python").values("author__name")
print(authors)  # <QuerySet [{'author__name': 'trr'}, {'author__name': 'cdc'}, {'author__name': 'ctt'}]>
```

- 反向查询

```python
# 查询跟我学Python这本书的所有作者
authors = Author.objects.filter(book__title="跟我学Python").values("name")
print(authors)  # <QuerySet [{'name': 'trr'}, {'name': 'cdc'}, {'name': 'ctt'}]>
```

练习：连续跨表查询

```python
# 查询年龄大于25岁的作者以及写过的书和书籍的出版社
res = Book.objects.filter(author__author_detail__age__gt=25).values("title", "publish__name", "author__name")
print(res)  # <QuerySet [{'title': '跟我学Python', 'publish__name': '北京出版社', 'author__name': 'ctt'}]>
```

## 四、聚合和分组查询

### **4.1 聚合查询**

​		ORM 中的聚合查询是通过  aggregate() 方法实现的，aggregate() 是 QuerySet 的一个终止子句，意思是说，它返回一个包含一些键值对的字典，不再是queryset。键的名称是聚合值的标识符，值是计算出来的聚合值。键的名称是按照字段和聚合函数的名称自动生成出来的。如果你想要为聚合值指定一个名称，可以向聚合子句提供它。

```python
from django.db.models import Avg, Max, Min, Count

# 查询所有书籍的平均价格
avg_price = Book.objects.all().aggregate(Avg("price"))  # {'price__avg': 150.0} 不为值指定名称的话，默认名称是字段加双下划线加计算方法
avg_price = Book.objects.all().aggregate(average_price=Avg("price"))  # 指定名称 {'average_price': 150.0}

# 查询最贵的书的价格
max_price = Book.objects.all().aggregate(max_price=Max("price"))
print(max_price)

# 查询最便宜的书的价格
min_price = Book.objects.all().aggregate(min_price=Min("price"))
print(min_price)

# 查有多少本书
book_num = Book.objects.all().aggregate(book_num=Count("title"))
print(book_num)
```

如果你希望生成不止一个聚合，你可以向`aggregate()`子句中添加另一个参数

```python
 res = Book.objects.all().aggregate(max_price=Max("price"), min_price=Min("price"), average_price=Avg("price"))
 print(res)  # {'max_price': Decimal('200.00'), 'min_price': Decimal('100.00'), 'average_price': 150.0}
```

### **4.2 分组查询**

ORM 中的聚合查询是通过  annotate() 方法实现的，annotate()为调用的`QuerySet`中每一个对象都生成一个独立的统计值（统计方法用聚合函数），返回值依然是一个queryset

#### **4.2.1 单表分组**

```
#----------------- 员工表 --------------------
id  name age  salary    dep
1   cdc  12    2000    销售部
2   ctt  22    3000    人事部
3   trr   22   5000    人事部
```

我们要查询每个部门有多少人

```sql
# SQL
select dep,Count(*) from emp group by dep;
```

```python
# ORM
from django.db.models import Avg, Max, Min, Count

res = Emp.objects.all().values("dep").annotate(Count("name"))
print(res)  # <QuerySet [{'dep': '销售部', 'name__count': 1}, {'dep': '人事部', 'name__count': 2}]>
```

values 中存放要分组的条件（需求中是按部门分组），annotate 中存放计算结果，结果的键名默认值为计算的字段加双下划线加计算方式，当然我们也可以指定结果名称

```python
res = Emp.objects.all().values("dep").annotate(people_num=Count("name"))
print(res)  # <QuerySet [{'dep': '销售部', 'people_num': 1}, {'dep': '人事部', 'people_num': 2}]>
```

#### **4.2.2 多表分组**

```sql
# 查询每个出版社的名称以及对应的出版的书籍量
# SQL 先连表，后查询
select publish.name,Count(*) from book left join publish on book.nid=publish.id group by publish.id
```

```python
# ORM
from django.db.models import Avg, Max, Min, Count

# 查询每个出版社的名称以及对应的出版的书籍量
res = Publish.objects.all().values("name").annotate(book_num=Count("book__title"))
print(res)  # <QuerySet [{'name': '北京出版社', 'book_num': 2}, {'name': '南京出版社', 'book_num': 1}, {'name': '上海出版社', 'book_num': 0}]>
```

或者还可以这么写

```python
# 查询每个出版社的名称以及对应的出版的书籍量
Publish.objects.all().values("nid").annotate(book_num=Count("book__title")).values("name", "book_num")
```

```python
# 查询每一个书籍的名称以及对应的作者个数
Book.objects.all().values("nid").annotate(author_num=Count("author__name"))
```

总结：多表分组查询模型：

- 正向查询

```
表模型.objects.all().values("主键").annotate(聚合函数(关联字段__统计字段))
```

- 反向查询

```
后表模型.objects.all().values("主键").annotate(聚合函数(关联表名__统计字段))
```

**补充：**当我们不指定分组的依据时，ORM 引擎默认根据主键来分组；

我们还可以查询到分组模型对象的其他字段。

```python
# 查询每一个出版社的名称以及出版的书籍个数
ret = Publish.objects.values("nid").annotate(c=Count("book__title")).values("name", "address")  # 完整版分组查询
ret = Publish.objects.all().annotate(c=Count("book__title")).values("name","c","address")  # 此时会自动按照主键分组
ret = Publish.objects.annotate(c=Count("book__title")).values("name","c","address")  # 默认全部查询后再按主键分组
```

## 五、F查询和Q查询

### **5.1 F查询**

在上面所有的例子中，我们构造的过滤器都只是将字段值与某个常量做比较。如果我们要对两个字段的值做比较，那该怎么做呢？Django 提供 F() 来做这样的比较。F() 的实例可以在查询中引用字段，来比较同一个 model 实例中两个不同字段的值。

```python
# 查询评论数大于收藏数的书籍
from django.db.models import F

Book.objects.filter(commnet_no__lt=F('keep_no'))
```

Django 还支持 F() 对象之间以及 F() 对象和常数之间的加减乘除和取模的操作。

```python
# 查询评论数大于收藏数2倍的书籍

Book.objects.filter(commnet_no__lt=F('keep_no')*2)
```

修改操作也可以使用F函数，比如将每一本书的价格提高30元：

```python
Book.objects.all().update(price=F("price")+30)　
```

### **5.2 Q查询**

filter() 等方法中的关键字参数查询其实都是一起进行 AND 操作 的。如果你需要执行更复杂的查询（例如 OR ），你可以使用 Q 对象。Q对象可以使用& 和| 操作符组合起来。当一个操作符在两个Q 对象上使用时，它产生一个新的Q 对象。

```python
# 查询书名是以Py开头或者以Li开头的书籍
from django.db.models import Q

Book.objects.filter(Q(title__startswith='Py')|Q(title__startswith='Li'))
```

可以组合使用 & 和 | 操作符，以及使用括号进行分组来编写任意复杂的Q 对象。同时，Q 对象可以使用 ~ 操作符取反，这允许组合正常的查询和取反(NOT) 查询：

```python
# 查询作者是cdc且发行年限不是2017年的书籍的书名

bookList=Book.objects.filter(Q(author__name="cdc") & ~Q(publish_date__year=2017)).values_list("title")
```

查询函数可以混合使用Q 对象和关键字参数。所有提供给查询函数的参数（关键字参数或Q 对象）都将"AND" 在一起。但是，如果出现Q 对象，它必须位于所有关键字参数的前面。例如：

```python
# 查询在2016年或者2017年出版的，且书名忽略大小写为python的书籍

bookList=Book.objects.filter(Q(publishDate__year=2016) | Q(publishDate__year=2017), title__icontains="python")
```

