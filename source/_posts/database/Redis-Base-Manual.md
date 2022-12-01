---
title: Redis 入门手册
description: Redis 入门笔记
categories: 
- Database
- Redis
---

## Redis 简介

<b>概念</b>：Redis（Remote Dictionary Server）是由C语言开发的一个开源高性能键值对NoSql数据库

<b>特征</b>： 
- 数据间没有必然的关系;
- 单线程机制：redis操作是原子性的，采用单线程处理所有业务，只有前一个命令执行完才能执行下一个命令，因此无需考虑并发对数据的影响。
- 高性能
- 多数据类型支持
- 持久化支持：可进行灾难恢复

<b>应用</b>:
- 热点数据加速查询：如热点商品、热点新闻等
- 任务队列：秒杀、抢购、购票排队等
- 及时信息的查询：排行榜、访问统计等
- 时效信息控制：验证码控制、投票控制等
- 分布式数据共享
- 消息队列
- 分布式锁

## 环境准备

### 使用Docker镜像

```bash
# 下载redis镜像
docker pull redis
# 后台运行redis
docker run --name redis-container -d redis redis-server
# 进入容器
docker exec -it redis-container /bin/bash
```
`redis-server` 是服务端命令，启动/关闭redis服务
`redis-cli` 是客户端命令，用于连接/操作已存在的redis

## Redis 数据类型

> [Redis 数据类型](https://redis.io/topics/data-types)

Redis 采用key value的形式存储数据，其支持的数据类型包括：`String`、`Hash`、`List`、`Sorted List`以及`Set`，注意这里的数据类型指的是value，key永远是`String`

### `String`类型

- **添加/修改数据**

```bash
# 添加单个数据
set key value
# 添加修改多个数据
mset key1 value1 key1 value2
# 追加信息（如果key存在，则追加，否则新建）
append key value
```

- **获取数据**

```bash
# 获取单个数据
get key
# 获取多个数据
mget key1 key2
# 获取字符串长度
strlen key
```

- **删除数据**

```bash
del key
```

- **数值增减**

```bash
# 每次加1
incr key
# 每次加指定的数值（increment 可以是负值）
incrby key increment
# 每次加指定的数值（浮点数）
incrbyfload key increment
# 每次减1
decr key
# 每次减指定的数值
decrby key increment
```

- **设置数值的有效周期**

> 场景：设置验证码过期时间

```bash
# key seconds秒后过期
setex key seconds value
# key millseconds秒后过期
psetex key millseconds value
```

### `Hash`类型

> `Hash`类型注意事项
>
> 1. `Hash`类型中的value只能是字符串，不存在嵌套情况
> 2. 每个`Hash`最多可以存储2<sup>32</sup> - 1 个键值对
> 3. `hgetall`操作可以获取全部属性，但如果field过多，效率会比较低

- **添加/修改数据**

```bash
# 为某个key添加单个成员
# 比如user的结构是{name: "redis"}
# 那么对应的命令是：hset user name redis
hset key field value
# 如果key中存在filed这个字段则什么都不操作，否则创建
hsetnx key filed value
# 为某个key添加多个成员
hmset key filed value 
```

- **获取数据**

```bash
# 获取key中单个字段的值
hget key filed
# 获取key中多个字段的值
hmget key filed1 filed2....
# 获取key对应的全部值
hgetall key
```
- **删除数据**

```bash
hdel key filed1 [filed2]
```

- **获取哈希表中字段的数量**

```bash
hlen key
```

- **获取哈希表中是否存在指定的字段**

```bash
hexists key filed
```

- **获取哈希表中所有的字段/值**

```bash
# 获取key对应的所有字段
hkeys key
# 获取key的所有value
hvals key
```

- **指定字段数值的增加**

```bash
hincrby key filed increment
hincrbyfloat key filed increment
```

### `List`类型的基本操作

- **添加/修改数据**

```bash
# 向左添加数据
lpush key value [value1]
# 向右添加数据
rpush key value [value2]
```

- **获取数据**

```bash
# 获取数据
# lrange key 0 -1 可以获取列表中全部数据
lrange key start length
# 获取特定索引的数据
lindex key index
# 获取长度
llen key
```

- **移除数据**

```bash
# 依次移除数据
# 每次操作只会删除一个数据
lpop key
rpop key
# 当给定列表内没有任何元素可供弹出的时候，连接将被 BLPOP 命令阻塞，直到等待超时或发现可弹出元素为止。
blpop key1 [key2] timeout
brpop key1 [key2] timeout
# 移除指定的数据
lrem key count value
```

### `Set`类型

- **添加/修改数据**

```bash
# 添加数据
sadd key value1 [value2]
```

- **获取数据**

```bash
# 获取全部数据
smembers key
# 获取数据个数
scard key
# 判断集合中是否报告某个值
sismemeber key value
```

- **移除数据**

```bash
# 移除指定的数据
srem key value1 [value2]
```

- **获取随机数据**

```bash
# 从集合中随机获取指定个数的随机值
srandmember key count
# 从集合中获取指定个数的随机值，并将这些值从集合中删除
spop key count
```

- **交集/并集/差集**

```bash
# key1 key2 的交集
sinter key1 [key2]
# 将key1 key2 的交集 存放在destination中
sinterstore destination key1 [key2]
# key1 key2 的并集
sunion key1 [key2]
sunionstore destination key1 [key2]
# key1 key2 的差集
sdiff key1 [key2]
sdiffstore destination key1 [key2]
# 将source中的value值移入destination中
smove source destination value
```

### `Sorted Set` 

- **添加/修改数据**

```bash
# score仅用于排序，value才是实际的值
zadd key score1 value1 [score2 value2]
```

- **获取数据**

```bash 
# 获取全部数据：zrange example 0 -1
zrange key start stop [WITHSCORES]
# 反向获取数据
zrevrange key start stop [WITHSCORES]
# 按条件获取数据
zrangebyscore key min max [WITHSCORES] [LIMIT]
zrevrangebyscore key max min [WITHSCORES]
# 获取集合数据个数
zcard key
# 获取集合中在范围内的数量 按score
zcount key min max
# 获取数据对应的索引
zrank key value
zrevrank key value
# 获取/修改score值
zscore key value
zincrby key increment value
```

- **移除数据**

```bash
zrem key value1 [value2]
# 按条件删除数据
zremrangebyrank key start stop 
zremrangebyscore key min max
```

- **交集/并集/差集**

```bash
# 将key1 key2 的交集 存放在destination中
zinterstore destination numberkeys key1 [key2]
# key1 key2 的并集
zunionstore destination numberkeys key1 [key2]
```

## Redis 通用指令

###  key 的操作

- 基本操作

```bash
# 删除指定key
del key
# 判断key是否存在
exists key
# 获取key类型
type key
# 查询key
# 比如: keys * 获取所有key
keys pattern 
```

- 时效性

```bash
# 为指定的key设置有效期
expire key seconds
pexpire key milliseconds
expireat key timestamp
pexpireat key milliseconds-timestamp
# 获取key的有效期
ttl key
pttl key
# 设为永久有效
persist key
```

###  数据库的操作

> redis 为每个服务提供16个数据库，编号从0~15，每个数据库之间的数据相互独立

- 基本操作

```bash
# 切换数据库
select index
# 退出命令行
quit
# 测试服务器是否连通
ping 
# 数据移动
move key db
# 清空当前数据库
flushdb
# 清空所有数据
flushall
# 查看当前数据库数据使用量
dbsize
```