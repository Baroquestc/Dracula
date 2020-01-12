# Redis

## 1 NoSQL简介

NoSQL：一类新出现的数据库(not only sql)，它的特点：

- 不支持SQL语法
- 存储结构跟传统关系型数据库中的那种关系表完全不同，nosql中存储的数据都是KV形式
- NoSQL的世界中没有一种通用的语言，每种nosql数据库都有自己的api和语法，以及擅长的业务场景
- NoSQL中的产品种类相当多：
  - Mongodb
  - Redis
  - Hbase hadoop
  - Cassandra hadoop

### 1.1 NoSQL vs SQL

- 适用场景不同：sql数据库适合用于关系特别复杂的数据查询场景，nosql反之
- “事务”特性的支持：sql对事务的支持非常完善，而nosql基本不支持事务
- 两者在不断地取长补短，呈现融合趋势

### 1.2 Redis特性优势

**Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。**

- 特性
  - Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
  - Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
  - Redis支持数据的备份，即master-slave模式的数据备份。

- 优势
  - 性能极高 – Redis能读的速度是110000次/s,写的速度是81000次/s 。
  - 丰富的数据类型 – Redis支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
  - 原子 – Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行。
  - 丰富的特性 – Redis还支持 publish/subscribe, 通知, key 过期等等特性。

### 1.3 Redis应用场景

- **用来做缓存(ehcache/memcached)——redis的所有数据是放在内存中的（内存数据库）**
- 可以在某些特定应用场景下替代传统数据库——比如社交类的应用
- 在一些大型系统中，巧妙地实现一些特定的功能：session共享、购物车

## 2 安装

- step1:下载

  > wget http://download.redis.io/releases/redis-3.2.8.tar.gz

- step2:解压

  > tar -zxvf redis-3.2.8.tar.gz

- step3:复制，放到usr/local⽬录下

  > sudo mv ./redis-3.2.8 /usr/local/redis/

- step4:进⼊redis⽬录

  > cd /usr/local/redis/

- step5:生成

  > sudo make

- step6:测试,这段运⾏时间会较⻓

  > sudo make test

  注意：

  > 报错：[You need tcl 8.5 or newer in order to run the Redis test](https://www.cnblogs.com/cxbhakim/p/9150953.html)

```bash
# 解决方法
[root@etcd3 tmp]# wget http://downloads.sourceforge.net/tcl/tcl8.6.1-src.tar.gz
[root@etcd3 tmp]# tar xzvf tcl8.6.1-src.tar.gz  -C /usr/local/
[root@etcd3 tmp]# cd  /usr/local/tcl8.6.1/unix/
[root@etcd3 unix]# ./configure
[root@etcd3 unix]# make
[root@etcd3 unix]# make install
```

- step7:安装,将redis的命令安装到/usr/local/bin/⽬录

  > sudo make install

- step8:安装完成后，我们进入目录/usr/local/bin中查看

  > cd /usr/local/bin
  > ls -all

  ![image-20191201144129820](https://tva1.sinaimg.cn/large/006tNbRwly1g9h7ua5kl5j30ys0fyan6.jpg)

  > - redis-server redis服务器
  > - redis-cli redis命令行客户端
  > - redis-benchmark redis性能测试工具
  > - redis-check-aof AOF文件修复工具
  > - redis-check-rdb RDB文件检索工具

- step9:配置⽂件，移动到/etc/⽬录下

- 配置⽂件⽬录为/usr/local/redis/redis.conf

  > sudo cp /usr/local/redis/redis.conf /etc/redis/

## 3 配置

- Redis的配置信息在/etc/redis/redis.conf下。

- 查看

  > sudo vi /etc/redis/redis.conf

- 绑定ip：如果需要远程访问，可将此⾏注释，或绑定⼀个真实ip

  > bind 127.0.0.1

- 端⼝，默认为6379

  > port 6379

- 是否以守护进程运⾏

  - 如果以守护进程运⾏，则不会在命令⾏阻塞，类似于服务
  - 如果以⾮守护进程运⾏，则当前终端被阻塞
  - 设置为yes表示守护进程，设置为no表示⾮守护进程
  - 推荐设置为yes

  > daemonize yes

- 数据⽂件

  > dbfilename dump.rdb

- 数据⽂件存储路径

  > dir /var/lib/redis

- ⽇志⽂件

  > logfile /var/log/redis/redis-server.log

- 数据库，默认有16个

  > database 16

- 主从复制，类似于双机备份。

  > slaveof

参考资料

redis配置信息http://blog.csdn.net/ljphilp/article/details/52934933

## 4 服务端和客户端命令

### 4.1 服务端

- 服务器端的命令为redis-server

- 可以使⽤help查看帮助⽂档

  > redis-server --help

- 推荐使⽤服务的⽅式管理redis服务

- 启动

  > sudo service redis start

- 停⽌

  > sudo service redis stop

- 重启 sudo service redis restart

- 个人习惯

  > ps -ef|grep redis 查看redis服务器进程
  > sudo kill -9 pid 杀死redis服务器
  > sudo redis-server /etc/redis/redis.conf 指定加载的配置文件

### 4.2 客户端

- 客户端的命令为redis-cli

- 可以使⽤help查看帮助⽂档

  > redis-cli --help

- 连接redis

  > redis-cli

![连接redis](https://tva1.sinaimg.cn/large/006tNbRwly1g9ha131z2rj30cn00x0so.jpg)

运⾏测试命令

> ping

![ping](https://tva1.sinaimg.cn/large/006tNbRwly1g9ha1okk43j30cn00x0so.jpg)

- 切换数据库

- 数据库没有名称，默认有16个，通过0-15来标识，连接redis默认选择第一个数据库

  > select n

![切换数据库](https://tva1.sinaimg.cn/large/006tNbRwly1g9ha23juwfj30bw01u3yi.jpg)

## 5 数据操作

- redis是key-value的数据，所以每个数据都是一个键值对
- 键的类型是字符串
- 值的类型分为五种：
  - 字符串string
  - 哈希hash
  - 列表list
  - 集合set
  - 有序集合zset

### 5.1 string

- string是redis最基本的类型
- 最大能存储512MB数据
- string类型是二进制安全的，即可以为任何数据，比如数字、图片、序列化对象等

#### 5.1.1 保存

如果设置的键不存在则为添加，如果设置的键已经存在则修改

- 设置键值

  > set key value

  例1：设置键为name值为itcast的数据

  > set name itcast

![设置键值](https://tva1.sinaimg.cn/large/006tNbRwly1g9hg3b3nbzj308d04eq2v.jpg)

- 设置键值及过期时间，以秒为单位

  > setex key seconds value

  例2：设置键为aa值为aa过期时间为3秒的数据

  > setex aa 3 aa


![设置过期时间](https://tva1.sinaimg.cn/large/006tNbRwly1g9hg4uewsnj307n02st8k.jpg)

- 设置多个键值

  > mset key1 value1 key2 value2 ...

  例3：设置键为'a1'值为'python'、键为'a2'值为'java'、键为'a3'值为'c'

  > mset a1 python a2 java a3 c


![设置多值](https://tva1.sinaimg.cn/large/006tNbRwly1g9hg6xxefkj30b404egls.jpg)

- 追加值

  > append key value

  例4：向键为a1中追加值' haha'

  > append 'a1' 'haha'


![追加值](https://tva1.sinaimg.cn/large/006tNbRwly1g9hg7hkra6j307s020dfp.jpg)

#### 5.1.2 获取

- 根据多个键获取多个值

  > mget key1 key2 ...

  例5：获取键a1、a2、a3'的值

  > mget a1 a2 a3


- 获取值长度

> STRLEN key

#### 5.1.3 运算

- 要求：值是数字
- 将key对应的value加1

> INCR key

- 将key对应的value加整数

> INCRBY key increment

- 将key对应的value减1

> DECR key

- 将key对应的value减整数

> DECRBY key decrement

### 5.2 键命令

1. 查找键，参数⽀持正则表达式

> keys pattern

- 查看所有键

> keys *

![查看所有键](https://tva1.sinaimg.cn/large/006tNbRwly1g9hgaklqqkj305l03wmx2.jpg)

- 查看名称中包含a的键

> keys 'a*'

![查看键](https://tva1.sinaimg.cn/large/006tNbRwly1g9hgbge88tj306801y3yc.jpg)

2. 判断键是否存在，如果存在返回1，不存在返回0

> exists key1

- 判断键a1是否存在

  > exists a1

![判断键是否存在](https://tva1.sinaimg.cn/large/006tNbRwly1g9hgbvdtizj307u02twec.jpg)

3. 查看键对应的value的类型

> type key

- 查看键a1的值类型，为redis⽀持的五种类型中的⼀种

  > type a1

4. 删除键及对应的值

> del key1 key2 ...

- 删除键a2、a3

  > del a2 a3

5. 重新设置过期时间

> expire key seconds

- 设置键'a1'的过期时间为3秒

  > expire 'a1' 3

6. 查看有效时间，以秒为单位

> ttl key

- 查看键'bb'的有效时间

  > ttl bb

### 5.3 hash

#### 5.3.1 增加、修改

- 设置单个属性

> HSET key field value

设置键 user的属性name为itheima

> hset user name itheima

2. 设置多个属性

> hmset key field1 value1 field2 value2 ...

- 设置键u2的属性name为itcast、属性age为11

  > hmset u2 name itcast age 11

#### 5.3.2 获取

1. 获取指定键所有的属性

> hkeys key

- 获取键u2的所有属性

  > hkeys u2

2. 获取⼀个属性的值

> hget key field

- 获取键u2属性'name'的值

  > hget u2 'name'

3. 获取多个属性的值

> hmget key field1 field2 ...

- 获取键u2属性'name'、'age的值

  > hmget u2 name age

4. 获取所有属性和值

> HGETALL key

5. 获取所有的属性

> HKEYS key

6. 返回包含属性的个数

> HLEN key

7. 获取所有值

> HVALS key

8. 返回值的字符串长度

> HSTRLEN key field

9. 判断属性是否存在

> HEXISTS key field

#### 5.3.3 删除

- 删除整个hash键及值，使⽤del命令

- 删除属性，属性对应的值会被⼀起删除

  > hdel key field1 field2 ...

  - 删除键'u2'的属性'age'

  > hdel u2 age

### 5.4 list

#### 5.4.1 增加

1. 在左侧插⼊数据

   >  lpush key value1 value2 ...

   - 从键为'a1'的列表左侧加⼊数据a 、 b 、c
```
lpush a1 a b c
```

2. 在右侧插⼊数据

   > rpush key value1 value2 ...

   - 从键为'a1'的列表右侧加⼊数据0 1

```
rpush a1 0 1
```

3. 在指定元素的前或后插⼊新元素

   > linsert key before或after 现有元素 新元素

   - 在键为'a1'的列表中元素'b'前加⼊'3'

```
linsert a1 before b 3
```

#### 5.4.2 获取

- 返回列表⾥指定范围内的元素
  - start、stop为元素的下标索引

  - 索引从左侧开始，第⼀个元素为0

  - 索引可以是负数，表示从尾部开始计数，如-1表示最后⼀个元素

    > lrange key start stop

- 获取键为'a1'的列表所有元素

```
lrange a1 0 -1
```

#### 5.4.3 设置指定索引位置的元素值

- 索引从左侧开始，第⼀个元素为0

- 索引可以是负数，表示尾部开始计数，如-1表示最后⼀个元素

  > lset key index value

  - 修改键为'a1'的列表中下标为1的元素值为'z'

  > lset a 1 z

#### 5.4.4 删除

删除指定元素

- 将列表中前count次出现的值为value的元素移除
- count > 0: 从头往尾移除
- count < 0: 从尾往头移除
- count = 0: 移除所有

> lrem key count value

1. 向列表'a2'中加⼊元素'a'、'b'、'a'、'b'、'a'、'b'

```
lpush a2 a b a b a b
```

2. 从'a2'列表右侧开始删除2个'b'

```
lrem a2 -2 b
```

3. 查看列表'py12'的所有元素

```
lrange a2 0 -1
```

### 5.5 set

#### 5.5.1 增加

- 增加元素

> sadd key member1 member2 ...

 - 向键'a3'的集合中添加元素'zhangsan'、'lisi'、'wangwu'

   ```
   sadd a3 zhangsan sili wangwu
   ```

#### 5.5.2 获取

- 返回所有的元素

> smembers key   

- 返回集合元素个数

> SCARD key

#### 5.5.3 删除

- 删除指定元素

  > srem key

- 例3：删除键'a3'的集合中元素'wangwu'

  ```
  srem a3 wangwu
  ```

#### 5.5.4 其他

- 求多个集合的交集

> SINTER key [key ...]

- 求某集合与其它集合的差集

> SDIFF key [key ...]

- 求多个集合的合集

> SUNION key [key ...]

- 判断元素是否在集合中

> SISMEMBER key member

### 5.6 zset

- sorted set，有序集合
- 元素为string类型
- 元素具有唯⼀性，不重复
- 每个元素都会关联⼀个double类型的score，表示权重，通过权重将元素从⼩到⼤排序
- 说明：没有修改操作

#### 5.6.1 增加

- 添加

  > zadd key score1 member1 score2 member2 ...

- 例1：向键'a4'的集合中添加元素'lisi'、'wangwu'、'zhaoliu'、'zhangsan'，权重分别为4、5、6、3

```
zadd a4 4 lisi 5 wangwu 6 zhaoliu 3 zhangsan
```

#### 5.6.2 获取

- 返回指定范围内的元素

- start、stop为元素的下标索引

- 索引从左侧开始，第⼀个元素为0

- 索引可以是负数，表示从尾部开始计数，如-1表示最后⼀个元素

  > zrange key start stop

- 获取键'a4'的集合中所有元素

```
zrange a4 0 -1
```

- 返回score值在min和max之间的成员

> zrangebyscore key min max

- 获取键'a4'的集合中权限值在5和6之间的成员

```
zrangebyscore a4 5 6
```

返回成员member的score值

> zscore key member

- 获取键'a4'的集合中元素'zhangsan'的权重

```
zscore a4 zhangsan
```

#### 5.6.3 删除

- 删除指定元素

  > zrem key member1 member2 ...

- 删除集合'a4'中元素'zhangsan'

```
zrem a4 zhangsan
```

- 删除权重在指定范围的元素

> zremrangebyscore key min max

- 删除集合'a4'中权限在5、6之间的元素

```
zremrangebyscore a4 5 6
```

## 6 与python交互

- pip安装

```bash
sudo pip install redis
```

- 源码安装

```bash
wget https://github.com/andymccurdy/redis-py/archive/master.zip
unzip master.zip
cd redis-py-master
sudo python setup.py install
```

### 6.1 方法

- 通过init创建对象，指定参数host、port与指定的服务器和端⼝连接，host默认为localhost，port默认为6379，db默认为0

```
sr = StrictRedis(host='localhost', port=6379, db=0)

简写
sr=StrictRedis()
```

- string
  - set
  - setex
  - mset
  - append
  - get
  - mget
  - key

- keys
  - exists
  - type
  - delete
  - expire
  - getrange
  - ttl

- hash
  - hset
  - hmset
  - hkeys
  - hget
  - hmget
  - hvals
  - hdel

- list
  - lpush
  - rpush
  - linsert
  - lrange
  - lset
  - lrem

- set
  - sadd
  - smembers
  - srem

- zset
  - zadd
  - zrange
  - zrangebyscore
  - zscore
  - zrem
  - zremrangebyscore

### 6.2 string

#### 6.2.1 string-增加

- ⽅法set，添加键、值，如果添加成功则返回True，如果添加失败则返回False
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #添加键name，值为itheima
        result=sr.set('name','itheima')
        #输出响应结果，如果添加成功则返回True，否则返回False
        print(result)
    except Exception as e:
        print(e)
```

#### 6.2.2 string-获取

- ⽅法get，添加键对应的值，如果键存在则返回对应的值，如果键不存在则返回None
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #获取键name的值
        result = sr.get('name')
        #输出键的值，如果键不存在则返回None
        print(result)
    except Exception as e:
        print(e)
```

#### 6.2.3 string-修改

- ⽅法set，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #设置键name的值，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
        result = sr.set('name','itcast')
        #输出响应结果，如果操作成功则返回True，否则返回False
        print(result)
    except Exception as e:
        print(e)
```

#### 6.2.4 string-删除

- ⽅法delete，删除键及对应的值，如果删除成功则返回受影响的键数，否则则返 回0
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #设置键name的值，如果键已经存在则进⾏修改，如果键不存在则进⾏添加
        result = sr.delete('name')
        #输出响应结果，如果删除成功则返回受影响的键数，否则则返回0
        print(result)
    except Exception as e:
        print(e)
```

#### 6.2.5 获取键

- ⽅法keys，根据正则表达式获取键
- 编写代码如下

```python
from redis import *
if __name__=="__main__":
    try:
        #创建StrictRedis对象，与redis服务器建⽴连接
        sr=StrictRedis()
        #获取所有的键
        result=sr.keys()
        #输出响应结果，所有的键构成⼀个列表，如果没有键则返回空列表
        print(result)
    except Exception as e:
        print(e)
```

## 7 搭建主从

### 7.1 主从概念

- ⼀个master可以拥有多个slave，⼀个slave⼜可以拥有多个slave，如此下去，形成了强⼤的多级服务器集群架构
- master用来写数据，slave用来读数据，经统计：网站的读写比率是10:1
- 通过主从配置可以实现读写分离

![主从配置](https://tva1.sinaimg.cn/large/006tNbRwly1g9ja7846y2j30df08s751.jpg)

- master和slave都是一个redis实例(redis服务)

### 7.2 主从配置

#### 7.2.1 主

- 查看当前主机的ip地址

> ifconfig

![ifconfig](https://tva1.sinaimg.cn/large/006tNbRwly1g9ja8eyr51j30ju0bcq61.jpg)

- 修改etc/redis/redis.conf文件

  > sudo vi redis.conf
  > bind 192.168.26.128

- 重启redis服务

  > sudo service redis stop
  > redis-server redis.conf

#### 7.2.2 从

- 复制etc/redis/redis.conf文件

  > sudo cp redis.conf ./slave.conf

- 修改redis/slave.conf文件

  > sudo vi slave.conf

- 编辑内容

  > bind 192.168.26.128
  > slaveof 192.168.26.128 6379 # 最新版本更换为replicaof
  > port 6378

- redis服务

  > sudo redis-server slave.conf

- 查看主从关系

  > redis-cli -h 192.168.26.128 info Replication

![主从关系](https://tva1.sinaimg.cn/large/006tNbRwly1g9ja945fvxj30id04m0t8.jpg)

#### 7.2.3 数据操作

- 在master和slave分别执⾏info命令，查看输出信息 进入主客户端

  > redis-cli -h 192.168.26.128 -p 6379

- 进入从的客户端

  > redis-cli -h 192.168.26.128 -p 6378

- 在master上写数据

  > set aa aa

![master写数据](https://tva1.sinaimg.cn/large/006tNbRwly1g9ja9orx35j30g201xwek.jpg)

- 在slave上读数据

> get aa

![slave读数据](https://tva1.sinaimg.cn/large/006tNbRwly1g9jaa3tm4wj30b404egls.jpg)

## 8 搭建集群

- 分类
  - 软件层面
    - 只有一台电脑，在这一台电脑上启动了多个redis服务。
  - 硬件层面
    - 存在多台实体的电脑，每台电脑上都启动了一个redis或者多个redis服务。

- redis集群搭建http://www.cnblogs.com/wuxl360/p/5920330.html
- [Python]搭建redis集群http://blog.5ibc.net/p/51020.html

