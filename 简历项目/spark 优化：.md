简历

# spark 优化：

（1）cache RDD、DSTREAM（注意释放）

（2）

## Spark streaming

###  direactor & director

![image-20200909083401222](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200909083401222.png)

- 在executor上会有receiver从kafka接受数据，并存储在spark executor，到了一个batch会触发job区处理收到的数据，1个receiver占有一个core
- 为了不丢失数据需要定期开放WAL机制，将receiver数据备份到第三方系统上HDFS
- Receiver内部使用kafka high level api去消费数据并自动更新offset

![image-20200909084344972](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200909084344972.png)

- 没有receiver接受数据，不占用core。
- 定期查询Kafka中每个partition的最新的offset，每批次拉取上次处理的offset和当前查询的offset和当前查询的offset的范围进行处理
- 无需将数据备份，只需要手动保存offset
- 内部使用kafka simple Level API消费数据，需要手动维护offset，zk上不会自动更新offset

### 优化

把所有需要的数据规整为一个数据结构，避免多次取相同的数据，细粒度设置数据结构

# HIVE调优

（1）查询过程中先处理掉带null的数据

（2）**设置并行执行任务数**

`hive.exec.parallel 值为 true`

（3）JVM重用可以使得JVM实例在同一个job中重新使用N次

（4）**选择使用Tez引擎**，减少了Ｍap/Reduce之间的文件存储

（5）hive可以通过本地模式在单节点上处理所有任务，对于小数据量任务可以大大的缩短时间

`hive.exec.mode.local.auto=true`

（6）Hive提供了一种严格模式，可以防止用户执行那些可能产生意想不到的不好的影响查询

`Hive.mapred.mode=strict`

# HBase调优

- 不写WAL（预写日志），手动刷新memstore

