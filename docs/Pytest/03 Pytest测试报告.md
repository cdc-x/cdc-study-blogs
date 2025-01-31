# Pytest之生成allure报告

## 一、前戏

在之前我们已经学会使用 `pytest-html` 插件生成 html 格式的测试报告：

```python
# 第一步，安装插件
pip install pytest-html

# 第二步，执行用例时使用 --html 参数
## main 函数中执行
if __name__ == '__main__':
    pytest.main(["-vs", "--html=./report/result.html"])
    
## 使用命令模式执行
pytest -vs --html ./report/result.html
```

![](/static/img/Pytest专题/50.png) 

很明显报告的效果配不上我们高大上的逼格.......除了 `pytest-html` 插件，`pytest` 还可以和 `allure` 结合，生成更加详细美观的测试报告。

## 二、allure的使用

- 第一步，下载第三方插件

```python
pip install allure-pytest
```

- 第二步，访问 `allure` 官网，下载最新的版本

```
网址：https://github.com/allure-framework/allure2/releases
```

 <img src="/static/img/Pytest专题/51.png" style="zoom:80%;" />  

- 第三步，解压并配置环境变量，将解压后的一直到 `bin` 目录的文件路径添加到计算机环境变量的 `path` 中

<img src="/static/img/Pytest专题/52.png" style="zoom:80%;" /> 

- 第四步，验证是否配置成功（如果 IDE 的终端中无法执行检查版本的命令，重启 IDE 即可）

<img src="/static/img/Pytest专题/53.png" style="zoom:100%;" /> 

<img src="/static/img/Pytest专题/54.png" style="zoom:80%;" /> 

- 第五步，在项目的配置文件 `pytest.ini` 中添加参数 `--alluredir ./tmp`

```ini
[pytest]
addopts = -vs --alluredir ./tmp
testpaths = .
python_files = test_*.py
python_classes = Test*
python_functions = test
```

这个参数的作用是在用例执行时，会在临时文件夹 `tmp` 中生成很多 `json` 文件，这些文件记录了用例执行过程中的相关信息，最后生成报告使用到的数据就是从 `json` 中获取的。

<img src="/static/img/Pytest专题/55.png" style="zoom:80%;" /> 

- 第六步，执行完用例后运行命令生成报告

```python
allure generate ./tmp -o ./report --clean

# 参数详情
# ./tmp：存放临时 json 数据的目录
# -o：表示输出 output
# ./report：测试报告存放目录
# --clean：清空 report 目录中原有的数据
```

为了方便起见，我们一般会把生成报告的命令直接写在主函数里面：

```python
# 根目录下新建一个 all.py
import pytest
import os

if __name__ == '__main__':
    pytest.main()  # 执行项目中所有用例
    os.system("allure generate ./tmp -o ./report --clean")  # 生成allure测试报告
```

最后报告会生成在 `report` 目录下：

<img src="/static/img/Pytest专题/56.png" style="zoom:80%;" /> 

