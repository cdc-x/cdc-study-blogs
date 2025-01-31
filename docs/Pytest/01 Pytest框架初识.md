# Pytest初识

## 一、单元测试框架简介

### **1. 什么是单元测试**

单元测试是指在软件开发过程中，针对软件的最小单位（函数，方法）进行正确性的检查测试。

### **2. 常用单元测试框架**

#### 2.1 Java 类别

- junit
- testng

#### 2.2 Python 类别

- unittest
- pytest

### **3. 单元测试框架主要作用**

- 测试发现：从多个文件中找到测试用例
- 测试执行：按照一定的顺序和规则去执行用例，并生成结果
- 测试判断：通过断言判断预期结果和实际结果的差异
- 测试报告：统计测试进度、耗时、通过率，生成测试报告

## 二、自动化测试框架简介

### **1. 什么是自动化测试框架**

自动化测试框架是指为了完成一个指定的系统的自动化测试而封装的一整套的完成的代码。主要封装了自动化的基础模块、管理模块、测试的统计模块等等。

### **2. 自动化测试框架的作用**

- 提高测试效率，降低维护成本
- 减少人工干预，提高测试的准确性，增加代码的重用性
- 核心思想是让不懂代码的人也能够通过这个框架去实现自动化测试

### **3. 单元测试框架和自动化测试框架的关系**

单元测试框架只是自动化测试框架中的组成部分之一。一套完整的自动化测试框架还包括：pom设计模式、数据驱动、关键字驱动、全局配置文件的封装、日志监控、类似selenium和requests等功能模块的二次封装、断言系统、测试报告邮件等许多方面。

## 三、Pytest 简介

pytest 是一个非常成熟的 Python 单元测试框架，比 unittest 更加灵活，功能也更加强大。使用 pytest 主要有以下优点：

- 可以和 selenium，requests，appium 结合实现 web 自动化，接口自动化，app 自动化；

- 可以实现测试用例的跳过和失败用例重跑机制；

- 可以和 allure 结合生成非常美观的测试报告；

- 可以和 jenkins 持续集成；

- 有很多强大的插件：
  - pytest：指定用例执行方式

  - pytest-html：生成 html 格式的自动化测试报告

  - pytest-xdist：测试用例分布执行，多 CPU 分发

  - pytest-ordering：用于改变测试用例的执行顺序

  - pytest-rerunfailures：用例失败后重跑

**框架和插件的下载：**

```python
# 直接通过 pip 下载
pip install pytest
pip install pytest-html
pip install pytest-xdist
pip install pytest-ordering
pip install pytest-rerunfailures
```

## 四、Pytest 的使用

### **1. 默认规则**

在使用 pytest 框架时，需要遵循一些默认的规则（规则不是固定的，可以根据全局配置文件进行规则的修改）：

- 模块名必须以 `test_` 开头或者以 `_test` 结尾；
- 测试类必须以 `Test` 开头，并且不能有 `init` 方法；
- 测试方法必须以 `test` 开头

如下我们创建一套测试环境，其中 cases 目录为自定义目录，用于存放所有的测试用例： 

![](/static/img/Pytest专题/01.png) 

### **2. 测试用例的执行**

构造如下测试项目：

<img src="/static/img/Pytest专题/02.png" style="zoom:80%;" /> 

其中各个用例模块的代码内容为：

```python
# product_cases/test_product.py
def test_out_case():
    print("模拟测试用户退出操作")

    
# user_cases/test_login.py
def test_other_case():
    print("模拟测试其他情况")

class TestLogin:

    def test_case_001(self):
        print("模拟测试用户登录操作")
        

# user_cases/test_logout.py
def test_out_case():
    print("模拟测试用户退出操作")
```

**pytest 用例的执行主要有以下三种方式：**

- 通过主函数执行
- 通过命令模式执行
- 通过读取 `pytest.ini` 配置文件执行

#### **2.1 通过主函数执行**

##### 2.1.1 执行所有用例

我们修改一下用户登录的用例：

```python
# user_cases/test_login.py
# 导入pytest
import pytest


def test_other_case():
    print("模拟测试其他情况")


class TestLogin:

    def test_case_001(self):
        print("模拟测试用户登录操作")


if __name__ == '__main__':
    pytest.main()  # 执行测试用例
```

此时直接执行：

<img src="/static/img/Pytest专题/03.png" style="zoom:80%;" /> 

从终端的打印可以看到，用例文件中的用例方法 `test_other_case` 以及测试类中的测试方法 `test_case_001` 都被执行了。

我们再来修改一下产品操作的用例，并执行看看效果：

```python
# product_cases/test_product.py
import pytest

def test_product_case():
    print("模拟测试产品相关的操作")


if __name__ == '__main__':
    pytest.main()
```

<img src="/static/img/Pytest专题/04.png" style="zoom:80%;" /> 

可见，只需要在对应的用例文件中调用主函数，就可以自动执行文件中的所有用例。但是在实际的使用中，我们一般会将用例分类写在不同目录下的不同用例文件里，且执行时要批量去执行所有的用例，所以我们往往会将主函数写在一个与用例文件夹平级的 `py` 文件中，pytest 内部设有对应的机制，会自动扫描全局，执行所有的用例文件。

<img src="/static/img/Pytest专题/05.png" style="zoom:80%;" /> 

```python
# all.py
import pytest

if __name__ == '__main__':
    pytest.main()
```

执行 `all.py` 文件，从终端输出可以看到，所有的用例都被执行了：

<img src="/static/img/Pytest专题/06.png" style="zoom:80%;" /> 



##### 2.1.2 运行指定模块

```python
# all.py
import pytest

if __name__ == '__main__':
    pytest.main(["./user_cases/test_logout.py"])  # 只运行用户退出操作用例
```

<img src="/static/img/Pytest专题/07.png" style="zoom:80%;" /> 



##### 2.1.3 运行指定目录

```python
# all.py
import pytest

if __name__ == '__main__':
    pytest.main(["./user_cases"])  # 只运行用户目录下的所有用例
```

<img src="/static/img/Pytest专题/08.png" style="zoom:80%;" /> 



##### 2.1.4 通过nodeid指定用例运行

注：nodeid 由模块名、分隔符、类名、方法名、函数名组成

```python
# all.py
import pytest

if __name__ == '__main__':
    # 只运行用户登录用例模块中的 test_other_case 测试方法
    pytest.main(["-vs", "./user_cases/test_login.py::test_other_case"])
    # 只运行用户登录用例模块中的 TestLogin 测试类中的 test_case_001 测试方法
    pytest.main(["-vs", "./user_cases/test_login.py::TestLogin::test_case_001"])
```

#### **2.2 通过命令模式去执行**

##### 2.2.1 执行所有用例

直接通过 `pytest` 命令就可以执行当前目录下所有的测试用例，所以要想执行项目中所有的用例，只需要将执行目录切换到根目录即可：

<img src="/static/img/Pytest专题/09.png" style="zoom:80%;" /> 



##### 2.2.2 运行指定模块

```python
pytest ./product_cases/test_product.py
```

<img src="/static/img/Pytest专题/10.png" style="zoom:80%;" /> 



##### 2.2.3 运行指定的目录

```python
pytest ./product_cases
```

<img src="/static/img/Pytest专题/11.png" style="zoom:80%;" /> 



##### 2.2.4 通过nodeid指定用例运行

```python
pytest ./user_cases/test_login.py::test_other_case
pytest ./user_cases/test_login.py::TestLogin::test_case_001
```

<img src="/static/img/Pytest专题/12.png" style="zoom:80%;" /> 

<img src="/static/img/Pytest专题/13.png" style="zoom:80%;" /> 

#### **2.3 通过配置文件去执行**

在真正的项目中，都是通过配置全局的配置文件来执行测试用例的。其余两种执行方式在编写和调试用例时比较方便。对于配置文件，有以下几点要求：

- 配置文件的名字必须为 `pytest.ini`
- 配置文件一般放置在项目的根目录
- 配置文件必须时 ANSI 编码

`pytest.ini` 是 pytest 框架的核心配置文件，它可以改变 pytest 的默认行为，不管是通过主函数还是命令模式去执行用例，都会先去读取配置文件。

```ini
# pytest.ini
[pytest]
# 命令行参数，多个参数用空格隔开
addopts = -vs --reruns=2
# 测试用例文件夹，可自己配置
testpaths = ./user_cases
# 配置测试搜索的模块名称,如下表示搜索的模块必须以test_开头
python_files = test_*.py
# 配置测试搜索的测试类名,如下表示搜索的测试类必须以Test开头
python_classes = Test*
# 配置测试搜索的测试函数名
python_functions = test
```

修改完配置后，再去执行用例时就会按照配置上要求去执行：

<img src="/static/img/Pytest专题/24.png" style="zoom:80%;" /> 

### **3. 执行时使用参数**

我们在执行用例时，可以通过添加相关的参数实现不同的执行效果，提升测试效率。

- **- s：用于输出调试信息，包括打印输出的信息**

```python
# 通过main函数执行
pytest.main(["-s", "./product_cases/test_product.py"])
# 通过命令模式执行
pytest -s ./product_cases/test_product.py
```

<img src="/static/img/Pytest专题/14.png" style="zoom:80%;" />   

- **-v：显示更详细的信息**

```python
# 通过main函数执行
pytest.main(["-v", "./product_cases/test_product.py"])
# 通过命令模式执行
pytest -v ./product_cases/test_product.py
```

<img src="/static/img/Pytest专题/15.png" style="zoom:80%;" /> 

- **-vs：将 -v 和 -s 参数效果结合，推荐使用**

<img src="/static/img/Pytest专题/16.png" style="zoom:80%;" /> 

- **-n：支持多线程或者分布式运行测试用例**

```python
# 通过main函数执行
# 开两个线程去执行用户操作目录下所有的用例
pytest.main(["-vs", "./user_cases", "-n=2"])

# 通过命令模式执行
pytest -vs ./user_cases -n 2
```

为了方便看出测试效果，我们给用户操作目录下的每个测试方法中都添加一个睡眠延时：

```python
# user_cases/test_login.py
import time

def test_other_case():
    time.sleep(2)
    print("模拟测试其他情况")

class TestLogin:

    def test_case_001(self):
        time.sleep(2)
        print("模拟测试用户登录操作")
        

# user_cases/test_logout.py
import time

def test_out_case():
    time.sleep(2)
    print("模拟测试用户退出操作")
```

正常执行结果：

<img src="/static/img/Pytest专题/17.png" style="zoom:80%;" /> 

开启线程执行结果：

<img src="/static/img/Pytest专题/18.png" style="zoom:80%;" /> 

- **--reruns：失败用例重跑**

```python
# 通过main函数执行
# 如果用例执行失败，该用例再执行2次
pytest.main(["-vs", "./user_cases", "--reruns=2"])

# 通过命令模式执行
pytest -vs ./user_cases --reruns 2
```

为了方便测试重跑机制，我们将用户操作用例中的 `test_other_case` 测试方法添加一个断言，使其报错

```python
# user_cases/test_login.py
def test_other_case():
    print("模拟测试其他情况")
	assert 1 == 2  # 执行到此时报错，该用例不会通过
    
class TestLogin:

    def test_case_001(self):
        print("模拟测试用户登录操作")
```

<img src="/static/img/Pytest专题/19.png" style="zoom:80%;" /> 

- **-x：只要有一个用例失败，就停止执行**

```python
# 通过main函数执行
pytest.main(["-vs", "./user_cases", "-x"])

# 通过命令模式执行
pytest -vs ./user_cases -x
```

<img src="/static/img/Pytest专题/20.png" style="zoom:80%;" /> 

- **--maxfail：出现失败的用例数达到规定值时停止测试**

```python
# 通过main函数执行
# 失败的用例数达到两个时停止测试
pytest.main(["-vs", "./user_cases", "--maxfail=2"])

# 通过命令模式执行
pytest -vs ./user_cases --maxfail 2
```

- **-k：根据测试用例的部分字符串指定用例执行**

```python
# 通过main函数执行
# 只执行用例名字中包含01的用例
pytest.main(["-vs", "./user_cases", "-k=01"])

# 通过命令模式执行
pytest -vs ./user_cases --k="01"
```

<img src="/static/img/Pytest专题/21.png" style="zoom:80%;" /> 

如果指定的字符串是被测试类包含的，那么测试类中所有的测试方法都会被执行。

- **--html：用例执行完成后生成 html 格式的测试报告**

<img src="/static/img/Pytest专题/28.png" style="zoom:80%;" /> 

执行完成后，在本地的 report 文件夹中生成了对应的测试报告，打开可以看到用例执行的详情。

<img src="/static/img/Pytest专题/29.png" style="zoom:80%;" /> 

### **4. 用例执行顺序**

我们再往 `test_product.py` 中添加一些测试方法：

```python
# product_cases/test_product.py
def test_product_case():
    print("模拟测试产品相关的操作")


def test_case_002():
    print("测试方法_002")


def test_case_003():
    print("测试方法_003")


def test_case_001():
    print("测试方法_001")


def test_case_004():
    print("测试方法_004")


def test_case_005():
    print("测试方法_005")

```

执行该测试模块：

<img src="/static/img/Pytest专题/22.png" style="zoom:80%;" /> 

从执行的打印可以看出来，`pytest` 对于用例的执行顺序是按照从上到下执行的，并不像 `unittest` 那样是按照用例名的 ASCII 大小来执行的，当然我们也是有方法可以改变用例的执行顺序的。

```python
# product_cases/test_product.py
import pytest

def test_product_case():
    print("模拟测试产品相关的操作")

# 通过装饰器方法 @pytest.mark.run() 可以改变用例的执行顺序
# order参数表示该用例要排在第几个执行
@pytest.mark.run(order=2)
def test_case_002():
    print("测试方法_002")


@pytest.mark.run(order=3)
def test_case_003():
    print("测试方法_003")


@pytest.mark.run(order=1)
def test_case_001():
    print("测试方法_001")


def test_case_004():
    print("测试方法_004")


def test_case_005():
    print("测试方法_005")
```

<img src="/static/img/Pytest专题/23.png" style="zoom:80%;" /> 

从终端输出的结果可以看到，被装饰器装饰的测试用例都按照了指定的顺序执行，没有被装饰到的用例还是按照从上到下的顺序执行。

### **5. 分组执行用例**

在实际的测试业务中，我们常常需要去测试某些模块中的某些用例，例如冒烟测试、分模块执行、分接口和web执行等测试场景中就常常涉及到这种需求。`pytest` 提供了十分便捷的分组执行用例的方式。

- 第一步，使用装饰器去装饰要执行的用例，`@pytest.mark.name`，其中 `name` 为自定义的名称

```python
#################### product_cases/test_product.py ######################
import pytest

def test_product_case():
    print("模拟测试产品相关的操作")

def test_case_002():
    print("测试方法_002")

def test_case_003():
    print("测试方法_003")

@pytest.mark.product_manage
def test_case_001():
    print("测试方法_001")

@pytest.mark.smoke
def test_case_004():
    print("测试方法_004")

@pytest.mark.product_manage
def test_case_005():
    print("测试方法_005")

    
################## user_cases/test_login.py #######################
import pytest

def test_other_case():
    print("模拟测试其他情况")

class TestLogin:
    @pytest.mark.user_manage
    def test_case_001(self):
        print("模拟测试用户登录操作")

        
################### user_cases/test_logout.py ########################
def test_out_case():
    print("模拟测试用户退出操作")
```

- 第二步，去 `pytest.ini` 中添加分组配置，配置名必须和装饰器中的 `name` 值一致

```ini
[pytest]
addopts = -vs --reruns=2
testpaths = .
python_files = test_*.py
python_classes = Test*
python_functions = test
# 分组配置
markers = 
	smoke:冒烟用例
	user_manage:用户管理用例
	product_manage:商品管理用例
```

- 第三步，执行时通过参数 `-m` 指定要执行的用例的分组类别

```python
pytest -m "smoke"
```

<img src="/static/img/Pytest专题/25.png" style="zoom:80%;" /> 

### **6. 跳过用例**

对于某些我们不想执行的用例，可以使用 `pytest` 提供的装饰器方法跳过该用例不去执行。

#### **6.1 无条件跳过**

**装饰器语法：**`@pytest.mark.skip(reason="")` ，reason 参数可选，表示跳过该测试方法的原因

```python
#################### product_cases/test_product.py ######################
import pytest

@pytest.mark.skip(reason="无意义的测试方法")
def test_product_case():
    print("模拟测试产品相关的操作")


def test_case_001():
    print("测试方法_001")


def test_case_002():
    print("测试方法_002")
```

<img src="/static/img/Pytest专题/26.png" style="zoom:80%;" />

#### **6.2 有条件跳过** 

**装饰器语法：**`@pytest.mark.skipif(判断条件, reason="")` ，reason 参数可选，表示跳过该测试方法的原因

```python
import pytest

num = 20

@pytest.mark.skip(reason="无意义的测试方法")
def test_product_case():
    print("模拟测试产品相关的操作")
    
@pytest.mark.skipif(num > 18, reason="超过限定条件")
def test_case_001():
    print("测试方法_001")

def test_case_002():
    print("测试方法_002")
```

<img src="/static/img/Pytest专题/27.png" style="zoom:80%;" /> 