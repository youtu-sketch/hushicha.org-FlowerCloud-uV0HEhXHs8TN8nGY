
为什么学习redis，前几天有个需求，用到了redis库，之前学习过但仅限于大学的时候，这个时候差不多忘光了，需求测的时候也是现学的几个查询指令。印象最深的是开发设计的时候存储了10个不同key值但value（list字符串）相同的数据，当时问过为什么要存十个，解释为  “为了防止频繁的访问一个key导致的热key问题，所以存储10个相同value的key，随机访问，分散压力”。后面查询资料才发现，频繁访问一个会导致CPU资源过载，内存资源紧张，负载不均衡等问题。


## 一.环境搭建


我是直接在网上下载的win redis\-x64\.zip压缩包，解压到本地后可以看到两个文件，一个是redis\-server.exe redis服务启动程序，另一个是redis\-cli.exe redis客户端操作程序。压缩包地址：https://pan.baidu.com/s/1t\_NpWnE\_qj4TvyESyvXYDA?pwd\=4c0a    提取码：4c0a


## 二.基本使用


1\.启动服务




```
redis-server.exe redis.windows.conf  # 在解压目录下打开cmd，运行此命令
```


2\.打开redis客户端连接




```
redis-cli.exe  # 在解压目录下另外打开cmd，运行此命令，服务不要关闭
```


输入ping返回PONG即连接成功


![](https://img2024.cnblogs.com/blog/1668630/202411/1668630-20241128142526503-24859029.png)


 ip默认本地ip，端口默认6379


3\.切换库




```
select index  # redis共有16个库，index为0-15，默认为0号库
```


![](https://img2024.cnblogs.com/blog/1668630/202411/1668630-20241128143552381-1419182636.png)


 4\.设置key和value值




```
set key value   # 设置key值和对应的value值
get key  # 通过key获取value值
```


![](https://img2024.cnblogs.com/blog/1668630/202411/1668630-20241128143747598-1274963486.png)


 5\.停止服务




```
shutdown
```


6\.其它常用操作


dbsize：查看当前库key值数量


flushdb：删除当前库所有数据


exit：退出客户端连接


keys \*：查看当前库所有key值（还可模糊匹配：\*通配符，？占位符，例：\*明\*，\*ming，li\*）


exists key：判断key是否存在（例：exists name   存在返回1，不存在返回0）


expire key seconds：设置key过期时间（例如：expire name 30，设置name存活30s，30秒后key会失效删除）


ttl key：查询key剩余存活时间（\-1 表示未设置存活时间，永久生效，\-2已失效，存活时间已过，正数表示剩余存活时间）


type key：查询vaule数据类型


del key：删除key


append key value：在当前value末尾追加内容


strlen key：返回value长度


## 三.存储类型


### 1\.string


字符串是Redis中最基本的数据类型，可以存储任何形式的字符串，包括数字、文本等


存储时使用：set key value（通过set存储的value都是string类型）


### 2\.哈希（Hash）


哈希类型是一个键值对的集合，适用于存储对象类型的数据。每个哈希表可以包含多个字段和值，非常适合表示具有多个属性的数据对象


1）HSET key field value：设置哈希表字段的值。


2）HGET key field：获取哈希表字段的值。


3）HDEL key field：删除哈希表的某个字段


### 3\.列表（list）


列表是一个双向链表，支持在列表的两端进行高效的插入和删除操作


1）声明一个key，value值为list的




```
lpush namelist 'liming'  # 当key不存在时，创建一个key并赋予value值
```


2）添加多个vallue




```
lpush namelist '2' '3'  # 默认在最左侧添加，即（['3','2','liming']）
```


3）根据下标查询value list值




```
lrange namelist 0 100  # 查询namelist  key对于的value list中0-100的下标值
```


4）向list右侧添加value值




```
rpush namelist 4 5  # 即['3','2','liming',4,5]
```


5）向右侧添加单个元素值




```
 rpushx namelist 6  # 即[.......4,5,6]，若key不存在则添加失败
```


6）向左添加单个元素值




```
lpushx namelist 0  # 若key不存在则添加失败
```


7）通过下标获取对应的元素值




```
lindex namelist 2
```


8）删除最左侧的值




```
lpop namelist
```


9）删除最右侧的值




```
rpop namelist
```


10）删除




```
lrem namelist 0 4  # 删除list中值为4的全部元素
lrem namelist 2 4  # 删除list中第二个元素值为4的元素
```


11）查询list长度




```
llen key
```


12）插入元素




```
linsert key before 3 2  # 在3前插入2
linsert key after 3 2  #在3后插入2
```


### 4\.集合（set）


集合是一种无序、不重复的数据类型。Redis提供了高效的集合操作，如求交集、并集、差集等


1）创建一个set类型的value值




```
sadd set 4 5 6  # 赋予set的元素值有三个，4，5，6
```


2）查看全部的value值




```
smembers set
```


3）判断value值是否存在




```
sismember key value
```


4）判断元素个数




```
scard key
```


5）删除元素




```
 srem set 1,2,3 4 5  # 删除1,2,3  4  5三个元素值
```


### 5\.有序集合（zset）


 有序集合与普通集合类似，但每个元素都有一个关联的分数（score），Redis会根据分数对元素进行自动排序


 1）ZADD key score member：向有序集合中添加元素并指定分数




```
zadd age 1 2 2 3  # 添加两个元素2，3
```


 2）ZRANGE key start stop：获取指定范围内的有序集合成员




```
zrange age 0 100
```


 3）ZREM key member：删除有序集合中的某个元素




```
zrem age 4
```


## 四.python操作


### 1\.安装redis库




```
pip install redis
```


### 2\.实例操作




```
# pip install redis
import time

import redis

host = '127.0.0.1'  # redis服务地址
port = 6379  # redis服务端口

r = redis.StrictRedis(host=host, port=port, db=0, decode_responses=True)  # decode_responses=True设置true返回list类型，不设置返回字节
r.delete('age')  # 删除key
key = r.keys()  # 获取数据库所有的key，返回list
print(key)
r.set('python', 'python', ex=10)  # set(key,value,过期时间(ex设置的是秒，px设置毫秒))
time.sleep(3)
print(r.ttl('python'))  # 查询key剩余存活时间
r.set('python', 'pythoned', nx=True)
'''
nx=True当key值python不存在时，添加数据，存在则添加失败
xx=True 当key值python存在时，修改value值
'''

print(r.get('python'))  # 通过key获取value
print(r.dbsize())
r.quit()  # 关闭连接
```


操作方法和dos差不多，r为redis实例化对象，直接用r.的方式调用方法操作即可。


### 3\.python实现随机访问key来避免频繁的访问一个key导致的热key问题




```
import random

import redis

host = '127.0.0.1'  # redis服务地址
port = 6379  # redis服务端口

r = redis.StrictRedis(host=host, port=port, db=1, decode_responses=True)  # decode_responses=True设置true返回list类型，不设置返回字节
key_list = ['key0', 'key1', 'key2', 'key3', 'key4', 'key5', 'key6', 'key7', 'key8', 'key9']
for key in key_list:
    r.set(key, 1)
keys = r.keys()
a = random.choice(keys)
print(r.get(a))
```


 


 本博客参考[slower加速器](https://jisuanqi.org)。转载请注明出处！
