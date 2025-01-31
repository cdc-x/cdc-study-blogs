# Pytest 固件

## 一、固件使用背景

在执行测试用例时，我们常常需要在测试用例执行的前后去完成一些额外的操作。例如针对于 Web 测试，在用例执行前需要打开浏览器，完成用户登录等一系列前置操作；在用例执行完成后，要清除浏览器缓存，关闭浏览器...... Pytest 框架提供的固件机制（又称为夹具）可以帮我们实现一系列的前后置操作。

我们先创建一套测试用例：

![](/static/img/Pytest专题/30.png) 

## 二、前后置函数

### **1. 测试方法级别**

`setup` 和 `teardown` 方法作用于每一个测试方法，每个测试方法执行之前都会先去执行 `setup` 方法，执行之后都会再去执行 `teardown` 方法。

```python
# cases/test_cases.py
class TestCase:

    def setup(self):
        print("\n测试方法执行前做对应的操作！！！")

    def teardown(self):
        print("\n测试方法执行后做对应的操作！！！")

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/31.png" style="zoom:80%;" />  

**需要注意的是：**

- 如果前后置方法是定义在测试类内部的，那么作用的对象是当前测试类中的每一个测试方法，其他测试类和外部的测试方法将不会被影响。

```python
# cases/test_cases.py
class TestCase:

    def setup(self):
        print("\n测试方法执行前做对应的操作！！！")

    def teardown(self):
        print("\n测试方法执行后做对应的操作！！！")

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")


class TestCase2:
    def test_case_004(self):
        print("模拟测试案例004")

    def test_case_005(self):
        print("模拟测试案例005")


def test_outer_case():
    print("模拟测试外部的测试方法")
```

<img src="/static/img/Pytest专题/32.png" style="zoom:80%;" /> 

- 如果前后置方法是定义在测试类外部的，那么作用的对象是定义在外部的测试方法，测试类中的测试方法不会被影响。

```python
# cases/test_cases.py
def setup():
    print("\n测试方法执行前做对应的操作！！！")


def teardown():
    print("\n测试方法执行后做对应的操作！！！")

class TestCase:

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")


class TestCase2:
    def test_case_004(self):
        print("模拟测试案例004")

    def test_case_005(self):
        print("模拟测试案例005")


def test_outer_case():
    print("模拟测试外部的测试方法")
```

<img src="/static/img/Pytest专题/33.png" style="zoom:80%;" /> 

### **2. 测试类级别**

`setup_class` 和 `teardown_class` 方法作用于当前的测试类，每个测试类执行之前都会先去执行 `setup_class` 方法，执行之后都会再去执行 `teardown_class` 方法。测试方法并不会受到这两个方法的影响。

```python
# cases/test_cases.py
class TestCase:

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")


class TestCase2:
    def setup_class(self):
        print("\n测试方法执行前做对应的操作！！！")

    def teardown_class(self):
        print("\n测试方法执行后做对应的操作！！！")

    def test_case_004(self):
        print("模拟测试案例004")

    def test_case_005(self):
        print("模拟测试案例005")
```

<img src="/static/img/Pytest专题/34.png" style="zoom:80%;" /> 

## 三、装饰器实现

使用前后置函数的方式作用的对象都是一个模块内或者是一个测试类中的全体对象，没有办法做到只作用于部分对象。`Pytest` 提供了 `@pytest.fixture()` 方法来实现部分用例的前后置操作。

### **1. 简单使用**

- 第一步，先自定义一个生成器方法

```python
def my_fixture():
    print("前置操作")
    yield
    print("后置操作")
```

- 第二步，添加装饰器方法

```python
import pytest

@pytest.fixture()
def my_fixture():
    print("前置操作")
    yield
    print("后置操作")
```

- 第三步，将函数名作为参数，传递给需要做前后置操作的测试方法。测试方法在执行前会先去执行生成器函数中 `yield` 的前半部分代码；测试方法执行完成后，会去执行生成器函数中 `yield` 的后半部分代码。

```python
# cases/test_cases.py
import pytest

@pytest.fixture()
def my_fixture():
    print("前置操作")
    yield
    print("后置操作")

class TestCase:

    def test_case_001(self, my_fixture):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/35.png" style="zoom:80%;" /> 

### **2. 相关参数详解**

#### **2.1 autouse**

值为 `True` 时，表示固件自动执行，即不需要在对应的测试方法中引用也会触发，默认值为 `False`

```python
# cases/test_cases.py
import pytest

@pytest.fixture(autouse=True)
def my_fixture():
    print("\n前置操作")
    yield
    print("\n后置操作")


class TestCase:
	# 并未传入固件使用
    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/36.png" style="zoom:80%;" /> 

#### **2.2 scope**

表示的是被 `@pytest.fixture` 标记的方法的作用域，有以下几个值：

- **function**：作用于测试方法级别，每个函数或方法都会调用

```python
# cases/test_cases.py
import pytest

@pytest.fixture(scope="function", autouse=True)
def my_fixture():
    print("\n前置操作")
    yield
    print("\n后置操作")


class TestCase:

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/36.png" style="zoom:80%;" /> 

- **class**：作用于测试类级别，测试类执行时会执行一次固件

```python
# cases/test_cases.py
import pytest

@pytest.fixture(scope="class", autouse=True)
def my_fixture():
    print("\n前置操作")
    yield
    print("\n后置操作")

class TestCase:

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")

class TestCase2:

    def test_case_004(self):
        print("模拟测试案例004")

    def test_case_005(self):
        print("模拟测试案例005")
```

<img src="/static/img/Pytest专题/37.png" style="zoom:80%;" /> 

- **module**：作用于测试模块，测试模块（即 `py` 文件）执行时会执行一次固件

<img src="/static/img/Pytest专题/38.png" style="zoom:80%;" /> 

- **session**：作用于会话，可以跨多个`.py` 文件，若多个模块中的用例都调用了 fixture，只会运行一次

```python
####################### cases/test_cases.py ########################
import pytest

@pytest.fixture(scope="session", autouse=True)
def my_fixture():
    print("\n前置操作")
    yield
    print("\n后置操作")

class TestCase:

    def test_case_001(self):
        print("模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
        
####################### cases/test_cases_2.py ##############################
class TestCase2:

    def test_case_004(self):
        print("模拟测试案例004")

    def test_case_005(self):
        print("模拟测试案例005")
```

<img src="/static/img/Pytest专题/39.png" style="zoom:80%;" /> 

#### **2.3 params**

使用装饰器方的固件，还可以进行参数传递。参数的类型支持以下四种：

- 列表
- 元组
- 字典列表：[{}，{}，{}]
- 字典元组：（{}，{}，{}）

我们先打印一下测试方法中接收的固件名的值是啥

```python
import pytest

@pytest.fixture(scope="function")
def my_fixture():
    print("一些操作......")

class TestCase:

    def test_case_001(self, my_fixture):
        print(f"模拟测试案例001----{my_fixture}")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/40.png" style="zoom:80%;" /> 

在固件中我们尝试返回一个值试试：

```python
import pytest

@pytest.fixture(scope="function")
def my_fixture():
    print("一些操作......")
    return "success"

class TestCase:

    def test_case_001(self, my_fixture):
        print(f"模拟测试案例001----{my_fixture}")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/41.png" style="zoom:80%;" /> 

可见在测试方法中传入固件名，除了可以执行固件对应的操作，还可以拿到固件的返回值。那么想结合 `params` 参数在固件中传值就十分容易了：

```python
import pytest

@pytest.fixture(scope="function", params=["aaa", "bbb", "ccc"])
def my_fixture(request):  # 固定写法，使用参数时必须接收一个request变量
    print("一些操作......")
    return request.param  # 固定写法，返回参数

class TestCase:

    def test_case_001(self, my_fixture):
        print(f"模拟测试案例001----{my_fixture}")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/42.png" style="zoom:80%;" /> 

被标记的测试方法被执行了三次，是因为传的参数有三个值，每执行一次就会传一个值过去。

如果固件要执行前后置操作，就不能用 `return` 返回值了，要使用 `yield`:

```python
import pytest

@pytest.fixture(scope="function", params=["aaa", "bbb", "ccc"])
def my_fixture(request):  # 固定写法，使用参数时必须接收一个request变量
    print("前置操作......")
    yield request.param  # 固定写法，返回参数
    print("后置操作......")

class TestCase:

    def test_case_001(self, my_fixture):
        print(f"模拟测试案例001----{my_fixture}")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

#### **2.4 ids**

当固件使用 `params` 进行传值时，给每一个参数值设置一个单独的 id（意义不是很大）

```python
import pytest

@pytest.fixture(scope="function", params=["aaa", "bbb", "ccc"], ids=["parm_1", "parm_2", "parm_3"])
def my_fixture(request):  # 固定写法，使用参数时必须接收一个request变量
    print("前置操作......")
    yield request.param  # 固定写法，返回参数
    print("后置操作......")

class TestCase:

    def test_case_001(self, my_fixture):
        print(f"模拟测试案例001----{my_fixture}")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/43.png" style="zoom:80%;" /> 

#### **2.5 name**

给标记的固件方法起一个别名，后续传参都使用该别名。

```python
import pytest

@pytest.fixture(scope="function", name="init")
def my_fixture():
    print("前置操作......")
    yield
    print("后置操作......")

class TestCase:
    # 测试方法中传参不再是固件函数的名字，而是别名
    def test_case_001(self, init):
        print(f"模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

**注意**：一旦使用别名，就不能再使用原来的函数名作为参数传递了，否则会报错。

```python
import pytest

@pytest.fixture(scope="function", name="init")
def my_fixture():
    print("前置操作......")
    yield
    print("后置操作......")

class TestCase:
    # 固件方法起了别名init，但是测试方法中仍然使用原固件函数名my_fixture作为参数传参
    def test_case_001(self, my_fixture):
        print(f"模拟测试案例001")

    def test_case_002(self):
        print("模拟测试案例002")

    def test_case_003(self):
        print("模拟测试案例003")
```

<img src="/static/img/Pytest专题/44.png" style="zoom:80%;" /> 

## 四、conftest.py 实现

为了避免代码的冗余，测试用例公共的前后置操作往往会抽离出来而不是重复写在每个用例之中。在 `pytest` 中，全局公共的前后置操作要求写在一个名为 `conftest.py` 的文件中。该文件主要有以下特点：

- `conftest.py` 文件是单独存放的一个夹具配置文件，且文件名不能更改，必须是这个名字；
- `conftest.py` 常常和 `fixture` 装饰器联合使用，实现测试项目用例全局的前后置操作，且定义在 `conftest.py` 中的固件函数可以被不同的 py 文件引用；
- 原则上，`conftest.py` 文件需要和执行的用例放在同一层级上（实际上也可以放到其他目录中），且不需要做任何的 `import` 操作。

下面我们就用几个实际的示例来感受以下 `conftest.py` 的强大：

首先，我们创建一套测试用例环境：

<img src="/static/img/Pytest专题/45.png" style="zoom:80%;" /> 

我们先忽略`conftest.py` ，还是按照一开始使用装饰器定义固件的方式创建用例：

```python
############# product/test_product.py #############
import pytest

@pytest.fixture(name="init_product")
def init():
    print("\n产品测试前置操作....")
    yield
    print("\n产品测试后置操作....")

def test_product_case(init_product):
    print("模拟产品测试......")

    
############# user/test_user.py ##################
import pytest

@pytest.fixture(name="init_user")
def init():
    print("\n用户测试前置操作....")
    yield
    print("\n用户测试后置操作....")

def test_user_case(init_user):
    print("模拟用户测试......")
```

此时，我们尝试在两个测试用例里面分别调用对方用例中的固件函数：

```python
############# product/test_product.py #############
import pytest

@pytest.fixture(name="init_product")
def init():
    print("\n产品测试前置操作....")
    yield
    print("\n产品测试后置操作....")

def test_product_case(init_product,init_user):
    print("模拟产品测试......")

    
############# user/test_user.py ##################
import pytest

@pytest.fixture(name="init_user")
def init():
    print("\n用户测试前置操作....")
    yield
    print("\n用户测试后置操作....")

def test_user_case(init_user,init_product):
    print("模拟用户测试......")
```

<img src="/static/img/Pytest专题/46.png" style="zoom:80%;" /> 

结果是显而易见的，由于固件不是定义在当前的 py 文件中的，跨 py 文件引用且不导入，肯定报错。接下来我们将每个模块的固件分别迁移到各自的 `conftest.py` 下：

```python
############# product/conftest.py #############
import pytest

@pytest.fixture(name="init_product")
def init():
    print("\n产品测试前置操作....")
    yield
    print("\n产品测试后置操作....")
    
    
############# product/test_product.py #############
def test_product_case(init_product):
    print("模拟产品测试......")

    
    
############# user/conftest.py ##################
import pytest

@pytest.fixture(name="init_user")
def init():
    print("\n用户测试前置操作....")
    yield
    print("\n用户测试后置操作....")
    
    
############# user/test_user.py ##################
def test_user_case(init_user):
    print("模拟用户测试......")
```

<img src="/static/img/Pytest专题/47.png" style="zoom:80%;" /> 

执行用例通过，可见测试用例在执行时会去自己模块下的 `conftest.py` 中寻找对应的固件执行。当然，此时想要调用对方的固件还是没法调用的：

```python
############# product/test_product.py #############
def test_product_case(init_product, init_user):
    print("模拟产品测试......")
    

############# user/test_user.py ##################
def test_user_case(init_user, init_product):
    print("模拟用户测试......")
```

<img src="/static/img/Pytest专题/48.png" style="zoom:80%;" /> 

要想实现可以全局调用，可以将两个模块中的固件方法迁移到根目录下的 `conftest.py` 中：

```python
# 根目录下的 conftest.py
import pytest

@pytest.fixture(name="init_product")
def product_init():
    print("\n产品测试前置操作....")
    yield
    print("\n产品测试后置操作....")


@pytest.fixture(name="init_user")
def user_init():
    print("\n用户测试前置操作....")
    yield
    print("\n用户测试后置操作....")

```

此时，我们尝试在两个测试用例里面分别调用对方用例中的固件函数：

```python
############# product/test_product.py #############
def test_product_case(init_product, init_user):
    print("模拟产品测试......")
    

############# user/test_user.py ##################
def test_user_case(init_user, init_product):
    print("模拟用户测试......")
```

<img src="/static/img/Pytest专题/49.png" style="zoom:80%;" /> 

执行用例通过，且固件执行的顺序取决于固件名参数在测试方法中传参的位置，先传入的就先执行。