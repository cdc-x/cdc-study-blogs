# Redis 操作命令

## 一、连接命令

- **ping**  用于测试 redis 是否连通，连接成功返回 pong

```shell
127.0.0.1:6379> ping
PONG 
```

- **echo**  打印内容，测试时使用

```shell
127.0.0.1:6379> echo "hello world"
"hello world" 
```

- **quit**  客户端退出与服务端的连接

```shell
127.0.0.1:6379> quit
[root@localhost src]# 
```

- **select**  选择数据库，不同库内的数据内容互不影响（键可以重名）。redis 一共有 16 个库，库名为 0 -- 15

```shell
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> get name
"cdc"
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> get name
"cdd"
```

- **auth**  用户认证，主要用于设置了密码的 redis。用户只有通过密码认证才能进行后续操作

```shell
[root@localhost src]# ./redis-cli 
127.0.0.1:6379> keys *
(error) NOAUTH Authentication required.  # 未认证，无权操作

# 通过密码认证
127.0.0.1:6379> auth 123456
OK

127.0.0.1:6379> keys *
1) "aa"
2) "aaa"
3) "a"
```

## 二、键操作命令

- **del**  删除指定的一个或多个 key，删除成功返回被删除的键的个数，对于不存在的键会直接忽略

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> set age 18
OK
127.0.0.1:6379> set address 南京
OK
127.0.0.1:6379> del name age address
(integer) 3
127.0.0.1:6379> del hobby salary  # 不存在的键进行删除也不会报错
(integer) 0
```

- **exists**  判断指定的键是否存在，不存在返回 0，存在返回 1

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> exists name
(integer) 1
127.0.0.1:6379> exists age
(integer) 0
```

- **expire**  给键设置存活时间，单位为秒

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> expire name 10 # 设置10秒后键失效
(integer) 0

# 等待3秒后查询
127.0.0.1:6379> get name
"cdc"

# 等待10秒后查询
127.0.0.1:6379> get name
(nil)
```

- **ttl**  查询指定键的存活时间，返回结果为-2表示键已过期，返回结果为-1表示键永久有效

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> ttl name # 未给键设置超时，键永久有效
(integer) -1

127.0.0.1:6379> expire name 10
(integer) 1

# 10秒内查询键有效时间，返回键的存活时间
127.0.0.1:6379> ttl name
(integer) 9
127.0.0.1:6379> ttl name
(integer) 7
127.0.0.1:6379> ttl name
(integer) 7

# 10秒后查询键有效时间，键已过期
127.0.0.1:6379> ttl name
(integer) -2
```

- **pexpire**   给键设置存活时间，单位为毫秒

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> pexpire name 10000  # 设置超时时间为 10000 毫秒，即 10 秒
(integer) 1
```

- **pttl**  查询指定键的存活时间，单位为毫秒。返回结果为-2表示键已过期，返回结果为-1表示键永久有效

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> pexpire name 10000
(integer) 1

127.0.0.1:6379> pttl name
(integer) 4279
127.0.0.1:6379> pttl name
(integer) 3028

# 10秒后查询
127.0.0.1:6379> pttl name
(integer) -2
```

- **expireat**  以 UNIX 时间戳格式给键设置过期时间

```shell
127.0.0.1:6379> expireat name 1718345968
(integer) 1
```

- **persist**  移除给定键的生存时间，将这个键转换成永久的。当生存时间移除成功时，返回 1，如果键不存在或键没有设置生存时间，返回 0 。

```shell
127.0.0.1:6379[1]> set name cdc
OK
127.0.0.1:6379[1]> expire name 60
(integer) 1

127.0.0.1:6379[1]> ttl name
(integer) 50
127.0.0.1:6379[1]> ttl name
(integer) 47

# 取消设置的存活时间
127.0.0.1:6379[1]> persist name
(integer) 1
127.0.0.1:6379[1]> ttl name
(integer) -1
```

- **keys**  查找所有符合条件的键

```
* 表示多个字符
? 表示一个字符
[] 表示只能是[]里面的字符
\ 表示指定特殊字符
```

```shell
127.0.0.1:6379[1]> set a 123
OK
127.0.0.1:6379[1]> set aa 456
OK
127.0.0.1:6379[1]> set aaa 789
OK
127.0.0.1:6379[1]> set aab 987
OK
127.0.0.1:6379[1]> set a5 258
OK
127.0.0.1:6379[1]> set aacbb 654
OK

# 查询所有键
127.0.0.1:6379[1]> keys *
1) "aa"
2) "aab"
3) "aaa"
4) "a5"
5) "a"
6) "aacbb"

# 查询以b结尾的键
127.0.0.1:6379[1]> keys *b
1) "aab"
2) "aacbb"

# 差选以 a 开头的键
127.0.0.1:6379[1]> keys a*
1) "aa"
2) "aab"
3) "aaa"
4) "a5"
5) "a"
6) "aacbb"

# 查询以a开头，长度为2的键
127.0.0.1:6379[1]> keys a?
1) "aa"
2) "a5"

# 查询以a开头，长度为3的键
127.0.0.1:6379[1]> keys a??
1) "aab"
2) "aaa"

# 查询以数字结尾的键
127.0.0.1:6379[1]> keys *[1-9]
1) "a5"
```

- **move**  移动当前的键到指定的数据库里面，成功返回 1，失败返回 0

```shell
127.0.0.1:6379[1]> set name cdc
OK
127.0.0.1:6379[1]> move name 5
(integer) 1
127.0.0.1:6379[1]> select 5
OK
127.0.0.1:6379[5]> get name
"cdc"
```

- **randomkey**  随机获取一个键，如果数据库为空，则返回空

```shell
127.0.0.1:6379[1]> randomkey
"aaa"
127.0.0.1:6379[1]> randomkey
"aacbb"
127.0.0.1:6379[1]> randomkey
"a"
127.0.0.1:6379[1]> randomkey
"aaa"
127.0.0.1:6379[1]> randomkey
"aacbb"
```

- **rename**  重命名键，如果源键不存在，则报错；如果目标键存在，则覆盖

```shell
127.0.0.1:6379[1]> set name1 cdc
OK
127.0.0.1:6379[1]> set name2 cdd
OK

# aaa 不存在，直接报错
127.0.0.1:6379[1]> rename aaa name
(error) ERR no such key

# 将name1重命名为user
127.0.0.1:6379[1]> rename name1 user
OK
127.0.0.1:6379[1]> keys *
1) "name2"
2) "user"

# 将user重命名为name2，原来的name2会被覆盖
127.0.0.1:6379[1]> rename user name2
OK
127.0.0.1:6379[1]> get name2
"cdc"
```

- **renamenx**  重命名键，如果源键不存在，则报错；如果目标键存在，则不生效

```shell
127.0.0.1:6379[1]> set name1 cdc
OK
127.0.0.1:6379[1]> set name2 cdd
OK
127.0.0.1:6379[1]> renamenx name1 name2
(integer) 0
127.0.0.1:6379[1]> keys *
1) "name2"
2) "name1"
127.0.0.1:6379[1]> get name2
"cdd"
```

- **type**  查询键所存储的值的类型

```shell
127.0.0.1:6379[1]> set name cdc
OK
127.0.0.1:6379[1]> lpush hobby sing dance rap basketball
(integer) 4
127.0.0.1:6379[1]> type name
string
127.0.0.1:6379[1]> type hobby
list
```

- **dump**  用于序列化给的键，并返回序列化的值

```shell
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> dump name
"\x00\x03cdc\t\x00\x9c\x1b\x83\x06\xbe$dn"
```

## 三、服务器命令

- **client**  用于操作连接到 redis 服务器的客户端 

```shell
# client pause 用于阻塞客户端命令一段时间，单位为毫秒
127.0.0.1:6379> client pause 10000
OK
127.0.0.1:6379> set name cdc
OK
(9.30s)


# client list 获取连接到服务器的客户端连接列表
127.0.0.1:6379> client list
id=4 addr=127.0.0.1:42778 fd=8 name= age=1526 idle=0 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=26 qbuf-free=32742 obl=0 oll=0 omem=0 events=r cmd=client
id=5 addr=192.168.121.130:54342 fd=9 name= age=9 idle=9 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=0 qbuf-free=0 obl=0 oll=0 omem=0 events=r cmd=command

# client setname 设置当前连接的名称
127.0.0.1:6379> client setname connect_1
OK

# client getname 获取当前连接的名称
127.0.0.1:6379> client getname
"connect_1"

# client kill 关闭指定的客户端连接
127.0.0.1:6379> client list
id=5 addr=192.168.121.130:54342 fd=9 name= age=466 idle=466 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=0 qbuf-free=0 obl=0 oll=0 omem=0 events=r cmd=command
id=6 addr=127.0.0.1:42780 fd=8 name= age=44 idle=0 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=26 qbuf-free=32742 obl=0 oll=0 omem=0 events=r cmd=client

127.0.0.1:6379> client kill 192.168.121.130:54342
OK

127.0.0.1:6379> client list
id=6 addr=127.0.0.1:42780 fd=8 name= age=70 idle=0 flags=N db=0 sub=0 psub=0 multi=-1 qbuf=26 qbuf-free=32742 obl=0 oll=0 omem=0 events=r cmd=client
```

- **数据库删除命令**

```shell
# flushdb 删除当前数据库的所有键
127.0.0.1:6379> keys *
1) "address"
2) "name"
3) "user"
4) "age"
127.0.0.1:6379> flushdb
OK
127.0.0.1:6379> keys *
(empty list or set)


# flushall 删除所有数据库的所有键
# 当前在数据库0
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> set age 18
OK
127.0.0.1:6379> keys *
1) "name"
2) "age"

# 切换到数据库1
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> set a aaaa
OK
127.0.0.1:6379[1]> set b bbbb
OK
127.0.0.1:6379[1]> keys *
1) "a"
2) "b"

# 删除所有库的所有key
127.0.0.1:6379[1]> flushall
OK
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> keys *
(empty list or set)
```

- **config**  用于操作 redis 相关配置项的值，只对当前生效，一旦 redis 服务重启则失效

```shell
# config get 查询指定配置项的值
127.0.0.1:6379> config get loglevel
1) "loglevel"
2) "notice"

# config set 设置指定配置项的值
127.0.0.1:6379> config set loglevel debug
OK
127.0.0.1:6379> config get loglevel
1) "loglevel"
2) "debug"


# config resetstat 重置 info 命令中的某些统计数据，包括：键空间命中次数、键空间不命中次数、执行命令的次数、连接服务器的次数、过期key的数量、被拒绝的连接数量、最后执行 fork(2) 的时间、aof_delayed_fsync计数器的值
127.0.0.1:6379> config resetstat
OK


# config rewrite 将通过 config set 修改过的配置项同步写入到配置文件中
127.0.0.1:6379> config get loglevel
1) "loglevel"
2) "notice"
127.0.0.1:6379> config set loglevel debug
OK
127.0.0.1:6379> config rewrite
OK
```

- **command**  用于获取 redis 命令详情相关信息

```shell
# command 返回所有的Redis命令的详细信息，以数组形式展示
127.0.0.1:6379> command
...
52) 1) "lastsave"
     2) (integer) 1
     3) 1) random
        2) fast
     4) (integer) 0
     5) (integer) 0
     6) (integer) 0
 53) 1) "ttl"
     2) (integer) 2
     3) 1) readonly
        2) random
        3) fast
     4) (integer) 1
     5) (integer) 1
...

# command info 获取指定命令详细信息
127.0.0.1:6379> command info hset
1) 1) "hset"
   2) (integer) -4
   3) 1) write
      2) denyoom
      3) fast
   4) (integer) 1
   5) (integer) 1
   6) (integer) 1


# 获取给定命令的所有键
127.0.0.1:6379> command getkeys set name cdc
1) "name"
127.0.0.1:6379> command getkeys hset user name cdc age 18
1) "user"


# command count  获取 redis 命令总数
127.0.0.1:6379> command count
(integer) 200
```

- **数据持久化命令**

```shell
# save 执行一个同步保存操作，将当前 redis 实例的所有数据快照（snapshot）以 RDB 文件的形式保存到硬盘（同步执行，数据过大会阻塞服务）
127.0.0.1:6379> save
OK

# bgsave 执行一个同步保存操作，将当前 redis 实例的所有数据快照（snapshot）以 RDB 文件的形式保存到硬盘（异步执行，不阻塞服务）
127.0.0.1:6379> bgsave
Background saving started

# lastsave 返回最近一次 Redis 成功将数据保存到磁盘上的时间，以 UNIX 时间戳格式表示
127.0.0.1:6379> lastsave
(integer) 1719390254

# 异步执行一个 AOF（AppendOnly File） 文件重写操作
127.0.0.1:6379> Bgrewriteaof
Background append only file rewriting started
```

- **主从配置命令**

```shell
# slaveof host port 将当前服务器转变为指定服务器的从属服务器
127.0.0.1:6379> slaveof 192.168.121.132 6379
OK

# role 返回主从实例所属的角色
127.0.0.1:6379[1]> role
1) "slave"
2) "192.168.121.132"
3) (integer) 6379
4) "connect"
5) (integer) -1


# slaveof no one 接触从主关系
127.0.0.1:6379[1]> slaveof no one
OK
```

- **info**  用于获取服务器的信息

```shell
# 查询服务器所有信息
127.0.0.1:6379> info
# Server
redis_version:5.0.4
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:e603ce1d4a058d72
redis_mode:standalone
os:Linux 3.10.0-1160.el7.x86_64 x86_64
arch_bits:64
multiplexing_api:epoll
......

# 查询指定的信息
127.0.0.1:6379> info cpu
# CPU
used_cpu_sys:2.015769
used_cpu_user:2.575955
used_cpu_sys_children:0.003982
used_cpu_user_children:0.000995

127.0.0.1:6379> info memory
# Memory
used_memory:1859496
used_memory_human:1.77M
used_memory_rss:4698112
used_memory_rss_human:4.48M
used_memory_peak:1860728
......
```

- **time**  返回当前服务器时间

```shell
127.0.0.1:6379> time
1) "1719390441"
2) "469267"
```

- **monitor**  实时打印出 redis 服务器接收到的命令（调试时使用）

```shell
127.0.0.1:6379> monitor
OK
1719390580.724698 [0 192.168.121.130:55728] "keys" "*"
1719390588.266864 [0 192.168.121.130:55728] "set" "age" "18"
```

- **dbsize**  返回当前数据库的键的数量

```shell
127.0.0.1:6379> dbsize
(integer) 4
```

- **debug object**  获取指定键的调试信息

```shell
127.0.0.1:6379> debug object name
Value at:0x7fd95c0285a0 refcount:1 encoding:embstr serializedlength:4 lru:8113817 lru_seconds_idle:930
127.0.0.1:6379> debug object user
Value at:0x7fd95c0b95a0 refcount:1 encoding:ziplist serializedlength:31 lru:8113817 lru_seconds_idle:933
```

- **slowlog**  检查运行缓慢的查询。 slowlog 将会记录运行时间超过 Y 微秒的最后 X 条查询（X 和 Y 可以在 redis.conf 中配置）

```shell
# 默认最大时间为 10000 微秒，存储的最大条数为 128 条
127.0.0.1:6379> config get slowlog-log-slower-than
1) "slowlog-log-slower-than"
2) "10000"
127.0.0.1:6379> config get slowlog-max-len
1) "slowlog-max-len"
2) "128"

# 为方便测试，将超时时间设置成 5 微秒
127.0.0.1:6379> config set slowlog-log-slower-than 5
OK

# 执行一些命令
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> lpush student xiaoming xiaohong xiaolan xiaolv
(integer) 4
127.0.0.1:6379> hmset user name cdc age 18 gender male address nanjing
OK

# slowlog get 查询指定条数的超时记录
127.0.0.1:6379> slowlog get 2
1) 1) (integer) 1
   2) (integer) 1719391591
   3) (integer) 14
   4)  1) "hmset"
       2) "user"
       3) "name"
       4) "cdc"
       5) "age"
       6) "18"
       7) "gender"
       8) "male"
       9) "address"
      10) "nanjing"
   5) "127.0.0.1:42784"
   6) ""
2) 1) (integer) 0
   2) (integer) 1719391555
   3) (integer) 18
   4) 1) "lpush"
      2) "student"
      3) "xiaoming"
      4) "xiaohong"
      5) "xiaolan"
      6) "xiaolv"
   5) "127.0.0.1:42784"
   6) ""


# 查看存储的超时记录条数
127.0.0.1:6379> slowlog len
(integer) 4

# 清空记录
127.0.0.1:6379> slowlog reset
OK
```

- **shutdown**  异步保存数据到硬盘，并关闭服务器

```shell
127.0.0.1:6379[1]> shutdown
not connected> keys *
Could not connect to Redis at 127.0.0.1:6379: Connection refused
```

## 四、事务命令

- **multi**  标记事务的开始

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name cdc
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> ping
QUEUED
```

- **exec**  执行事务中所有的指令

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name cdc
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> ping
QUEUED
127.0.0.1:6379> exec
1) OK
2) "cdc"
3) PONG
```

- **discard**  取消事务，放弃执行事务块内所有命令

```shell
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set name cdc
QUEUED
127.0.0.1:6379> set age 18
QUEUED
127.0.0.1:6379> get name
QUEUED
127.0.0.1:6379> get age
QUEUED
127.0.0.1:6379> discard
OK
```

- **watch**  监视一个(或多个) 键，如果在事务执行之前这个(或这些) 键被其他命令所改动，那么事务将被打断。

```shell
# 在 192.168.121.132 上设置事务监听
127.0.0.1:6379> set views 100
OK
127.0.0.1:6379> watch views
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> incr views
QUEUED
127.0.0.1:6379> incr views

# 在另一台服务器上连接 192.168.121.132 上的 redis，修改 views 的值
192.168.121.132:6379> set views 500
OK

# 返回 192.168.121.132 执行事务，执行过程中断
127.0.0.1:6379> exec
(nil)
```

- **unwatch **  取消对所有键的监视

```shell
127.0.0.1:6379> set views 100
OK
127.0.0.1:6379> watch views
OK
127.0.0.1:6379> unwatch
OK
```

## 五、发布订阅命令

- **subscribe**  订阅一个或多个频道

```shell
127.0.0.1:6379> subscribe ch1 ch2 ch3
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "ch1"
3) (integer) 1
1) "subscribe"
2) "ch2"
3) (integer) 2
1) "subscribe"
2) "ch3"
3) (integer) 3
```

- **unsubscribe**  取消订阅一个或多个频道

```shell
127.0.0.1:6379> unsubscribe ch1
unsubscribe
ch1
0
```

- **publish**  向指定的频道发送信息，订阅了该频道的客户端可以接收到信息

```shell
# 向频道 ch1 发送信息
127.0.0.1:6379> publish ch1 hellp
(integer) 1
127.0.0.1:6379> publish ch1 hello
(integer) 1

# 订阅者接收到了信息
127.0.0.1:6379> subscribe ch1
Reading messages... (press Ctrl-C to quit)
1) "subscribe"
2) "ch1"
3) (integer) 1
1) "message"
2) "ch1"
3) "hellp"
1) "message"
2) "ch1"
3) "hello"
```

- **psubscribe **  订阅一个或多个指定模式的频道（支持通配符）

```shell
# 订阅了以 it_ 和 cdc_ 开头的所有频道
127.0.0.1:6379> psubscribe it_* cdc_*
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "it_*"
3) (integer) 1
1) "psubscribe"
2) "cdc_*"
3) (integer) 2

# 向不同的频道发送信息
127.0.0.1:6379> publish cdc_123 hello
(integer) 1
127.0.0.1:6379> publish cdc123 hahahha
(integer) 0
127.0.0.1:6379> publish it_abcd ssss
(integer) 1
127.0.0.1:6379> publish itabcd fffff
(integer) 0

# 订阅者接收信息情况
[root@localhost redis-5.0.4]# ./src/redis-cli 
127.0.0.1:6379> psubscribe it_* cdc_*
Reading messages... (press Ctrl-C to quit)
1) "psubscribe"
2) "it_*"
3) (integer) 1
1) "psubscribe"
2) "cdc_*"
3) (integer) 2
1) "pmessage"
2) "cdc_*"
3) "cdc_123"
4) "hello"
1) "pmessage"
2) "it_*"
3) "it_abcd"
4) "ssss"
```

- **punsubscribe**  退订所有给定模式的频道

```shell
127.0.0.1:6379> Punsubscribe it_*
1) "punsubscribe"
2) "it_*"
3) (integer) 0
```

- **pubsub **  查看发布订阅系统相关信息

```shell
# pubsub channels 查看当前活跃的频道
127.0.0.1:6379> pubsub channels
1) "ch2"
2) "ch1"

# pubsub numsub 查询指定的频道有多少人订阅
127.0.0.1:6379> pubsub numsub ch1
1) "ch1"
2) (integer) 2

# pubsub numpat 查询匹配模式频道有多少人订阅
127.0.0.1:6379> pubsub numpat
(integer) 2
```

