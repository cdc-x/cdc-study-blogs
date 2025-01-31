# RabbitMQ简介及环境安装

## 一、什么是消息队列

在之前的多进程讲解中，曾介绍利用 Queue 编写生产者消费者模式。它的原理就是准备一个容器，生产者生产了数据就丢到容器里，消费者从容器中取数据而不是直接去问生产者要数据，这样就不需要和生产者进程直接交互，从而降低了两者之间的耦合性。

消息队列的原理和上述差不多，是生产者消费者模型的一个典型代表。消息队列可以抽象的理解为一个功能更加强大的队列。不同服务器上的进程想要实现通信，可以把消息队列作为第三媒介。目前使用较多的消息队列有 ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ。本章节博客主要介绍 RabbitMQ 。

## 二、为什么需要消息队列

消息队列中间件是分布式系统中重要的组件，主要应用于解决解耦，处理异步消息，流量削锋等问题，可实现高性能，高可用，可伸缩和最终一致性架构。下面我们结合一个实际的例子，简单来探讨下使用消息队列的好处。

现在有一个外卖系统，用户通过客户端和订单系统进行交互，订单系统接收到用户订单后，又会分别去调用商家通知系统，配送员通知系统和支付系统，等订单系统收到三个子系统的回应，再告诉用户下单成功。在上述系统的实现过程中，主要需要关注以下问题：

### **1、异步消息和解耦**

我们先按照上述需求将系统的执行流程图简单梳理一下：

<img src="/static/img/RabbitMQ专题/外卖系统.png" style="zoom: 67%;" /> 

首先用户先下单，订单系统接收到用户下单请求后，通知商家，商家收到订单系统提示后，再通知外卖配送员......依次通知下去，直到订单系统接收到最后的返回结果，再通知用户下单成功了。上述过程是一个线性的过程，显然不符设计需求，利用并发知识，我们可以将系统稍作修改：

<img src="/static/img/RabbitMQ专题/外卖系统并发.png" style="zoom:67%;" /> 

当订单系统接收到用户下单请求时，同时通知三个子系统，让他们同时去执行，等接收到三个子系统的响应消息后，再最后通知用户下单成功了。虽然做到了异步执行，但是仔细想想，设计方案还是存在问题的。

首先，在响应时间方面，系统虽然让三个子系统并发执行了，但是还是要等待接收到所有的返回结果后才能响应用户，如果某个子系统的处理时间过长，那么响应用户的时间也会延长，用户体验感较差；其次，在异常处理机制方面，如果某个子系统处理时报错，那么响应用户就会失败，这显然是不符合基本需求的，因为这是由于系统自身的 bug 导致任务的失败，而不是由用户操作引起的，所以理论上只要用户下单且不是因为用户的误操作导致的订单失败，不管后台如何去设计异常处理机制，最后响应用户的状态应该永远是下单成功。

针对上述问题，我们可以使用消息队列做进一步的优化：

<img src="/static/img/RabbitMQ专题/外卖系统消息队列.png" style="zoom:67%;" /> 

订单系统收到用户请求后，立即响应用户，接着将用户的请求内容放到消息队列中，各个子系统并发去消息队列中取出对应的请求去执行。这样做的好处是，首先响应用户的时间快，其次子系统和订单系统的耦合度减弱了，系统与系统间的交互都是通过中间的消息队列来实现的。

### **2、流量削锋**

使用消息队列还有个好处就是可以流量削锋，例如假设每个子系统的消息处理能力只有一百条，此时正逢店里搞促销活动，商品都在打折，同一时间突然有上万人同时下单，如果还是采用之前并发的方式，生产者生产数据的速度远远大于消费者的处理速度，此时服务器基本就瘫痪了；而使用消息队列以后，不管生产者有多少数据，都先放在队列中，消费者按照自己的处理能力，分批去队列中获取请求再处理，这样就可以避免服务器瘫痪的风险。

## 三	RabbitMQ安装

### **1、erlang安装**

- 由于 RabbitMQ 是 Erlang 编写的，所以要先安装 Erlang。下载地址：https://www.erlang-solutions.com/resources/download.html，建议下载较新的版本，否则安装 RabbitMQ  会报版本过低无法安装错误。

<img src="/static/img/RabbitMQ专题/erl_1.png" style="zoom: 50%;" /> 

-   下载完后，直接傻瓜式安装，疯狂下一步即可，期间也可以修改默认的安装地址
- 安装完成后，配置环境变量
  - 右键计算机 ===> 属性 ===> 高级系统设置过 ===> 环境变量 ===> 系统变量 ===> 新建
  - 变量名：ERLANG_HOME，变量值就是 erlang 的安装地址

 <img src="/static/img/RabbitMQ专题/erl_2.png" style="zoom: 80%;" /> 

- 双击系统变量 path

<img src="/static/img/RabbitMQ专题/erl_3.png" style="zoom: 80%;" /> 

- 点击新建，将 %ERLANG_HOME%\bin 加入到 path 中，一路点击确定保存

<img src="/static/img/RabbitMQ专题/erl_4.png" style="zoom:80%;" /> 

- 最后代开 cmd ，输入 erl，检查是否有相关的版本信息，如果有表示安装成功。

<img src="/static/img/RabbitMQ专题/erl_5.png" style="zoom: 67%;" /> 

### **2、RabbitMQ安装**

- 下载网址：https://www.rabbitmq.com/download.html

<img src="/static/img/RabbitMQ专题/rbq_1.png" style="zoom:50%;" /> 

- 下载完后，傻瓜式安装，期间可修改默认的安装位置
- RabbitMQ 安装好后接下来安装 RabbitMQ-Plugins
  - 打开 cmd，进入到 RabbitMQ  的安装位置
  - 输入rabbitmq-plugins enable rabbitmq_management命令进行安装

<img src="/static/img/RabbitMQ专题/rbq_2.png" style="zoom: 67%;" /> 

- 还是在该目录下，执行 rabbitmqctl status，检查 RabbitMQ  是否启动成功

<img src="/static/img/RabbitMQ专题/rbq_3.png" style="zoom: 80%;" /> 

- 访问http://localhost:15672，用户名和密码都是 guest，登录成功后即可看到 RabbitMQ  的相关信息

<img src="/static/img/RabbitMQ专题/rbq_4.png" style="zoom: 50%;" /> 

- 补充几条操作 RabbitMQ  的命令
  - 启插件：rabbitmq-plugins enable rabbitmq_management 
  - 移除服务：rabbitmq-service remove 
  - 安装服务：rabbitmq-service install 
  - 重启服务：rabbitmq-server restart
  - 启用服务：rabbitmq-server start
  - 停止应用程序：rabbitmqctl stop_app
  - 启用运用：rabbitmqctl start_app
  - 重置清除节点的配置(先stop_app，后reset节点)：rabbitmqctl stop_app    rabbitmqctl rest
  - 启用日志插件命令：rabbitmq-plugins enable rabbitmq_tracing
  - 更多命令请参考：https://www.cnblogs.com/potato-chip/p/9977386.html