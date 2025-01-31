# forms组件

## 一、forms组件简介

​		forms 组件是 django 提供的一种可以快速校验前端发送的数据的格式以及渲染相关信息和标签的机制，使用 forms 组件可以大大提高开发中的效率。

## 二、forms组件的校验功能

我们模拟实现一个用户注册信息的例子，来研究一下使用forms组件如何去实现对数据的校验功能。

### **2.1 普通版**

```python
# urls.py

from django.conf.urls import url
from app01 import views

urlpatterns = [
    url(r'^reg/', views.reg),
]
```

```python
# views.py

from django.shortcuts import render, HttpResponse


def reg(request):

    if request.method == "POST":
        print(request.POST)
        """
        对接收到数据分别进行校验，比如：
        密码长度是不是大于4
        电话号码是否是有效的
        邮箱格式是否正确等
        """
        return HttpResponse("OK")

    return render(request, "reg.html")
```

```html
<!-- reg.html -->

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
    <p>密  码：<input type="text" name="pwd"></p>
    <p>确认密码：<input type="text" name="r_pwd"></p>
    <p>邮  箱：<input type="text" name="email"></p>
    <p>电话号码：<input type="text" name="tel"></p>
    <p><input type="submit" value="提交"></p>
</form>
</body>
</html>
```

我们在后台的视图中要自己编写对应的规则去匹配用户输入的数据是否合法，显然这是十分复杂和困难的，我们可以使用 django 提供的forms机制来实现对数据的校验。

### **2.2 使用forms简单校验数据**

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms


class UserForm(forms.Form):
    name = forms.CharField(min_length=4)  # 用户名长度至少是4
    pwd = forms.CharField(min_length=4)  # 密码的长度至少为4
    email = forms.EmailField()  # 邮箱必须符合邮箱规则


def reg(request):
    if request.method == "POST":
        print(request.POST)
        name = request.POST.get("name")
        pwd = request.POST.get("pwd")
        r_pwd = request.POST.get("r_pwd")
        email = request.POST.get("email")
        tel = request.POST.get("tel")

        form = UserForm({"name": name, "pwd": pwd, "email": email})
        # form = UserForm(request.POST)  # 如果想直接检验从前端获取的数据，那么form表单的name属性值应该与forms组件字段名称一致

        # 使用forms检验数据，返回值是一个布尔值，当传入的值都检验成功时值为True，其他情况均为False
        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)  # 存放校验成功的键值对
        else:
            print(form.cleaned_data)  # 存放校验成功的键值对
            print(form.errors)  # 存放校验失败的内容部分

        return HttpResponse("OK")

    return render(request, "reg.html")
```

​		首先要自定义一个检验的类，该类必须继承 forms.Form，在类中我们可以对相关的字段做一些条件的约束。当获取到前端的数据后，可以根据自定义的校验类来对数据进行校验。使用时需要注意以下几点：

#### **2.2.1 关于自定义校验类中的字段**

在传入校验数据的时候，必须要与类中的字段都能匹配上，只能多不能少，否则校验结果为 False

```python
# 在类中定义了三个字段，分别为 name,pwd,email
class UserForm(forms.Form):
    name = forms.CharField(min_length=4)  # 用户名长度至少是4
    pwd = forms.CharField(min_length=4)  # 密码的长度至少为4
    email = forms.EmailField()  # 邮箱必须符合邮箱规则
    
    
# 针对以下几种情况的校验结果
# 1.传入数据时，字段名填写错误，校验结果为false（比如类中定义的是name，传数据时候写的是names）
form = UserForm({"names": name, "pwd": pwd, "email": email})
form.is_valid()  # 此时校验结果为false

# 2.传入数据时，字段少于类中定义的字段，校验结果为false（比如类中定义了三个属性，传数据时候只传了两个）
form = UserForm({"name": name, "pwd": pwd})
form.is_valid()  # 此时校验结果为false

# 3.传入数据时，字段多于类中定义的字段，只要能全部匹配到类中定义的字段，多余的字段会自动忽略，校验结果有数据的正确与否决定（比如类中定义了三个属性，传数据时候传了五个）
form = UserForm({"name": name, "pwd": pwd, "r_pwd":pwd, "tel":tel, "email": email})
form.is_valid()  
```

#### **2.2.2 关于数据的校验**

is_valid() 方法就是自动校验的方法，结果是一个布尔值：

- 结果为False：校验时字段传入有问题，或者数据不符合规则
- 结果为True：校验时传入的字段没问题，且数据都符合规则

form.cleaned_data    存放校验通过的字段，是一个字典的数据格式，存放的值是校验通过的数据键值对

```python
# 比如用户名和密码是校验通过的，那么 form.cleaned_data 存放的值的格式为
{"name": “cdchello, "pwd": "123456"}
```

form.errors     存放校验失败的内容部分，是一个字典的数据格式，但是跟我们常见的字典类型不一样，字典的键是校验失败的字段，值是一个列表，存放的是错误信息。

```python
# 比如用户名长度为3，不符合类中定义的最小长度为4的规则，其余都是合法数据

        if form.is_valid():
            print(form.cleaned_data)  # 存放校验成功的键值对  
        else:
            print(form.cleaned_data)  # 存放校验成功的键值对  {'pwd': 'dasdasdasdasd', 'email': '1275500642@qq.com'}
            print(form.errors)  # 存放校验失败的内容部分   <ul class="errorlist"><li>name<ul class="errorlist"><li>Ensure this value has at least 4 characters (it has 3).</li></ul></li></ul>
            print(type(form.errors))  #  <class 'django.forms.utils.ErrorDict'>
            print(form.errors.get("name"))  # <ul class="errorlist"><li>Ensure this value has at least 4 characters (it has 3).</li></ul>
            print(type(form.errors.get("name")))  # <class 'django.forms.utils.ErrorList'>
            print(form.errors.get("name")[0])  # 获取错误信息  Ensure this value has at least 4 characters (it has 3).
```

使用 form.errors.get("字段值")[0] 从 form.errors 中获取错误信息。

## 三、forms组件渲染标签的功能

在上一节中我们提到，我们可以直接将request.POST中获取到的内容进行校验，但是这就要求前端的form表单的name属性值应该与forms组件字段名称一致，如果不一致校验就会失败。

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms

class UserForm(forms.Form):
    name = forms.CharField(min_length=4)  # 用户名长度至少是4
    pwd = forms.CharField(min_length=4)  # 密码的长度至少为4
    email = forms.EmailField()  # 邮箱必须符合邮箱规则


def reg(request):
    if request.method == "POST":
        print(request.POST) 
      
        form = UserForm(request.POST)  # 如果想直接检验从前端获取的数据，那么form表单的name属性值应该与forms组件字段名称一致

        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)  
        else:
            print(form.cleaned_data)  
            print(form.errors)  

        return HttpResponse("OK")

    return render(request, "reg.html")
```

但是每次要保证前后端的一致性是十分麻烦的，所以 forms 组件还给我们提供了简单渲染标签的功能，会自动在前端根据我们设置的字段名生成对应的input标签，主要有以下三种方式：

- 直接使用变量替换

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms


class UserForm(forms.Form):
    name = forms.CharField(min_length=4)  # 用户名长度至少是4
    pwd = forms.CharField(min_length=4)  # 密码的长度至少为4
    r_pwd = forms.CharField(min_length=4)  # 密码的长度至少为4
    email = forms.EmailField()  # 邮箱必须符合邮箱规则
    tel = forms.EmailField()  # 邮箱必须符合邮箱规则


def reg(request):
    if request.method == "POST":
        form = UserForm(request.POST)  # 如果想直接检验从前端获取的数据，那么form表单的name属性值应该与forms组件字段名称一致

        # 使用forms检验数据，返回值是一个布尔值，当传入的值都检验成功时值为True，其他情况均为False
        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)  # 存放校验成功的键值对
        else:
            print(form.cleaned_data)  # 存放校验成功的键值对  
            print(form.errors)  # 存放校验失败的内容部分   
           
        return HttpResponse("OK")

    form = UserForm()  # 在接收到 get 请求时，就实例化一个form对象，用于渲染标签
    return render(request, "reg.html", locals())
```

```html
<!-- reg.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h3>forms渲染标签1</h3>
<form action="" method="post">
    {% csrf_token %}
    <p>用户名：{{ form.name }}</p>
    <p>密  码：{{ form.pwd }}</p>
    <p>确认密码：{{ form.r_pwd }}</p>
    <p>邮  箱：{{ form.email }}</p>
    <p>电话号码：{{ form.tel }}</p>
    <p><input type="submit" value="提交"></p>
</form>
</body>
</html>
```

- 使用for循环

```html
<!-- reg.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h3>forms渲染标签2</h3>
<form action="" method="post">
    {% csrf_token %}
    {% for field in form %}
        <p>{{ field }}</p>
    {% endfor %}
    <p><input type="submit" value="提交"></p>
</form>
</body>
</html>
```

以上方式有一个问题，虽然输入狂生成了，但是没有前面的填写信息的提示，forms中在设置字段规则的时候还有一个label属性，我们可以通过设置各个字段 label 的值。如果不设置，默认值为字段名

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms

class UserForm(forms.Form):
    name = forms.CharField(min_length=4, label="用户名")  # 用户名长度至少是4
    pwd = forms.CharField(min_length=4, label="密码")  # 密码的长度至少为4
    r_pwd = forms.CharField(min_length=4, label="确认密码")  # 密码的长度至少为4
    email = forms.EmailField(label="邮箱")  # 邮箱必须符合邮箱规则
    tel = forms.EmailField(label="电话")  # 邮箱必须符合邮箱规则


def reg(request):
    if request.method == "POST":
        form = UserForm(request.POST) 

        # 使用forms检验数据，返回值是一个布尔值，当传入的值都检验成功时值为True，其他情况均为False
        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)  # 存放校验成功的键值对
        else:
            print(form.cleaned_data)  # 存放校验成功的键值对 
            print(form.errors)  # 存放校验失败的内容部分  

        return HttpResponse("OK")

    form = UserForm()  # 在接收到 get 请求时，就实例化一个form对象，用于渲染标签
    return render(request, "reg.html", locals())
```

```html
<!-- reg.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h3>forms渲染标签2</h3>
<form action="" method="post">
    {% csrf_token %}
    {% for field in form %}
        <p>
            <label for="">{{ field.label }}</label>
            {{ field }}
        </p>
    {% endfor %}
    <p><input type="submit" value="提交"></p>
</form>
</body>
</html>
```

- 通过 forms.as_p 属性，不推荐使用，一旦使用这种方式，前端自动生成的标签就固定住了，灵活性差

```html
<!-- reg.html -->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h3>forms渲染标签3</h3>
<form action="" method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <p><input type="submit" value="提交"></p>
</form>

</body>
</html>
```

## 四、forms组件渲染错误信息的功能

### **4.1 渲染错误信息**

用户填写相关信息时，如果填写错误，需要给用户提示错误的原因。要实现这个功能，我们首先要对视图函数进行修改。

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms

def reg(request):
    if request.method == "POST":
       
        form = UserForm(request.POST)  # 如果想直接检验从前端获取的数据，那么form表单的name属性值应该与forms组件字段名称一致

        # 使用forms检验数据，返回值是一个布尔值，当传入的值都检验成功时值为True，其他情况均为False
        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)  
        else:
            print(form.cleaned_data)  
            print(form.errors) 

        return render(request, "reg.html", locals())

    form = UserForm()  # 在接收到 get 请求时，就实例化一个form对象，用于渲染标签
    return render(request, "reg.html", locals())
```

​		上面的代码中，我们实例化出了两个 forms 对象，但是两者是完全不同的。对于 get 请求的使用的 forms 对象（未绑定数据的forms对象），它的作用主要就是用来渲染生成标签；对于 post 请求中的forms 对象，除了有渲染标签的功能，它还能绑定用户在输入框中输入的数据，确保刷新页面时，数据不丢失。

对于前端，我们只需要在标签后显示对应的错误信息即可，代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<h3>forms渲染标签1</h3>
<form action="" method="post">
    {% csrf_token %}
    <p>用户名：{{ form.name }} <span>{{ form.name.errors.0 }}</span></p>
    <p>密 码：{{ form.pwd }} <span>{{ form.pwd.errors.0 }}</span></p>
    <p>确认密码：{{ form.r_pwd }} <span>{{ form.r_pwd.errors.0 }}</span></p>
    <p>邮 箱：{{ form.email }} <span>{{ form.email.errors.0 }}</span></p>
    <p>电话号码：{{ form.tel }} <span>{{ form.tel.errors.0 }}</span></p>
    <p><input type="submit" value="提交"></p>
</form>
<hr>

</body>
</html>
```

### **4.2 相关参数的设置**

​		渲染完错误信息后，我们发现提示语都是django内置的内容，我们要如何显示我们自己定义的内容呢？并且前端使用forms组件方式来进行渲染标签，我们想要给标签添加对应的属性又要怎么操作呢？使用forms组件渲染的标签都是text类型的输入标签，怎样设置其他格式的标签呢（单选框等）？这些都可以在定义forms组件时进行设置。

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms
from django.forms import widgets

class UserForm(forms.Form):
    # error_message 参数用于设置错误提示信息
    #   固定键值 required 表示为空错误
    #   固定键值 invalid 表示格式错误

    # widget 参数用于设置标签的类型以及添加相关的属性等
    #   widget = widgets.PasswordInput()  规定标签的类型
    #   widget = widgets.PasswordInput(attrs={"class": "form-control", "id": "pwd"})  给标签添加对应的css属性
    
    name = forms.CharField(min_length=4, label="用户名",
                           error_messages={"required": "信息不能为空", "invalid": "数据格式错误"}, widget=widgets.TextInput())
    pwd = forms.CharField(min_length=4, label="密码",
                          widget=widgets.PasswordInput(attrs={"class": "form-control", "id": "pwd"}))  #
    r_pwd = forms.CharField(min_length=4, label="确认密码", widget=widgets.PasswordInput())  #
    email = forms.EmailField(label="邮箱", widget=widgets.EmailInput())
    tel = forms.CharField(label="电话", widget=widgets.TextInput)
```

## 五、forms组件的钩子功能

​		我们在自定义forms组件的类的时候，只能简单的进行一些规则的定制，如果想要实现更加负责的操作，可以使用forms组件提供的钩子机制来完成。由于钩子机制是forms组件内部封装好的，所以在使用前，有必要对forms的源码进行简单的了解，看一下forms的检验机制究竟是怎样实现的。

我们从 forms 的校验函数  form.is_valid() 进入查看：

```python
# forms.py

def is_valid(self):
    """
    Returns True if the form has no errors. Otherwise, False. If errors are
    being ignored, returns False.
    """
	return self.is_bound and not self.errors
```

该方法最后只返回了两个值，我们需要注意的是第二个返回值，这个 errors 就是存放校验失败内容的字典，我们再查看一下 errors 是怎么实现的

```python
# forms.py

 @property
    def errors(self):
        "Returns an ErrorDict for the data provided for the form"
        if self._errors is None:
            self.full_clean()
        return self._errors
```

在errors内部执行了一个 full_clean() 方法

```python
# forms.py

    def full_clean(self):
        """
        Cleans all of self.data and populates self._errors and
        self.cleaned_data.
        """
        self._errors = ErrorDict()  # 定义一个存放校验失败内容的字典
        if not self.is_bound:  # Stop further processing.
            return
        self.cleaned_data = {}  # # 定义一个存放校验成功内容的字典
        # If the form is permitted to be empty, and none of the form data has
        # changed from the initial data, short circuit any validation.
        if self.empty_permitted and not self.has_changed():
            return

        self._clean_fields()  # 局部钩子
        self._clean_form()  # 全局钩子
        self._post_clean()
```

我们需要重点关注的就是 full_clean() 方法中调用的 self._clean_fields() 和 self.\_clean_form()，这是实现钩子的核心。我们一个一个来看

### **5.1 局部钩子**

局部钩子函数用于实现对某一个字段进行更加复杂的校验操作

```python
# forms.py

    def _clean_fields(self):
        for name, field in self.fields.items():  # fileds是一个字典，存放的内容是 {"name":name字段的校验规则, "pwd":pwd的校验规则......}
            # value_from_datadict() gets the data from the data dictionaries.
            # Each widget type knows how to retrieve its own data, because some
            # widgets split data over several HTML fields.
            if field.disabled:
                value = self.get_initial_for_field(field, name)
            else:
                value = field.widget.value_from_datadict(self.data, self.files, self.add_prefix(name))
            try:
                if isinstance(field, FileField):
                    initial = self.get_initial_for_field(field, name)
                    value = field.clean(value, initial)
                else:
                    value = field.clean(value)  # 按定义的规则进行数据的校验
                self.cleaned_data[name] = value  # 数据校验通过，就把数据放到 cleaned_data 字典中，键为字段名，值为校验通过的数据
                if hasattr(self, 'clean_%s' % name):
                    value = getattr(self, 'clean_%s' % name)()
                    self.cleaned_data[name] = value
            except ValidationError as e:
                self.add_error(name, e)  # 校验失败，就把数据放到 errors 字典中，键为字段名，值为校验失败的报错信息
```

​		局部钩子的逻辑是这样的，先对数据按照我们在类中定义的规则进行校验，如果校验成功，会把数据存入 cleaned_data 字典中，紧接着，再通过反射去寻找有没有以 clean_ 开头的函数方法，如果有就再去执行，并把刚刚存数据的操作重新覆盖执行一遍；如果校验失败，会引发一个 ValidationError 异常，并把字段和异常存入 error 字典。

​		因此，我们想要对哪个字段使用钩子函数，只需要在自定义的类中去定义再去定义一个 clean_字段名 的函数即可，把复杂的操作在函数中完成。例如

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms
from django.forms import widgets
from app01.models import User
from django.core.exceptions import ValidationError


class UserForm(forms.Form):
    name = forms.CharField(min_length=4, label="用户名",
                           error_messages={"required": "信息不能为空", "invalid": "数据格式错误"}, widget=widgets.TextInput())
    pwd = forms.CharField(min_length=4, label="密码",
                          widget=widgets.PasswordInput(attrs={"class": "form-control", "id": "pwd"}))  #
    r_pwd = forms.CharField(min_length=4, label="确认密码", widget=widgets.PasswordInput())  #
    email = forms.EmailField(label="邮箱", widget=widgets.EmailInput())
    tel = forms.CharField(label="电话", widget=widgets.TextInput)

    # 校验用户名是不是在数据库中已经存在了
    def clean_name(self):
        # 会先执行自带的局部钩子，自带的钩子用于校验定义字段时的那些规则
        # 如果能取到值，表示已经通过字段中的定义规则了，就会接下去执行更复杂的校验
        val = self.cleaned_data.get("name")

        ret = User.objects.filter(name=val)

        if not ret:
            # 数据库中还没有该名字，可以注册，返回该结果
            # _clean_fields 函数会通过 value = getattr(self, 'clean_%s' % name)() 拿到该结果
            # 接着通过 self.cleaned_data[name] = value 把该值添加到 cleaned_data中去，如果已经存在该值，会进行覆盖
            raise val
        else:
            # 如果用户名已经存在，引发一个 ValidationError 异常
            # 字段名和自定义的异常信息会以键值对的形式存储到 errors 字典中去，如{"name":["用户名已存在"]}
            return ValidationError("用户名已存在")
```

### **5.2 全局钩子**

全局钩子函数用于实现对多个字段间的复杂校验操作

```python
# forms.py

 def _clean_form(self):
        try:
            cleaned_data = self.clean()
        except ValidationError as e:
            self.add_error(None, e)
        else:
            if cleaned_data is not None:
                self.cleaned_data = cleaned_data
```

对于全局钩子来说，最重要的就是执行了 clean 方法

```python
   def clean(self):
        """
        Hook for doing any extra form-wide cleaning after Field.clean() has been
        called on every field. Any ValidationError raised by this method will
        not be associated with a particular field; it will have a special-case
        association with the field named '__all__'.
        """
        return self.cleaned_data
```

​		clean 方法中只是给了简单的描述，并没有实际的逻辑代码，这就代表着 clean 方法只是开放了一个功能，具体的实现需要我们自己来完成。但是有一点需要注意的是，由于全局操作的是多个字段之间的关系校验，因此如果校验失败，想把错误放到 errors 字典中，键应该填什么呢？clean 方法规定了，在这里键可以填 \__all__

```python
# views.py

from django.shortcuts import render, HttpResponse
from django import forms
from django.forms import widgets
from app01.models import User
from django.core.exceptions import ValidationError


class UserForm(forms.Form):
    name = forms.CharField(min_length=4, label="用户名",
                           error_messages={"required": "信息不能为空", "invalid": "数据格式错误"}, widget=widgets.TextInput())
    pwd = forms.CharField(min_length=4, label="密码",
                          widget=widgets.PasswordInput(attrs={"class": "form-control", "id": "pwd"}))  #
    r_pwd = forms.CharField(min_length=4, label="确认密码", widget=widgets.TextInput())  #
    email = forms.EmailField(label="邮箱", widget=widgets.EmailInput())
    tel = forms.CharField(label="电话", widget=widgets.TextInput)


    # 校验输入的密码和确认密码是否一致
    def clean(self):
        # 两个只要有一个不能从cleaned_data中拿到值，那就证明对于最开始定义的字段时的校验规则都没有通过，因此也没有必要进行一致性的校验了
        # 两个都能从cleaned_data中拿到值就证明都通过了最基本的规则校验，可以接着进行更复杂的校验了

        pwd = self.cleaned_data.get("pwd")
        r_pwd = self.cleaned_data.get("r_pwd")

        if pwd and r_pwd:
            if pwd == r_pwd:
                # 一致性校验通过，将原始数据返回
                return self.cleaned_data
            else:
                raise ValidationError("两次密码不一致")
        else:
            # 两个只要有一个不能从cleaned_data中拿到值，就没有必要进行一致性的校验了，直接返回原结果
            return self.cleaned_data
        
        
def reg(request):
    if request.method == "POST":
        print(request.POST)
        form = UserForm(request.POST)  # 如果想直接检验从前端获取的数据，那么form表单的name属性值应该与forms组件字段名称一致

        # 使用forms检验数据，返回值是一个布尔值，当传入的值都检验成功时值为True，其他情况均为False
        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)
        else:
            print(form.cleaned_data)
            print(form.errors)

            # 校验全局钩子函数
            print(form.errors.get("__all__")[0])
			
            # 用该变量去前端渲染全局钩子错误
            errors = form.errors.get("__all__")

        return render(request, "reg.html", locals())

    form = UserForm()  # 在接收到 get 请求时，就实例化一个form对象，用于渲染标签
    return render(request, "reg.html", locals())
```

**补充：由于forms 组件的功能比较多，我们一般会把forms组件相关的内容单独放到一个py文件中，比如上述操作都属于 app01 模块的forms组件，可以在模块中新建一个 app01_forms.py 的文件**

```python
# app0/app01_forms.py

from django import forms
from django.forms import widgets
from app01.models import User
from django.core.exceptions import ValidationError


class UserForm(forms.Form):
    # error_message 参数用于设置错误提示信息
    #   固定键值 required 表示为空错误
    #   固定键值 invalid 表示格式错误

    # widget 参数用于设置标签的类型以及添加相关的属性等
    #   widget = widgets.PasswordInput()  规定标签的类型
    #   widget = widgets.PasswordInput(attrs={"class": "form-control", "id": "pwd"})  给标签添加对应的css属性
    name = forms.CharField(min_length=4, label="用户名",
                           error_messages={"required": "信息不能为空", "invalid": "数据格式错误"}, widget=widgets.TextInput())
    pwd = forms.CharField(min_length=4, label="密码",
                          widget=widgets.PasswordInput(attrs={"class": "form-control", "id": "pwd"}))  #
    r_pwd = forms.CharField(min_length=4, label="确认密码", widget=widgets.TextInput())  #
    email = forms.EmailField(label="邮箱", widget=widgets.EmailInput())
    tel = forms.CharField(label="电话", widget=widgets.TextInput)

    # 校验用户名是不是在数据库中已经存在了
    def clean_name(self):
        # 会先执行自带的局部钩子，自带的钩子用于校验定义字段时的那些规则
        # 如果能取到值，表示已经通过字段中的定义规则了，就会接下去执行更复杂的校验
        val = self.cleaned_data.get("name")

        ret = User.objects.filter(name=val)

        if not ret:
            # 数据库中还没有该名字，可以注册，返回该结果
            # _clean_fields 函数会通过 value = getattr(self, 'clean_%s' % name)() 拿到该结果
            # 接着通过 self.cleaned_data[name] = value 把该值添加到 cleaned_data中去，如果已经存在该值，会进行覆盖
            return val
        else:
            # 如果用户名已经存在，引发一个 ValidationError 异常
            # 字段名和自定义的异常信息会以键值对的形式存储到 errors 字典中去，如{"name":["用户名已存在"]}
            raise ValidationError("用户名已存在")

    # 校验输入的密码和确认密码是否一致
    def clean(self):
        # 两个只要有一个不能从cleaned_data中拿到值，那就证明对于最开始定义的字段时的校验规则都没有通过，因此也没有必要进行一致性的校验了
        # 两个都能从cleaned_data中拿到值就证明都通过了最基本的规则校验，可以接着进行更复杂的校验了

        pwd = self.cleaned_data.get("pwd")
        r_pwd = self.cleaned_data.get("r_pwd")

        if pwd and r_pwd:
            if pwd == r_pwd:
                # 一致性校验通过，将原始数据返回
                return self.cleaned_data
            else:
                raise ValidationError("两次密码不一致")
        else:
            # 两个只要有一个不能从cleaned_data中拿到值，就没有必要进行一致性的校验了，直接返回原结果
            return self.cleaned_data

```

```python
# app01/views.py

from django.shortcuts import render, HttpResponse
from app01.app01_forms import *

def reg(request):
    if request.method == "POST":
        print(request.POST)
        form = UserForm(request.POST)  # 如果想直接检验从前端获取的数据，那么form表单的name属性值应该与forms组件字段名称一致

        # 使用forms检验数据，返回值是一个布尔值，当传入的值都检验成功时值为True，其他情况均为False
        print(form.is_valid())

        if form.is_valid():
            print(form.cleaned_data)
        else:
            print(form.cleaned_data)
            print(form.errors)

            # 校验全局钩子函数
            print(form.errors.get("__all__")[0])

            errors = form.errors.get("__all__")

        return render(request, "reg.html", locals())

    form = UserForm()  # 在接收到 get 请求时，就实例化一个form对象，用于渲染标签
    return render(request, "reg.html", locals())

```

