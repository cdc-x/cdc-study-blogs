# Hello，Flask

## Flask简介

​		Flask 是一款轻量级的 Web 开发框架，为了方便体现出 Flask 的特点，我们可以用另一款教科书级别的框架 Django 来进行对比。

<img src="/static/img/Flask专题/Flask初识2.png"  />                 		  <img src="/static/img/Flask专题/Flask初识1.png" style="zoom:80%;" /> 

​						学完 Django 的我																  学完 flask 的我

​		Django 主要特点是大而全，集成了很多组件，例如：`Models` `Admin` `Form` 等等，不管你用得到用不到，反正它全都有，属于全能型框架，适用于大型或者密集型的项目开发。

​		Flask 主要特点是小而轻，原生的组件几乎为0， 组件都是靠第三方提供，属于短小精悍型框架，适用于小型或者API服务类项目的开发。

​		形象的来比较两者的区别，Django 如果是一件棉袄的话，那么 flask 就是一件短袖。虽然 Django 功能强大，但是不论项目的大小，使用 Django 都需要占用计算机很多的资源，而相反 flask 占用的资源较少，速度较快；同样的，因为 flask 的精简，没有 Django 那么多自带的功能，所以很多功能的实现都是依靠的第三方组件，这也导致了 flask 的稳定性远远比不上 Django。总而言之，两者各有千秋，所擅长的领域不一样，还是需要根据具体的业务来选择更适合的框架。

## Flask项目搭建

### **一、下载安装**

```
pip install flask
```

安装完成以后我们可以观察到，在安装 flask 的同时也安装了以下依赖包：

<img src="/static/img/Flask专题/flask初识3.png" style="zoom:80%;" />  

#### **Flask**

Flask文件库

#### **Jinja2**

模板语言渲染库

#### **MarkupSafe**

返回安全标签（只要 flask 返回模板或者标签使都会依赖 MarkupSafe）

#### **Werkzeug**

是德文 "工具" 的意思，在 flask 种的作用相当于 Django 种的 uWSGI，flask 项目的启动都是依赖于 Werkzeug 的。

**补充：** WSGI，即应用程序网关接口，uWSGI 和 Werkzeug 都是在 WSGI 上层封装的方法，两者本质上没有什么区别，只是应用的框架不一样。

### **二、项目搭建**

使用 flask 搭建一个简单的 web 项目不需要像 Django 那样创建一整套的项目结构和项目配置，flask 甚至可以简单到将所有的后台代码写在一个 py 文件当中。

```python
from flask import Flask  # 导入 Flask 类，创建 Flask 应用对象

app = Flask(__name__)  # app = application

@app.route("/index")  # 为 Flask 对象增加路由
def index():          # 与路由绑定的视图函数，函数名尽量保持唯一
    return "Hello, Flask"

if __name__ == '__main__':
    app.run()  # 启动 Flask 应用程序
```

直接运行脚本程序，终端会打印以下信息：

```
 * Serving Flask app "demo" (lazy loading)
 * Environment: production
   WARNING: This is a development server. Do not use it in a production deployment.
   Use a production WSGI server instead.
 * Debug mode: off
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

此时使用浏览器访问 http://127.0.0.1:5000/index，会看到以下结果：

<img src="/static/img/Flask专题/flask初识.png" style="zoom:80%;" /> 

如果我们想修改默认访问的 IP 和 端口，只需要在启动脚本的时候这样写

```python
from flask import Flask  # 导入 Flask 类，创建 Flask 应用对象

app = Flask(__name__)  # app = application

@app.route("/index")  # 为 Flask 对象增加路由
def index():          # 与路由绑定的视图函数，函数名尽量保持唯一
    return "Hello, Flask"

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=8000)  # 启动 Flask 应用程序
```

同样，我们也可以设置 debug 模式，方便编写时调试（线上部署的项目最好不要设置 debug 模式，容易暴露代码）

```python
from flask import Flask  # 导入 Flask 类，创建 Flask 应用对象

app = Flask(__name__)  # app = application

app.debug = True  # 设置 debug 模式，方便调试

@app.route("/index")  # 为 Flask 对象增加路由
def index():  # 与路由绑定的视图函数，函数名尽量保持唯一
    return "Hello, Flask"

if __name__ == '__main__':
    app.run(host="0.0.0.0", port=8000)  # 启动 Flask 应用程序
    # app.run(host="0.0.0.0", port=8000, debug=True)  # 也可以在启动程序的位置设置 debug 模式
```

​                                            <img src="/static/img/Flask专题/flask初识4.png" style="zoom:80%;" /> 