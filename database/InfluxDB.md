# InfluxDB

## 1 基本操作

### 1.1 操作方式

 1. 客户端命令行方式
 2. HTTP API接口
 3. 各语言API库

### 1.2 数据库操作

 - 显示数据库
```python
> show databases
name: databases
---------------
name
telegraf
_internal
testDB
```
 - 新建数据库
```python
> create database test
name: databases
---------------
name
telegraf    
_internal
testDB
```
 - 删除数据库
```python
> drop database test
```
 - 使用数据库
```python
> use test
```

### 1.3 数据表操作

 - 显示表
```python
> SHOW MEASUREMENTS
name: measurements
------------------
name
weather
```
 - 新建表
```python
insert disk_free,hostname=server01 value=442221834240i 1435362189575692182
# disk_free：表名
# hostname：索引
# value=xx：记录值，记录值可以有多个，最后是指定的时间
```
 - 删除表
```
> drop measurement disk_free
```

### 1.4 数据操作

 - 增加数据
```python
# 增加数据采用insert的方式，要注意的是 InfluxDB的insert中，表名与数据之间用逗号（,）分隔，tag和field之间用 空格分隔，多个tag或者多个field之间用逗号（,）分隔。
> insert disk_free,hostname=server01 value=442221834240i 1435362189575692182
> select * from disk_free
name: disk_free
---------------
time            hostname    value
1435362189575692182    server01    442221834240
```
 - 查询数据
 - 修改和删除数据
 > 数据保存策略（Retention Policies）来实现

### 1.5 series操作
```python
> show series from mem
key
mem,host=ResourcePool-0246-billing07
mem,host=billing07
```

## 2 HTTP API写入操作
### 2.1 通过HTTP API操作数据库

 - 建立数据库
```python
curl -POST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE mydb"
```
 - 删除数据库
```python
curl -POST http://localhost:8086/query --data-urlencode "q=DROP DATABASE mydb"
```

### 2.2 通过HTTP API添加数据
```python
curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server01,region=us-west value=0.64 1434055562000000000'
```

### 2.3 通过HTTP API添加多条数据
```python
curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server02 value=0.67
cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257'
```

### 2.4 HTTP API的响应

 - 2xx：204代表no content，200代表InfluxDB可以接收请求但是没有完成请求。一般会在body体中带有出错信息。
 - 4xx：InfluxDB不能解析请求。
 - 5xx：系统出现错误。

## 3 HTTP API查询操作
### 3.1 通过HTTP API查询方法
使用HTTP API在InfluxDB进行查询主要是发送 GET 请求到 InfluxDB的 /query 端
```python
curl -GET 'http://localhost:8086/query?pretty=true' --data-urlencode "db=mydb" 
--data-urlencode "q=SELECT value FROM cpu_load_short WHERE region='us-west'"
```

### 3.2 进行HTTP API查询多条数据
```python
curl -G 'http://localhost:8086/query?pretty=true' --data-urlencode "db=mydb" --data-urlencode "q=SELECT value FROM cpu_load_short WHERE region='us-west';
SELECT count(value) FROM cpu_load_short WHERE region='us-west'"
```

### 3.3 HTTP 查询的格式化输出

 - 规定时间格式
 在使用HTTP查询时可以使用 epoch 参数指定输出的时间格式。可选值有 epoch=[h,m,s,ms,u,ns]
```python
curl -G 'http://localhost:8086/query' --data-urlencode "db=mydb" 
--data-urlencode "epoch=s" --data-urlencode "q=SELECT value FROM cpu_load_short WHERE region='us-west'"
```
 - 指定每次查询数据大小
 使用 chunk_size 参数来指定每次结果的大小。比如，我要结果每次返回200个点的数据
```python
curl -G 'http://localhost:8086/query' --data-urlencode "db=mydb" --data-urlencode "chunk_size=200" --data-urlencode "q=SELECT value FROM cpu_load_short WHERE region='us-west'"
```

## 4 数据保留策略（Retention Policies）
### 4.1 数据保留策略操作
 - 查询策略
```python
> SHOW RETENTION POLICIES ON telegraf
name    duration    shardGroupDuration    replicaN    default
default    0        168h0m0s        1        true
#name--名称，此示例名称为 default
#duration--持续时间，0代表无限制
#shardGroupDuration--shardGroup的存储时间，shardGroup是InfluxDB的一个基本储存结构，应该大于这个时间的数据在查询效率上应该有所降低。
#replicaN--全称是REPLICATION，副本个数
#default--是否是默认策略`
```
 - 新建策略
```python
> CREATE RETENTION POLICY "2_hours" ON "telegraf" DURATION 2h REPLICATION 1 DEFAULT
> SHOW RETENTION POLICIES ON telegraf
name    duration    shardGroupDuration    replicaN    default
default    0        168h0m0s        1        false
2_hours    2h0m0s        1h0m0s            1        true
```
 - 修改策略
```python
> ALTER RETENTION POLICY "2_hours" ON "telegraf" DURATION 4h DEFAULT
> show retention POLICIES on telegraf
name    duration    shardGroupDuration    replicaN    default
default    0        168h0m0s        1        false
2_hours    4h0m0s        1h0m0s            1        true
```

 - 删除策略
```python
> drop retention POLICY "2_hours" ON "telegraf"
> show retention POLICIES on telegraf
name    duration    shardGroupDuration    replicaN    default
default    0        168h0m0s        1        false
```

## 5 连续查询（Continuous Queries）
### 5.1 操作

 - 新建连续查询
```python
CREATE CONTINUOUS QUERY <cq_name> ON <database_name> 
[RESAMPLE [EVERY <interval>] [FOR <interval>]] 
BEGIN SELECT <function>(<stuff>)[,<function>(<stuff>)] INTO <different_measurement> 
FROM <current_measurement> [WHERE <stuff>] GROUP BY time(<interval>)[,<stuff>] 
END
```

## 6 常用函数
### 6.1 聚合类函数

 - count（）函数
```python
>SELECT COUNT(water_level) FROM h2o_feet
name: h2o_feet
--------------
time                           count
1970-01-01T00:00:00Z     15258
# water_level这个字段在 h2o_feet表中共有15258条数据
```

InfluxDB中的函数如果没有指定时间的话，会默认以 epoch 0 (1970-01-01T00:00:00Z) 作为时间
```python
> SELECT COUNT(water_level) FROM h2o_feet WHERE time >= '2015-08-18T00:00:00Z' AND time < '2015-09-18T17:00:00Z' GROUP BY time(4d)
name: h2o_feet
--------------
time                           count
2015-08-17T00:00:00Z     1440
2015-08-21T00:00:00Z     1920
2015-08-25T00:00:00Z     1920
2015-08-29T00:00:00Z     1920
2015-09-02T00:00:00Z     1915
2015-09-06T00:00:00Z     1920
2015-09-10T00:00:00Z     1920
2015-09-14T00:00:00Z     1920
2015-09-18T00:00:00Z     335
```

 - DISTINCT()函数
返回一个字段（field）的唯一值
```python
> SELECT DISTINCT("level description") FROM h2o_feet
name: h2o_feet
--------------
time                           distinct
1970-01-01T00:00:00Z     between 6 and 9 feet
1970-01-01T00:00:00Z     below 3 feet
1970-01-01T00:00:00Z     between 3 and 6 feet
1970-01-01T00:00:00Z     at or greater than 9 feet
# 这个例子显示level description这个字段共有四个值，然后将其显示了出来，时间为默认时间。
```
 - MEAN() 函数
返回一个字段（field）中的值的算术平均值（平均值）。字段类型必须是长整型或float64
```python
> SELECT MEAN(water_level) FROM h2o_feet
name: h2o_feet
--------------
time                           mean
1970-01-01T00:00:00Z     4.286791371454075
# water_level字段的平均值为4.286791371454075
```
 - SPREAD()函数
返回字段的最小值和最大值之间的差值。数据的类型必须是长整型或float64。
```python
> SELECT SPREAD(water_level) FROM h2o_feet
name: h2o_feet
--------------
time                            spread
1970-01-01T00:00:00Z      10.574
```

### 6.2 选择类函数

 - TOP()函数
返回一个字段中最大的N个值，字段类型必须是长整型或float64类型。
```python
> SELECT TOP("water_level",3) FROM "h2o_feet"
name: h2o_feet
time                   top
----                   ---
2015-08-29T07:18:00Z   9.957
2015-08-29T07:24:00Z   9.964
2015-08-29T07:30:00Z   9.954
# 返回表中 water_level字段中最大的三个值
```
 - BOTTOM()函数
 返回一个字段中最小的N个值。字段类型必须是长整型或float64类型
```python
> SELECT BOTTOM(water_level,3) FROM h2o_feet
name: h2o_feet
--------------
time                           bottom
2015-08-29T14:30:00Z     -0.61
2015-08-29T14:36:00Z     -0.591
2015-08-30T15:18:00Z     -0.594
# 返回表中 water_level字段中最小的三个值。

#也可将关联tag放在一起查询，但如果tag值少于N的值，则返回的值的个数只会取tag中字段值少的那个
> SELECT BOTTOM(water_level,location,3) FROM h2o_feet
name: h2o_feet
--------------
time                           bottom     location
2015-08-29T10:36:00Z     -0.243     santa_monica
2015-08-29T14:30:00Z     -0.61      coyote_creek
# 语句取最小的三个值，然而结果只返回了2个值，因为 location 这个tag只有 两个取值
```

 - FIRST()函数
```python
> SELECT FIRST(water_level) FROM h2o_feet WHERE location = 'santa_monica'
name: h2o_feet
--------------
time                           first
2015-08-18T00:00:00Z     2.064
# 返回了 在 location为santa_monica条件下，最旧的那个water_level字段的取值和时间
```
 - LAST()函数
```python
> SELECT LAST(water_level),location FROM h2o_feet WHERE time >= '2015-08-18T00:42:00Z' and time <= '2015-08-18T00:54:00Z'
name: h2o_feet
--------------
time                           last      location
2015-08-18T00:54:00Z     6.982     coyote_creek
```
 - MAX()函数
 作用：返回一个字段中的最大值。该字段类型必须是长整型，float64，或布尔类型
```python
> SELECT MAX(water_level),location FROM h2o_feet
name: h2o_feet
--------------
time                           max       location
2015-08-29T07:24:00Z     9.964     coyote_creek
```

 - MIN()函数
作用：返回一个字段中的最小值。该字段类型必须是长整型，float64，或布尔类型。
```python
> SELECT MIN(water_level),location FROM h2o_feet
name: h2o_feet
--------------
time                          min       location
2015-08-29T14:30:00Z    -0.61     coyote_creek
```
 - PERCENTILE()函数
作用：返回排序值排位为N的百分值。字段的类型必须是长整型或float64。
```python
> SELECT PERCENTILE(water_level,5),location FROM h2o_feet
name: h2o_feet
--------------
time                      percentile     location
2015-08-28T12:06:00Z      1.122             santa_monica
```