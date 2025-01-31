# Redis 数据类型

## 一、数据类型简介

redis 是以 `key-value` 形式对数据进行存储，key 都是字符串类型，且在一个库中只能唯一；value 可以是以下 5 中类型

| 类型                        | 简介                                                         | 特性                                                         | 场景                                                         |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| string                      | 二进制安全                                                   | 可以包含任何数据，比如 jpg 图片或者序列化对象，一个键最大能存储 512 MB |                                                              |
| list                        | 链表（双向链表），它的子成员类型为 string 类型               | 增删快，提供了操作某一段元素的 API                           | 1）最新消息排行，如朋友圈发送的时间线；<br/>2）消息队列      |
| hash                        | 键值对集合。key、域、值的类型都为string，且域在同一个hash中是唯一的。<br/>key: {<br/>            域（属性）: 值，<br/>            域:值，            <br/>            域:值，<br/>            域:值，<br/>            ...<br/>		} | 可以存储对象，并且支持向数据库中的对象新增或修改某项属性     | 存储、读取、修改用户属性                                     |
| set                         | 哈希表实现，元素不重复，它的子成员类型为 string 类型，没有修改操作。 | 1）添加、删除、查找的复杂度都是 O(1) ；<br/>2）为集合提供了求差集、并集、交集等操作 | 1）共同好友；<br/>2）利用唯一性，统计访问网站的所有独立 IP；<br/>3）好友推荐时，根据某个标签求交集，交集大于指定阈值时，就可以推荐 |
| zset（Sorted Set 有序集合） | 将 set 中的元素添加一个权重参数，元素根据权重进行有序排列，它的子成员值的类型为 string类型，没有修改操作。 | 数据插入集合时，已经进行天然排序，权重值从小到大排列         | 1）排行榜；<br/>2）带权重的消息队列                          |

## 二、数据类型操作指令

### 1. string 类型

- **set**  设置键值，如果 key 存在则覆盖原来 key 的值，如果不存在则新增一个 key

```shell
# 语法：set 键 值 
127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> set age 18
OK

# 可接收的参数
# ex：设置键的存活时间，单位是秒
127.0.0.1:6379> set user cdc ex 3
OK

# px：设置键的存活时间，单位是毫秒
127.0.0.1:6379> set user cdc px 5000
OK

# nx：如果键不存在，则新建；如果键存在，则不做任何操作
127.0.0.1:6379> set user cdd nx
OK
127.0.0.1:6379> get user
"cdd"

127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> set name aaaaa nx
(nil)
127.0.0.1:6379> get name
"cdc"

# xx：只有键存在才能操作
127.0.0.1:6379> set name cdd xx
OK
127.0.0.1:6379> get name
"cdd"
127.0.0.1:6379> set xxxxx abcde xx
(nil)
```

- **setnx**  作用等价于 `set 键  值  nx`

```shell
# 语法：setnx 键 值

127.0.0.1:6379> set name cdc
OK
127.0.0.1:6379> setnx name cccccc
(integer) 0
127.0.0.1:6379> get name
"cdc"
```

- **setex**  作用等价于 `set 键  值  ex 秒`

```shell
# 语法：setex 键 秒 值

127.0.0.1:6379> setex user 3 aaaaa
OK
127.0.0.1:6379> get user
"aaaaa"
127.0.0.1:6379> get user
(nil)
```

- **get**  用于获取键对应的值，只能获取一个键。如果键存在，则返回键对应的值；如果键不存在，则返回空

```shell
127.0.0.1:6379[1]> set name cdc
OK
127.0.0.1:6379[1]> get name
"cdc"
127.0.0.1:6379[1]> get age
(nil)
```

- **mset**  批量设置键值对，会覆盖已存在的键

```shell
# 语法：mset 键1 值1 键2 值2 ...

127.0.0.1:6379[1]> set name cdc
OK
127.0.0.1:6379[1]> mset name aaaaa age 18 address nanjing
OK
127.0.0.1:6379[1]> keys *
1) "age"
2) "address"
3) "name"

# 原来的name被覆盖
127.0.0.1:6379[1]> get name
"aaaaa"
```

- **mget**  批量获取键对应的值，如果键不存在，则返回空

```shell
# 语法：mget 键1 键2 键3 ...

127.0.0.1:6379[1]> mget name age address hobby
1) "aaaaa"
2) "18"
3) "nanjing"
4) (nil)
```

- **getset**  给指定的键设置新的值，并返回原来的值，如果键不存在，则返回空

```shell
127.0.0.1:6379[1]> set name cdc
OK
127.0.0.1:6379[1]> getset name aaaaaa
"cdc"
127.0.0.1:6379[1]> getset 123456 abcdefg
(nil)
```

- **strlen**  返回指定键对应值的长度，如果键不存在，返回 0

```shell
127.0.0.1:6379[1]> set phone 12345678901
OK
127.0.0.1:6379[1]> strlen phone
(integer) 11
```

- **append**  向指定键的值追加内容，如果键不存在，则新建键

```shell
127.0.0.1:6379[1]> set a hello
OK
127.0.0.1:6379[1]> append a world
(integer) 10
127.0.0.1:6379[1]> get a
"helloworld"
127.0.0.1:6379[1]> append b hahahaha
(integer) 8
127.0.0.1:6379[1]> get b
"hahahaha"
```

- **incr**  对值进行加 1 操作（只能操作值是数字的键）

```shell
127.0.0.1:6379[1]> set num 1
OK
127.0.0.1:6379[1]> incr num
(integer) 2
127.0.0.1:6379[1]> incr num
(integer) 3
127.0.0.1:6379[1]> incr num
(integer) 4
127.0.0.1:6379[1]> get num
"4"
```

- **decr**  对值进行减 1 操作（只能操作值是数字的键）

```shell
127.0.0.1:6379[1]> set num 100
OK
127.0.0.1:6379[1]> decr num
(integer) 99
127.0.0.1:6379[1]> decr num
(integer) 98
127.0.0.1:6379[1]> decr num
(integer) 97
127.0.0.1:6379[1]> get num
"97"

127.0.0.1:6379[1]> set num 0
OK
127.0.0.1:6379[1]> decr num
(integer) -1
127.0.0.1:6379[1]> decr num
(integer) -2
127.0.0.1:6379[1]> get num
"-2"
```

- **incrby**  对值增加指定的数（只能操作值是数字的键）

```shell
# 语法：incrby 键1 数字

127.0.0.1:6379[1]> set num 1
OK
127.0.0.1:6379[1]> incrby num 10
(integer) 11
127.0.0.1:6379[1]> get num
"11"
```

- **decrby**  对值减少指定的数（只能操作值是数字的键）

 ```shell
 127.0.0.1:6379[1]> set num 10
 OK
 127.0.0.1:6379[1]> decrby num 5
 (integer) 5
 127.0.0.1:6379[1]> get num
 "5"
 ```

- **incrbyfloat**   对值增加指定的浮点数（只能操作值是数字的键），默认保留小数点后17位

```shell
127.0.0.1:6379[1]> set num 1
OK
127.0.0.1:6379[1]> incrbyfloat num 1.111
"2.111"
127.0.0.1:6379[1]> get num
"2.111"
```

- **getrange**  对值进行切片（左右都是闭区间）

```shell
127.0.0.1:6379[1]> set s abcdefghijklmn
OK
127.0.0.1:6379[1]> getrange s 1 5
"bcdef"
127.0.0.1:6379[1]> getrange s -3 -1
"lmn"
```

- **setbit**  设置一个bit数据的某一位的值

```shell
# 语法：setbit 键 位置索引 值(0 or 1)

127.0.0.1:6379[1]> setbit mybit 7 1  # 设置bit数据的索引第7位为1，即 0000001
(integer) 0
127.0.0.1:6379[1]> get mybit
"\x01"

127.0.0.1:6379[1]> setbit mybit 6 1  # 设置bit数据的索引第6位为1，即 0000011
(integer) 0
127.0.0.1:6379[1]> get mybit
"\x03"

127.0.0.1:6379[1]> setbit mybit 7 0  # 设置bit数据的索引第6位为1，即 0000010
(integer) 1
127.0.0.1:6379[1]> get mybit
"\x02"
```

- **getbit**  获取一个bit数据的某一位的值

```shell
127.0.0.1:6379[1]> getbit mybit 6
(integer) 1
127.0.0.1:6379[1]> getbit mybit 5
(integer) 0
```

- **bitcount**  统计bit数据中被设置为1的位数

```shell
127.0.0.1:6379[1]> setbit mybit2 7 1
(integer) 0
127.0.0.1:6379[1]> setbit mybit2 5 1
(integer) 0
127.0.0.1:6379[1]> setbit mybit2 3 1
(integer) 0
127.0.0.1:6379[1]> bitcount mybit2
(integer) 3
```

- **bitpos**  查询bit数据里面第一个被设置为1或者0的位置

```shell
127.0.0.1:6379[1]> bitpos mybit2 0
(integer) 0
127.0.0.1:6379[1]> bitpos mybit2 1
(integer) 3
```

### 2. list 类型

通过列表操作可以实现以下功能：

- lpush + lpop = Stack（栈）
- lpush + rpop = Queue（队列）
- lpush + ltrim = Capped Collection（有限集合）
- lpush + brpop = Message Queue（消息队列）

常用指令如下：

- **lpush**  将一个或多个元素插入到列表头部，如果键不存在，则新建（最先插入的元素在列表尾部）

```shell
# 语法：lpush 键 值1 值2 值3 ...

127.0.0.1:6379[1]> lpush mylist a b c d e f g
(integer) 7
```

- **lrange**  查询列表中所有元素

```shell
# 语法：lrange 键 开始索引 结束索引 （闭区间）

# 查看全部
127.0.0.1:6379[1]> lrange mylist 0 -1
1) "g"
2) "f"
3) "e"
4) "d"
5) "c"
6) "b"
7) "a"

127.0.0.1:6379[1]> lrange mylist 0 3
1) "g"
2) "f"
3) "e"
4) "d"
```

- **lpop**  移除并返回列表的第一个元素

```shell
127.0.0.1:6379[1]> lpop mylist
"g"
```

- **blpop**   当给定多个列表时，按给定键的先后顺序依次检查各个列表，弹出第一个非空列表的头元素

```shell
# 语法：blpop 键1 键2 ... 超时时间

127.0.0.1:6379[1]> lpush alist A B C D
(integer) 4
127.0.0.1:6379[1]> lpush blist a b c d
(integer) 4
127.0.0.1:6379[1]> lrange alist 0 -1
1) "D"
2) "C"
3) "B"
4) "A"
127.0.0.1:6379[1]> lrange blist 0 -1
1) "d"
2) "c"
3) "b"
4) "a"

# 按照顺序检查给定的列表
# 发现 alist 存在且非空，将 alist 的头部元素弹出
127.0.0.1:6379[1]> blpop alist blist 0
1) "alist"
2) "D"
127.0.0.1:6379[1]> lrange alist 0 -1
1) "C"
2) "B"
3) "A"
127.0.0.1:6379[1]> lrange blist 0 -1
1) "d"
2) "c"
3) "b"
4) "a"
```

- **rpush**  将一个或多个元素插入到列表尾部，如果键不存在，则新建（最先插入的元素在列表头部）

```shell
127.0.0.1:6379[1]> rpush mylist2 a b c d e f g
(integer) 7
127.0.0.1:6379[1]> lrange mylist2 0 -1
1) "a"
2) "b"
3) "c"
4) "d"
5) "e"
6) "f"
7) "g"
```

- **rpop**  移除并返回列表的最后一个元素

```shell
127.0.0.1:6379[1]> rpop mylist2
"g"
```

- **brpop**  当给定多个列表时，按给定键的先后顺序依次检查各个列表，弹出第一个非空列表的尾元素

```shell
127.0.0.1:6379[1]> brpop alist blist 0
1) "alist"
2) "A"
127.0.0.1:6379[1]> lrange alist 0 -1
1) "C"
2) "B"
127.0.0.1:6379[1]> lrange blist 0 -1
1) "d"
2) "c"
3) "b"
4) "a"
```

- **lpushx**  将一个或多个元素插入到列表头部，只能操作已存在的键

```shell
127.0.0.1:6379[1]> lpushx mylist cdc cdd
(integer) 7
127.0.0.1:6379[1]> lrange mylist 0 -1
1) "cdd"
2) "cdc"
3) "f"
4) "e"
5) "d"
6) "c"
7) "b"

# newlist 不存在，此时操作不生效
127.0.0.1:6379[1]> lpushx newlist cdc cdd
(integer) 0
```

- **rpushx**  将一个或多个元素插入到列表尾部，只能操作已存在的键

```shell
127.0.0.1:6379[1]> rpushx mylist aaaa bbbb cccc
(integer) 10
127.0.0.1:6379[1]> lrange mylist 0 -1
 1) "cdd"
 2) "cdc"
 3) "f"
 4) "e"
 5) "d"
 6) "c"
 7) "b"
 8) "aaaa"
 9) "bbbb"
10) "cccc"

127.0.0.1:6379[1]> rpushx newlist aaa bbb ccc
(integer) 0
```

- **lindex**  从表头开始查询指定索引位置的元素的值

```shell
# 语法：lindex 键 索引值

127.0.0.1:6379[1]> lindex mylist 3
"e"
127.0.0.1:6379[1]> lindex mylist 1
"cdc"
```

- **linsert**  将新值插入到指定元素的前面或者后面，如果指定的元素不存在，则不操作

```shell
# 语法：linsert  键  before|after  元素  值

127.0.0.1:6379[1]> linsert mylist before cdc hehehehe
(integer) 11
127.0.0.1:6379[1]> lrange mylist 0 -1
 1) "cdd"
 2) "hehehehe"
 3) "cdc"
 4) "f"
 5) "e"
 6) "d"
 7) "c"
 8) "b"
 9) "aaaa"
10) "bbbb"
11) "cccc"

127.0.0.1:6379[1]> linsert mylist after cdc hahahaha
(integer) 12
127.0.0.1:6379[1]> lrange mylist 0 -1
 1) "cdd"
 2) "hehehehe"
 3) "cdc"
 4) "hahahaha"
 5) "f"
 6) "e"
 7) "d"
 8) "c"
 9) "b"
10) "aaaa"
11) "bbbb"
12) "cccc"
```

- **llen**   获取列表长度

```shell
127.0.0.1:6379[1]> llen mylist
(integer) 12
```

- **lrem**  删除列表中指定的元素

```shell
# 语法：lrem 键 count 元素
# 如果 count > 0 从头往尾的方向查找，删除count个元素
# 如果 count < 0 从尾往头的方向查找，删除count个元素
# 如果 count = 0 删除所有指定的元素

127.0.0.1:6379[1]> lpush mylist3 a a a b b a a a
(integer) 8
127.0.0.1:6379[1]> lrange mylist3 0 -1
1) "a"
2) "a"
3) "a"
4) "b"
5) "b"
6) "a"
7) "a"
8) "a"

# 从头到尾删除前两个a
127.0.0.1:6379[1]> lrem mylist3 2 a
(integer) 2
127.0.0.1:6379[1]> lrange mylist3 0 -1
1) "a"
2) "b"
3) "b"
4) "a"
5) "a"
6) "a"

# 从尾到头删除前两个a
127.0.0.1:6379[1]> lrem mylist3 -2 a
(integer) 2
127.0.0.1:6379[1]> lrange mylist3 0 -1
1) "a"
2) "b"
3) "b"
4) "a"

# 删除所有的b
127.0.0.1:6379[1]> lrem mylist3 0 b
(integer) 2
127.0.0.1:6379[1]> lrange mylist3 0 -1
1) "a"
2) "a"
```

- **lset**  替换指定索引位置的值，如果索引超出范围则报错

```shell
# 语法：lset 键 索引 值

127.0.0.1:6379[1]> lpush mylist4 cdc cdd hello world
(integer) 4
127.0.0.1:6379[1]> lrange mylist4 0 -1
1) "world"
2) "hello"
3) "cdd"
4) "cdc"


127.0.0.1:6379[1]> lset mylist4 1 qwerty
OK
127.0.0.1:6379[1]> lrange mylist4 0 -1
1) "world"
2) "qwerty"
3) "cdd"
4) "cdc"
```

- **ltrim**  对列表进行切片（索引左右都是闭区间）

```shell
127.0.0.1:6379[1]> ltrim mylist4 0 2
OK
127.0.0.1:6379[1]> lrange mylist4 0 -1
1) "world"
2) "qwerty"
3) "cdd"
```

### 3. hash 类型 

- **hset**  给键增加 `field-value` 值

```shell
# 语法：hset 键 字段  值
127.0.0.1:6379[2]> hset userInfo name cdc
(integer) 1
127.0.0.1:6379[2]> hset userInfo age 18
(integer) 1
127.0.0.1:6379[2]> hset userInfo address nanjing
(integer) 1
```

- **hlen**  获取键中键值对的长度

```shell
127.0.0.1:6379[2]> hlen userInfo
(integer) 3
```

- **hget**  获取键中指定字段对应的值

```shell
127.0.0.1:6379[2]> hget userInfo name
"cdc"
127.0.0.1:6379[2]> hget userInfo age
"18"
127.0.0.1:6379[2]> hget userInfo address
"nanjing"
```

- **hgetall**  获取键中所有的键值对

```shell
127.0.0.1:6379[2]> hgetall userInfo
1) "name"
2) "cdc"
3) "age"
4) "18"
5) "address"
6) "nanjing"
```

- **hmset**  批量增加键值对

```shell
127.0.0.1:6379[2]> hmset userInfo2 name cdc age 18 address nanjing
OK
```

- **hmget**  根据字段批量获取值

```shell
127.0.0.1:6379[2]> hmget userInfo2 name age address
1) "cdc"
2) "18"
3) "nanjing"
```

- **hsetnx**   给键增加字段和值，如果字段已存在则无效，不存在则增加

```shell
127.0.0.1:6379[2]> hsetnx userInfo hobby sleep
(integer) 1
127.0.0.1:6379[2]> hsetnx userInfo name cdd
(integer) 0
127.0.0.1:6379[2]> hgetall userInfo
1) "name"
2) "cdc"
3) "age"
4) "18"
5) "address"
6) "nanjing"
7) "hobby"
8) "sleep"
```

- **hkeys**   获取键中所有的字段

```shell
127.0.0.1:6379[2]> hkeys userInfo
1) "name"
2) "age"
3) "address"
4) "hobby"
```

- **hvals**  获取键中所有字段对应的值

```shell
127.0.0.1:6379[2]> hvals userInfo
1) "cdc"
2) "18"
3) "nanjing"
4) "sleep"
```

- **hdel**  删除键中一个或多个字段

```shell
127.0.0.1:6379[2]> hdel userInfo name
(integer) 1
127.0.0.1:6379[2]> hdel userInfo age hobby
(integer) 2
127.0.0.1:6379[2]> hgetall userInfo
1) "address"
2) "nanjing"
```

- **hexists**  判断键中是否存在某个字段，存在返回 1，不存在返回 0

```shell
127.0.0.1:6379[2]> hexists userInfo address
(integer) 1
127.0.0.1:6379[2]> hexists userInfo name
(integer) 0
```

- **hincrby**  给键中的字段的值增加指定的数值，只限数字

```shell
# 语法：hincrby  键  field 数值

127.0.0.1:6379[2]> hset userInfo age 18
(integer) 1
127.0.0.1:6379[2]> hincrby userInfo age 10
(integer) 28
```

- **hincrbyfloat**  给键中的字段的值增加指定的浮点数，只限数字

```shell
127.0.0.1:6379[2]> hincrbyfloat userInfo age 10.11
"38.11"
```

### 4. set 类型

- **sadd**  向键中添加元素，如果元素已存在则不操作，不存在则新增

```shell
# 语法：sadd 键 值1 值2 值3 ...

127.0.0.1:6379[3]> sadd class1 aaa bbb ccc ddd
(integer) 4
```

- **smembers**  查询集合中所有元素

```shell
127.0.0.1:6379[3]> smembers class1
1) "ddd"
2) "aaa"
3) "ccc"
4) "bbb"
```

- **scard**   获取集合中元素的个数

```shell
127.0.0.1:6379[3]> scard class1
(integer) 4
```

- **sdiff**  求两个集合的差集

```shell
127.0.0.1:6379[3]> sadd class2 ccc ddd eee fff
(integer) 4
127.0.0.1:6379[3]> sdiff class1 class2
1) "aaa"
2) "bbb"
127.0.0.1:6379[3]> sdiff class2 class1
1) "eee"
2) "fff"
```

- **sdiffstore**   求两个集合的差集，并把差集保存到一个新的集合中，如果新集合已存在则被覆盖

```shell
127.0.0.1:6379[3]> sdiffstore diffset class1 class2
(integer) 2
127.0.0.1:6379[3]> smembers diffset
1) "aaa"
2) "bbb"
```

- **sinter**  求两个集合的交集

```shell
127.0.0.1:6379[3]> sinter class1 class2
1) "ddd"
2) "ccc"
```

- **sinterstore**   求两个集合的交集，并把交集保存到一个新的集合中，如果新集合已存在则被覆盖

```shell
127.0.0.1:6379[3]> sinterstore interset class1 class2
(integer) 2
127.0.0.1:6379[3]> smembers interset
1) "ddd"
2) "ccc"
```

- **sunion**  求两个集合的并集

```shell
127.0.0.1:6379[3]> sunion class1 class2
1) "bbb"
2) "ddd"
3) "aaa"
4) "eee"
5) "fff"
6) "ccc"
```

- **sunionstore**   求两个集合的并集，并把并集保存到一个新的集合中，如果新集合已存在则被覆盖

```shell
127.0.0.1:6379[3]> sunionstore unionset class1 class2
(integer) 6
127.0.0.1:6379[3]> smembers unionset
1) "bbb"
2) "aaa"
3) "ddd"
4) "eee"
5) "fff"
6) "ccc"
```

- **sismember**  判断指定元素是否属于指定的集合，属于返回 1，不属于返回 0

```shell
127.0.0.1:6379[3]> sismember class1 aaa
(integer) 1
127.0.0.1:6379[3]> sismember class2 aaa
(integer) 0
```

- **smove**  将指定元素从一个集合移动到另一个集合中，如果源集合不存在则不操作，存在则正常移动；如果目标集合不存在则新增一个集合并移入元素，存在则正常移入

```shell
# 语法：smove 源键 目标键 值

127.0.0.1:6379[3]> smembers class1
1) "ddd"
2) "aaa"
3) "ccc"
4) "bbb"
127.0.0.1:6379[3]> smembers class2
1) "ddd"
2) "eee"
3) "fff"
4) "ccc"

127.0.0.1:6379[3]> smove class1 class2 aaa
(integer) 1

127.0.0.1:6379[3]> smembers class1
1) "ddd"
2) "ccc"
3) "bbb"
127.0.0.1:6379[3]> smembers class2
1) "ddd"
2) "eee"
3) "fff"
4) "ccc"
5) "aaa"
```

- **spop**  随机删除指定个数的元素，并把元素返回

```shell
# 语法：spop 键 个数

127.0.0.1:6379[3]> spop class2 2
1) "ddd"
2) "eee"
127.0.0.1:6379[3]> spop class2 2
1) "aaa"
2) "ccc"
```

- **srandmember**  随机取出指定个数的集合元素

```shell
# 语法：srandmember 键  count
# - count 不写, 默认每次取出一个
# - count > 0, 如果count超过集合长度，则元素全部取出；如果count小于集合长度，则每次取出count个
# - count < 0, 取出 count 次元素，每次取一个（所以取出的元素最后可能是重复的）

127.0.0.1:6379[3]> sadd class3 aaa bbb ccc ddd eee fff ggg hhh jjj kkk
(integer) 10

127.0.0.1:6379[3]> srandmember class3
"jjj"
127.0.0.1:6379[3]> srandmember class3
"kkk"

127.0.0.1:6379[3]> srandmember class3 20
 1) "bbb"
 2) "hhh"
 3) "jjj"
 4) "ggg"
 5) "ddd"
 6) "aaa"
 7) "eee"
 8) "kkk"
 9) "ccc"
10) "fff"


127.0.0.1:6379[3]> srandmember class3 3
1) "kkk"
2) "eee"
3) "bbb"
127.0.0.1:6379[3]> srandmember class3 3
1) "aaa"
2) "bbb"
3) "hhh"


127.0.0.1:6379[3]> srandmember class3 -3
1) "eee"
2) "bbb"
3) "bbb"
127.0.0.1:6379[3]> srandmember class3 -20
 1) "eee"
 2) "ddd"
 3) "kkk"
 4) "jjj"
 5) "hhh"
 6) "eee"
 7) "ddd"
 8) "bbb"
 9) "ccc"
10) "kkk"
11) "kkk"
12) "eee"
13) "bbb"
14) "bbb"
15) "bbb"
16) "fff"
17) "ccc"
18) "kkk"
19) "kkk"
20) "aaa"
```

- **srem**  从集合删除指定的一个或者多个元素

```shell
127.0.0.1:6379[3]> srem class3 aaa
(integer) 1
127.0.0.1:6379[3]> srem class3 bbb ccc
(integer) 2
```

### 5. zset 类型

- **zadd**  向集合中添加元素

```shell
# 语法：zadd 键  权重1 值1  权重2 值2 ...
# 如果键不存在，则创建一个空的有序集；如果键存在但不是有序集类型时，返回一个错误。
# 如果元素已经存在，那么更新这个元素的权重值，并重新插入这个元素，来保证该元素在正确的位置上
# 权重值可以是整数值或双精度浮点数

127.0.0.1:6379[4]> zadd class 60 aaa
(integer) 1
127.0.0.1:6379[4]> zadd class 60 bbb
(integer) 1
127.0.0.1:6379[4]> zadd class 85 ccc 78 ddd 90 eee 95 fff 98 ggg
(integer) 5
```

- **zrange**  查询集合指定索引区间元素（从小到大排列）

```shell
127.0.0.1:6379[4]> zrange class 0 -1
1) "aaa"
2) "bbb"
3) "ddd"
4) "ccc"
5) "eee"
6) "fff"
7) "ggg"
127.0.0.1:6379[4]> zrange class 0 3
1) "aaa"
2) "bbb"
3) "ddd"
4) "ccc"
127.0.0.1:6379[4]> zrange class 0 3 withscores
1) "aaa"
2) "60"
3) "bbb"
4) "60"
5) "ddd"
6) "78"
7) "ccc"
8) "85"
```

- **zrevrange**  查询集合指定索引区间内元素（从大到小排列）

```shell
127.0.0.1:6379[4]> zrevrange class 0 -1 withscores
 1) "ggg"
 2) "98"
 3) "fff"
 4) "95"
 5) "eee"
 6) "90"
 7) "ccc"
 8) "85"
 9) "ddd"
10) "78"
11) "bbb"
12) "60"
13) "aaa"
14) "60"
```

- **zrangebyscore**   按权重进行从低往高排序，获取指定权重区间内的元素

```shell
# 语法：zrangebyscore 键 min max
# 支持分页： zrangebyscore 键 min max limit 开始索引 元素个数

127.0.0.1:6379[4]> zrangebyscore class 80 100 withscores
1) "ccc"
2) "85"
3) "eee"
4) "90"
5) "fff"
6) "95"
7) "ggg"
8) "98"


127.0.0.1:6379[4]> zrangebyscore class 80 100 limit 0 2
1) "ccc"
2) "eee"
127.0.0.1:6379[4]> zrangebyscore class 80 100 limit 1 2
1) "eee"
2) "fff"

# 分页时，开始索引超过上限则取到空集合
127.0.0.1:6379[4]> zrangebyscore class 80 100 limit 4 2
(empty list or set)
```

- **zrevrangebyscore**   按权重进行从高往低排序，获取指定权重区间内的元素

```shell
# 语法：zrangebyscore 键 max min

127.0.0.1:6379[4]> zrevrangebyscore class 100 80
1) "ggg"
2) "fff"
3) "eee"
4) "ccc"
```

- **zcard**  查询集合中元素的个数

```shell
127.0.0.1:6379[4]> zcard class
(integer) 7
```

- **zcount**  查询集合中权重在指定区间内的所有元素个数

```shell
# 语法：zcount 键 min max

127.0.0.1:6379[4]> zcount class 80 100
(integer) 4
```

- **zscore**  查询集合中指定元素的权重值

```shell
127.0.0.1:6379[4]> zscore class aaa
"60"
127.0.0.1:6379[4]> zscore class ggg
"98"
```

- **zincrby**  给集合中指定的元素新增权重数值

```shell
# 语法：zincrby  键  数值  元素

127.0.0.1:6379[4]> zincrby class 5 aaa
"65"
127.0.0.1:6379[4]> zincrby class -5 bbb
"55"
127.0.0.1:6379[4]> zrange class 0 -1 withscores
 1) "bbb"
 2) "55"
 3) "aaa"
 4) "65"
 5) "ddd"
 6) "78"
 7) "ccc"
 8) "85"
 9) "eee"
10) "90"
11) "fff"
12) "95"
13) "ggg"
14) "98"
```

- **zrank**  返回集合中指定元素的排名（权重从小到大排名）

```shell
127.0.0.1:6379[4]> zrank class eee
(integer) 4
127.0.0.1:6379[4]> zrank class ddd
(integer) 2
```

- **zrevrank**  返回集合中指定元素的排名（权重从大到小排名）

```shell
127.0.0.1:6379[4]> zrevrank class eee
(integer) 2
127.0.0.1:6379[4]> zrevrank class ggg
(integer) 0
```

- **zrem**  删除集合中指定的一个或多个元素

```shell
127.0.0.1:6379[4]> zrem class bbb
(integer) 1
127.0.0.1:6379[4]> zrem class aaa fff
(integer) 2
```

- **zpopmin**  删除指定数量的成员，从最低权重开始删除

```shell
# 语法：zpopmin 键 元素个数

127.0.0.1:6379[4]> zpopmin class 2
1) "ddd"
2) "78"
3) "ccc"
4) "85"
```

- **zpopmax ** 删除指定数量的成员，从最高权重开始删除

```shell
127.0.0.1:6379[4]> zpopmin class 2
1) "ddd"
2) "78"
3) "ccc"
4) "85"
```

