# Hello, Python

## 一、前戏

### 1. 编程语言简介

在生活中，如果你想和外国人进行交流，你就得懂得他们国家的语言（汉语、英语、德语、法语等等），同样的，编程语言就是人与计算机之间沟通的一门语言，它能够将我们想要的操作翻译成计算机能够看得懂的指令，从而让计算机按照人类的想法去工作。

编程语言的种类很多，你可能听说过 C语言，Java语言，Go语言等等，尽管语法、实现机制都有差异，但是其本质和目的都是一样的。目前编程语言都可以分为**解释型**和**编译型**两大类。

#### 1.1 编译型语言

编译型语言会使用编译器，先把源程序的每一条语句都编译成机器语⾔，并保存成二进制⽂件，再去执行这个二进制文件。这样程序运行时计算机可以直接以机器语言的方式来运行此程序，所以速度很快。简而言之就是先翻译再执行，典型的代表语言有 Go语言，C语言 等。

- **优点：**编译器一般会有预编译的过程，会对代码进行优化。因为编译只做一次，运行时不需要再次编译，所以编译型语言的程序执行效率高，可以脱离语言环境独立运行。
- **缺点：**编译之后如果需要修改源代码，需要对整个模块重新编译。编译的时候会根据对应的运行环境生成机器码，因此不同的操作系统之间移植会存在问题，需要根据运行的操作系统环境编译不同类型的可执行文件。如 Windows 的可执行文件类型是 `.exe`类型文件，在 Linux 环境上就无法使用。

#### 1.2 解释型语言

解释型语言会使用解释器，在执行程序时一条一条的将源代码解释成机器语言给计算机来执行，所以运行速度是不如编译后的程序运行的快的（这是因为计算机不能直接识别并执行我们写的语句，它只能认识机器语言，即二进制形式）。简而言之，就是边翻译边执行，典型的代表语言有 Python。

- **优点：**有良好的平台兼容性，在任何环境中都可以运行，前提是安装了解释器（虚拟机）；程序灵活，修改代码的时候直接修改就可以了，可以快速部署，不用停机维护。
- **缺点：**每次运行的时候都要解释一遍，性能上不如编译型语言。

<img src="./static/img/Python专题/00023.png" style="zoom:80%;" />  

## 二、Python简介

#### 1. Python优点

- 开发效率高，有丰富的第三方库和模块；
- 是一门高级语言，开发过程中无需考虑管理程序使用内存等一系列底层细节问题；
- 可移植性强；
- 可拓展性强；
- 可嵌入性强；

#### 2. Python缺点

- 由于是解释型语言，所以运行速度慢；
- 代码不能加密，源码是用明文形式存放的；
- 线程不能利用CPU问题，GIL(Global Interpreter Lock)，即全局解释器锁导致该问题；

更多相关介绍 *[戳这里……](https://baike.baidu.com/item/Python/407313?fr=aladdin)*

由于Python的简易且拥有强大的三方库支持，自诞生以来就一直受到广大开发者的喜爱。

## 三、环境搭建

### 1. 解释器安装

#### 1.1 下载安装

官网下载地址：*[https://www.python.org/downloads/](https://www.python.org/downloads/)*

- 第一步，访问官网，根据自己的计算机系统选择合适的安装包资源（以下步骤只针对windows）

<img src="/static/img/Python专题/00001.png" style="zoom: 50%;" /> 



- 第二步，选择版本（推荐 3.6 版本及其以上）

<img src="/static/img/Python专题/00002.png" style="zoom: 67%;" /> 



- 第三步，傻瓜式安装，疯狂点击下一步

<img src="/static/img/Python专题/00003.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00004.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00005.png" style="zoom: 40%;" /> 

<img src="/static/img/Python专题/00006.png" style="zoom: 50%;" /> 

#### 1.2 安装验证

- 第一步，打开命令提示符

<img src="/static/img/Python专题/00007.png" style="zoom: 40%;" /> 

- 第二步，输入 “python”，按下回车键，若出现相关信息，则证明安装成功

<img src="/static/img/Python专题/00008.png" style="zoom: 50%;" /> 

#### 1.3 常见问题

##### 1.3.1 情形一，系统找不到应用程序

命令提示符输入python指令验证时，提示 “python 不是内部或外部命令，也不是可运行的程序” 或者直接会打开商城搜索可应用程序，这是由于未配置系统环境变量引起的。

将解释器的安装目录配置到系统环境变量中即可：

<img src="/static/img/Python专题/00009.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00010.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00011.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00012.png" style="zoom: 50%;" /> 

##### **1.3.2 情形二，版本不一致**

 命令提示符输入python指令验证时，显示的python版本信息非安装的版本，例如下载安装的版本是 python 3.8.9，但输入指令显示的是 python 3.6.6，这是由于本地安装了多个版本的python，且系统读取环境变量时是从上往下读取的，因此想要使用某个版本的 python解释器，只需将该版本对应的环境变量提到最前面即可。

<img src="/static/img/Python专题/00013.png" style="zoom: 50%;" />        <img src="/static/img/Python专题/00014.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00015.png" style="zoom: 50%;" />         <img src="/static/img/Python专题/00016.png" style="zoom: 50%;" /> 

##### 1.3.3 情形三，同时使用多个解释器

针对于 1.3.2 的情形，修改环境变量可以实现使用指定的解释器，但是如果想要切换解释器，需要不停的修改环境变量对应的位置，十分麻烦。可以通过以下方式实现直接调用不同版本的python：

- 指定解释器位置使用对应的解释器（推荐）

<img src="/static/img/Python专题/00017.png" style="zoom: 50%;" /> 

<img src="/static/img/Python专题/00018.png" style="zoom: 50%;" /> 

- 重命名解释器名（不推荐）

<img src="/static/img/Python专题/00019.png" style="zoom: 50%;" />          <img src="/static/img/Python专题/00020.png" style="zoom: 50%;" />  

<img src="/static/img/Python专题/00021.png" style="zoom: 50%;" />  <img src="/static/img/Python专题/00022.png" style="zoom: 50%;" /> 

### 2. IDE 安装

Python 对开发工具没有什么限制，可以根据开发者的喜好自行选择。比较推荐的是使用 Pycharm 进行开发。

官网地址：[https://www.jetbrains.com/pycharm/](https://www.jetbrains.com/pycharm/)

