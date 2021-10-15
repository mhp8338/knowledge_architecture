

# Redis 概述

> 官网：[https://redis.io/](https://redis.io/)

RDBMS：mysql、oracle、db2、sql server

两个方面：read & write



NoSQL: HBase Redis ... 数据库

Redis 是一个高性能的`key-value`数据库



# Redis诞生的业务场景

大负荷、大负载的应用，不管如何优化，在RDBMS中都很难达到预想的性能指标

==>衍生出基于内存的数据库——Redis



- 成熟
- 社区大、使用者多
- 在特定场景查效率高（`在N多条记录中根据条件去快速查找某一条/几条记录`）



面向`key-value`对的数据类型的内存数据库，read/write

key类型：`string`

value类型：

| value    | 值类型     | 特点                        |
| -------- | ---------- | --------------------------- |
| string   | 字符串     |                             |
| hash     | 哈希       |                             |
| list     | 字符串列表 | 可重复、有序、通过index访问 |
| set      | 字符串集合 | 不可重复、无序              |
| sort set | 有序集合   | 不可重复、有序              |
| ...      |            |                             |

# Redis特性

- 速度快：10w `QPS`		数据存储方式：内存（对比HDFS NN）	一个Redis一个单线程

- 持久化：内存中的数据每隔一段时间就会保存到磁盘中，保存方式：
  - RDB：快照	周期性保存快照

  - AOF：日志    写操作的命令写值日志文件中

    同时使用`RDB`和`AOF`：优先`AOF`

- 多种数据结构
- 支持多种编程语言：`java` `php` `python` `ruby` `lua` `nodejs`
- 丰富的功能：发布订阅、lua脚本、事务、pipeline（支持一次执行多条语句）
- 简单易用：不依赖外部库、单线程
- 主从复制：Redis-HA
- 高可用和分布式：Redis-Sentinel(2.8+) Redis-Cluster(3.x)



# Redis应用场景

- 缓存场景`hashmap(key,value)`

  ​	用户 --->客户端--->Redis---DB

- 计数器：评论数、点赞数`atomeic increment` 设置过期

- 消息队列：kafka

- 排行榜：有序集合

- 社交

- 过期处理

# Redis 部署

- 前置安装：

  `yum * `

- 下载
- 解压
- 编译：`make`  `sudo make install`
- 修改配置文件：`redis.conf`
  - deamonize yes
  - logfile " /home/***/tmp/redis/redis.log"
- 启动Redis：`src/redis-server redis.conf [--port 63]`
- 启动客户端：`src/redis-cli [-h 127.0.0.1] [-p 6379]`
- 退出Redis三种方式:
  - `quit` `exit`
  - `src/redis-cli shutdown `
  - `kill -9 38495`

# Redis 多数据库的特性

MySQL：手工创建多个数据库 

```sql
CREATE DATABASE XX;
```

Redis：

- 支持多个数据库，但数据库不需要手工创建，Redis启动时会自动创建

- 以编号0递增，默认16个数据库，可以通过`redis.conf`配置文件进行修改

- `redis-cli`默认选择第0号数据库

- ```sql
  SELECT dbid;
  ```

  选择数据库，类似于`mysql`中的use

- 多个数据库一般情况下操作相互隔离

- `flushall`操作不隔离，清空所有数据库，**慎用**



# Redis基础命令

查询符合规则的`key`名称：`key` 表达式`?,*,[]`

判断一个key是否存在：`exists key`

删除`key`：`del key1 key2`

批量删除：

获取key的类型：`type user` 

`help`：查询redis命令的用法`help @<group>`

# Redis常用的数据类型

## Redis数据类型之String

### 常用命令

文本、数字、二进制

`help xxx`

`set(setnx)/get/mset/mget`

`incr/decr/incrby/decrby/incrbyfloat`

`append/strlen/getrange`

## Redis数据类型之List

有序的方式存储多个值，**值可重复，且不唯一**

list：一个或者多个`item`

Index：0 n-1



应用：消息队列

### 常用命令

`help @list`

`lpush/rpush/lpop/rpop`

`llen/lrange`

`lset`

`lindex`

`ltrim`

`linsert`

`rpoplpush`

`lrem`





# Redis数据类型之Set

**元素不重复，无序**

### 常用命令

`help @set`

`sadd/smembers/srem/sismember`

`sdiff/sinter/sunion`

`sdifstore`

`scard`

`srandmember key [count]`



应用：

`weibo/twitter`：共同关注

# Redis api操作

连接池API