# RabbitMQ的参数

## 一	应答参数

在消费者从队列中取出数据后，该数据并不会在消息队列中继续保留，而是直接丢弃。那么当消费者处理数据出错，想回头再拿一次数据，这个时候就再也取不到数据了。上述问题可以通过设置应答参数 auto_ack 的值来解决。当 auto_ack 的值为 True，是自动应答，即消费者取出数据，消息队列就把数据丢弃；当 auto_ack 的值为 False，是手动应答，消息队列必须接收到消费者返回的指令后，才能将数据丢弃：

```python
# 消费者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# 创建了一个队列
channel.queue_declare(queue='hello')


# 回调函数，用于操作从队列中取出的内容
def callback(ch, method, properties, body):
    print("消费者拿到数据：Received %r" % body)
    ch.basic_ack(delivery_tag=method.delivery_tag)  # 给队列一个应答，表示我已经处理完数据了，可以丢弃了


# 建立一个监听，此时还没有开始监听
# auto_ack改为False，手动应答
channel.basic_consume(queue='hello', auto_ack=False, on_message_callback=callback)

# 开启监听
channel.start_consuming()
```

## 二	持久化参数

队列中存储的数据也可以从内存中持久化到本地磁盘，这样即使消息队列崩溃或者重启，数据也不会丢失

```python
# 生产者
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('127.0.0.1'))  # 默认端口：15672

# 用于操作消息队列的一个变量/句柄
channel = connection.channel()

# 创建一个可持久化队列
# durable=True
channel.queue_declare(queue='hello', durable=True)

# 往队列里写数据
channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='CDC',
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # 模式2表示持久化存储数据
                      )
                      )

print("生产者放入数据：Sent 'CDC'")
```

```python
# 消费者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# 创建一个队列
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

## 三	分发参数

我们也可以通过设置分发参数，让消息队列从轮询分发数据模式改为公平的模式，即每个消费者都会拿到一模一样的数据，而不是轮询等待获取。想要做到公平分发（fair dispatch），必须关闭自动应答`ack`，改成手动应答。使用 `basicQos(perfetch=1)`限制每次只发送不超过1条消息到同一个消费者，消费者必须手动反馈告知队列，才会发送下一个。

```python
# 消费者

import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

# 创建一个可持久化队列
# durable=True
channel.queue_declare(queue='hello'， durable=True)


# 回调函数，用于操作从队列中取出的内容
def callback(ch, method, properties, body):
    print("消费者拿到数据：Received %r" % body)

# 在消费者中增加一个公平分发的机制
channel.basic_qos(prefetch_count=1)

# 建立一个监听，此时还没有开始监听
channel.basic_consume(queue='hello',
                      auto_ack=False,
                      on_message_callback=callback)

# 开启监听
channel.start_consuming()
```