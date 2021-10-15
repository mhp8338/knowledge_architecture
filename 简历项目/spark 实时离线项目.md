# spark 实时离线项目

基于访问日志统计：离线 HBase

- 搜索

基于订单数据日志统计：实时 Redis

# 离线项目处理流程

数据采集：将外部数据采集至内部

- SDK ==> 日志 ==>Hadoop
- Server日志：flume logstash
- 数据库：sqoop 
- 采集的数据直接放至HDFS上，基于数据进行  

数据预处理/数据清洗：

加字段：脏乱数据 ===> 数据规整化 (RDD/DF/DS)

- IP解析城市，运营商
- 时间解析
- 按照指定的分隔符拆分

减字段：

- 删除不必要的字段

**（使用Spark）**

数据入库：把规整化的数据写入存储中，

- HIVE
- HBASE
- REDIS

rowkey设计：cf column 

HDFS ==> SPARK ==>HBASE

数据分析

- 出报表的核心所在
- 存储分析结果

HBASE ==> MR ==>业务分析 ==> DB

HBASE ==> HIVE/SPARK SQL ==> SQL ==> DB

数据展示

- 将分析所得到的数据进行可视化展示

- HUE、Zeppelin、Echarts 、自研

# 离线项目处理架构

![image-20200908100651574](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200908100651574.png)

# 实时项目处理

## 架构

![image-20200908105628462](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200908105628462.png)

离线项目使用的是访问日志，实时项目采用的是付费日志

spark streaming读取Kafka数据，通过fastjson将数据解析出来

# Kafka

## ack

`ack = 0`：生产者只要把消息发出去，就默认已经发送成功，**这种方式消息容易丢失**

`ack = 1`：只要partiantion的leader收到消息就默认已经发送成功，不管其他FOLLOWER有没有同步这个消息

`ack = all`：LEADER收到消息后，还必须要求ISR跟leader同步的follower把消息同步过去，才算成功

**ack = all 不代表数据一定不会丢失，因为数据仅存在leader中，若ISR中仅有一个leader，接受完就宕机，仍热无法保证**

# 硬件资源分配

共有6个节点，每个节点16个cores和64GB RAM

Core利用：

- 1GB内存和1个core用于操作系统和Hadoop的守护线程
- core的数量意味着一个executor**能同时执行的tasks数量**，core设置为5
- 每个节点可以跑（15 / 5）3个executor，6个节点将会有18个executors
- 其中一个被YARN的Application Master占用，可用17个executors

内存的利用：

- 每个executor将会有63 / 3 = 21 GB
- overhead memory : `max(384MB, 0.07*21GB(执行内存))=1.47GB`
- 每个`executor`的内存约等于19 GB

