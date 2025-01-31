# RabbitMQ的工作模式

## 一	简单模式

RabbitMQ 的简单模式就是和普通的生产者消费者模型原理一样，只不过通过 RabbitMQ  可以实现多台服务器进程之间的通信，服务器 A 将数据放入 RabbitMQ 中的某个队列中，服务器 B 不停的去监听该队列，如果有数据进来，那么就取出来处理；如果没有数据，消费者就阻塞等待数据。在 python 中，我们使用第三方库 pika 来操作 RabbitMQ 

<img src="/static/img/RabbitMQ专题/pika_1.png" style="zoom: 67%;" />  

```python
# 生产者
import pika

# 连接 RabbitMq
# 因为此处连接的是本地的，所以直接写回环地址即可，如果是连接其他服务器上的，需要指定 ip+端口
connection = pika.BlockingConnection(pika.ConnectionParameters('127.0.0.1'))  # 默认端口：15672

# 创建用于操作消息队列的一个变量/句柄
channel = connection.channel()

# 创建一个队列，名字 hello
channel.queue_declare(queue='hello')

# 往队列里写数据
"""
exchange：简单模式下，该字段为空即可
routing_key：要放入数据的队列的名字
body：放入的数据的内容
"""
channel.basic_publish(exchange='', routing_key='hello', body='CDC')

print("生产者放入数据：Sent 'CDC'")
```

```python
# 消费者
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# 创建了一个队列
"""
生产者已经创建了一个队列，为什么消费者还要创建一个同名的队列呢？
- 这是因为在实际的应用中，我们无法预知到底是生产者还是消费者会先去连接消息队列，如果是消费者比生产者先执行，先去连接队列，但是队列还未创建，消费者监听某个指定的队列时就会因找不到队列报错。所以为了保险起见，消费者也先去创建一个同名的队列。

- 需要注意的是，当消费者或者生产者检测到已存在要创建的队列时，这句代码就不会执行了
"""
channel.queue_declare(queue='hello')


# 回调函数，用于操作从队列中取出的内容
def callback(ch, method, properties, body):
    print("消费者拿到数据：Received %r" % body)


# 建立一个监听，此时还没有开始监听
channel.basic_consume(queue='hello',
                      auto_ack=True,
                      on_message_callback=callback)

# 开启监听
channel.start_consuming()
```

如果有多个消费者，他们取数据的顺序时怎样的呢？我们可以测试一下：

- 先开启三个消费者，此时队列中没有数据，消费者都在阻塞等待；
- 生产者依次传入 "aaa"，"bbb"，"ccc"，"ddd"，"eee"；
- 此时按先后启动的顺序，三个消费者接收到的数据分别是：
  - 消费者1： "aaa"，"ddd"
  - 消费者2："bbb"，"eee"
  - 消费者3："ccc"

可见，如果存在多个消费者，是按照连接的先后顺序，轮询去取值的。这样的机制在某些场合显然是有缺陷的，比如消费者1对于数据 "aaa" 的处理需要花费一小时，而消费者2花了五秒就把任务处理完了，但是按照轮询机制，消费者2无法继续取值，必须等消费者1处理完当前任务再次从队列中取完值后，消费者2才能继续取值，这显然是对资源极大的浪费。

## 二	交换机模式

交换机模式弥补了上述简单模式中的弊端，它的原理是生产者不再创建队列，而是创建一个交换机。将数据放入交换机中，每一个消费者都会有一个队列去和交换机绑定，消费者只监控自己的队列。 

### **1、发布订阅模式**

所谓的发布订阅模式，就像订阅报纸杂志一样，一旦有新的发布，订阅者就会人手拿到一份。所以一旦交换机中有数据，交换机就会平等的给所有绑定的队列都传一份数据。

<img src="/static/img/RabbitMQ专题/pika_2.png" style="zoom:80%;" /> 

```python
# 生产者
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

# 创建了一个交换机
"""
exchange：交换机的名字
exchange_type：交换机的模式
    - fanout 表示发布订阅模式
"""
channel.exchange_declare(exchange='logs', exchange_type='fanout')

message = "生产者发送数据：Hello World!"

# 往交换机里放数据
"""
exchange：交换机的名字
routing_key：交换机模式下，该字段为空即可
body：要放入的数据
"""
channel.basic_publish(exchange='logs', routing_key='', body=message)

connection.close()
```

```python
# 消费者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

# 先创建一个交换机
# 理由和之前为什么消费者也要创建一个同名的队列一样
channel.exchange_declare(exchange='logs', exchange_type='fanout')

# 消费者创建一个属于自己的队列，但是要保证队列的名字是唯一的
"""
第一个字段是队列名，我们可以自己定义，也可以传空字符串；
传空字符串的时候，exclusive设置为True，此时消息队列会帮我们随机生成一个不重复的名字
"""
result = channel.queue_declare("", exclusive=True)
# 取出 RabbitMQ 为我们创建的随机的名字
queue_name = result.method.queue

# 让队列去绑定交换机
channel.queue_bind(exchange='logs', queue=queue_name)


# 定义回调函数
def callback(ch, method, properties, body):
    print(body.decode("utf-8"))


# 创建监听对象
channel.basic_consume(queue=queue_name, auto_ack=True, on_message_callback=callback)

# 开启监听
channel.start_consuming()
```

我们可以启动三个消费者进行简单测试：

- 先开启三个消费者，此时队列中没有数据，消费者都在阻塞等待；
- 生产者依次传入 "aaa"，"bbb"，"ccc"；
- 此时按先后启动的顺序，三个消费者接收到的数据分别是：
  - 消费者1："aaa"，"bbb"，"ccc"
  - 消费者2："aaa"，"bbb"，"ccc"
  - 消费者3："aaa"，"bbb"，"ccc"

### **2、关键字模式**

发布订阅模式是交换机给所有的绑定的队列都传一份数据，但是大多数情况下，每个队列对应的消费者处理的东西是不一样的，那么有些数据队列就没必要接收，我们可以使用关键字模式来进一步优化。生产者在创建交换机的时候指定模式为关键字模式，在往交换机中传入数据的时候，给数据指定一个关键词；消费者在创建队列的时候，也给队列绑定一个一样的关键词。上述操作完成以后，当生产者往交换机中放入数据时，会根据关键词只把数据传给对应的对列，其他对列将接收不到数据。

<img src="/static/img/RabbitMQ专题/pika_3.png" style="zoom: 67%;" /> 

```python
# 生产者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

# exchange_type='direct' ---> 指定交换机为关键字模式
channel.exchange_declare(exchange='logs2', exchange_type='direct')

message = "生产者: Hello CDC!"

# routing_key 参数指定关键字为
channel.basic_publish(exchange='logs2', routing_key='error', body=message)

connection.close()
```

```python
# 消费者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

# 声明一个名为logs2，模式为关键字模式的交换机
channel.exchange_declare(exchange='logs2', exchange_type='direct')

# 创建队列
result = channel.queue_declare("", exclusive=True)
queue_name = result.method.queue

# 将指定队列绑定到交换机上，给队列指定关键字为error
channel.queue_bind(exchange='logs2', queue=queue_name, routing_key="error")


# 定义回调函数
def callback(ch, method, properties, body):
    print(" [x] %r" % body)


# 建立监听对象
channel.basic_consume(queue=queue_name, auto_ack=True, on_message_callback=callback)

# 开启监听
channel.start_consuming()
```

我们可以启动三个消费者进行简单测试：

- 创建三个消费者，对应的队列分别绑定关键字 "info"，"error"，"debug"

- 先开启三个消费者，此时队列中没有数据，消费者都在阻塞等待；
- 生产者依次传入：
  -  "aaa"，routing_key的值为"info"
  - "bbb"，routing_key的值为"error"
  - "ccc"；routing_key的值为"debug"
- 此时三个消费者接收到的数据分别是：
  - 消费者1："aaa"
  - 消费者2：bbb"
  - 消费者3："ccc"

### **3、通配符模式**

通配符模式本质上和关键字模式没有区别，只是在交换机和队列进行关键字匹配时，可以进行类似正则的规则定制，其中我们主要了解一下两个符号：

- `#` 号：匹配一个或者多个词
- `*`号：只匹配一个词

<img src="/static/img/RabbitMQ专题/pika_4.png" style="zoom:67%;" /> 

```python
# 生产者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

# exchange_type='topic' ---> 指定交换机为通配符模式
channel.exchange_declare(exchange='logs3', exchange_type='topic')

message = "生产者: Hello CDC!"

# routing_key 参数指定关键字为error.logs
channel.basic_publish(exchange='logs3', routing_key='error.logs', body=message)

connection.close()
```

```python
# 消费者
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters(host='localhost'))
channel = connection.channel()

# 声明一个名为logs3，模式为通配符模式的交换机
channel.exchange_declare(exchange='logs3', exchange_type='topic')

# 创建队列
result = channel.queue_declare("", exclusive=True)
queue_name = result.method.queue

# 将指定队列绑定到交换机上，给队列指定通配符为#.logs
channel.queue_bind(exchange='logs3', queue=queue_name, routing_key="#.logs")


# 定义回调函数
def callback(ch, method, properties, body):
    print(" [x] %r" % body)


# 建立监听对象
channel.basic_consume(queue=queue_name, auto_ack=True, on_message_callback=callback)

# 开启监听
channel.start_consuming()
```

我们可以启动两个消费者进行简单测试：

- 创建两个消费者，对应的队列分别绑定通配符 "#.logs"，"error.#"
- 先开启两个消费者，此时队列中没有数据，消费者都在阻塞等待；

- 生产者依次传入：
  -  "aaa"，routing_key的值为"error.logs"
  - "bbb"，routing_key的值为"info.logs"
- 此时两个消费者接收到的数据分别是：
  - 消费者1："aaa"，"bbb"
  - 消费者2："aaa"