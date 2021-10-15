# HDFS 初入江湖

**HDFS** （**Hadoop Distributed File System**）是 Hadoop 下的分布式文件系统，具有高容错、高吞吐量等特性，可以部署在低成本的硬件上。

# 分布式文件系统

## 传统文件系统

Linux、Windows、Mac：

- 根目录：/  C 
- 存放的是文件或者文件夹
- 对外提供服务：创建、修改、删除、查看、移动等等

## 普通文件系统与分布式文件系统的比较

| 普通文件系统 | 分布式文件系统  |
| ------------ | --------------- |
| 单机操作     | 能够横跨N个机器 |

# HDFS的前提和设计目标

## Hardware Failure 

硬件的故障，是一种常见异常，为了减少该异常的影响（也就是容错），每个机器只存储文件的一部分，即一个block。**将一个文件拆分为多个block，然后分别打入不同的机器中**。

## Streaming Data Access

> The emphasis is on high throughput of data access rather than low latency of data access.

高吞吐量而不是低延时——**不适合做实时处理**

## Large Data Sets

支持大规模数据存储，不怕数据大，怕数据小

## Moving Computation is Cheaper than Moving Data

移动数据成本高 ，而移动计算省掉很多资源

# HDFS的架构

主从架构：

- NameNode(master) and DataNodes(slave)

- master/slave architecture

| NameNode                                     | DataNodes    |
| -------------------------------------------- | ------------ |
| master                                       | slave        |
| name space（记录对名称和其他属性的任何更改） | storage      |
| 提供客户端对文件的访问                       | 负责存储文件 |

> HDFS exposes a file system namespace and allows user data to be stored in files.

## 文件系统命名空间Name Space

> HDFS supports a traditional hierarchical file organization. A user or an application can create directories and store files inside these directories. The file system namespace hierarchy is similar to most other existing file systems; one can create and remove files, move a file from one directory to another, or rename a file. HDFS supports user quotas and access permissions. HDFS does not support hard links or soft links. However, the HDFS architecture does not preclude implementing these features.

- 与Linux类似
- 目录和文件的创建、移动、删除和重命名等操作，支持配置用户和访问权限
- **不支持硬链接和软链接**

`NameNode` 负责维护文件系统名称空间，记录对名称空间或其属性的任何更改。

## 内部实现

1.  A file is split into one or more blocks:
    `blocksize: 128M`
   `150M -> 128M 22M`
2. these blocks are stored in a set of DataNodes:只为容错啊！
3. The NameNode executes file system namespace operations like opening, closing, and renaming files and directories :CRUD
4. determines the mapping of blocks to DataNodes：存放数据的具体位置信息，例如

| a.txt | blocksize | replication |
| ----- | --------- | ----------- |
| 150M  | 128M      | 1           |

针对`a.txt`，namenode 存储如下关系：

| Block1             | Block2            |
| ------------------ | ----------------- |
| 128M 192.168.199.1 | 22M 192.168.199.2 |

获取a.txt所经历的过程如下图，**用户对该过程不感知**
![NameNode and DataNodes](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsarchitecture.png "NameNode and DataNodes")

> 通常情况下，一个node部署一个机器，但也可以一台机器部署多个node

## 副本机制Data Replication

>HDFS is designed to reliably store very large files across machines in a large cluster. It stores each file as a sequence of blocks. The blocks of a file are replicated for fault tolerance. The block size and replication factor are configurable per file.

- 为保证容错性，HDFS提供了副本机制
- 将一个文件分成多个块，每个块建立多个副本来保证容错
- 块大小和副本因子可以自行配置



![replication](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsdatanodes.png "replication")

## 副本摆放策略

知识补充：

- 实例分布在多个机架的多台服务器上，不同机架上的两台服务器通过交换机通讯
- 同一机架内的节点通信方便，不需要交换机
- 同一机架上服务器之间的带宽大于不同机架上服务器之间的带宽
- 若将所有节点放入同一机架，则容错性将变得很差

机架感知摆放策略（复制因子等于3）：

- 副本一 => 本rack的一个节点上
- 副本二 => 另外一个rack的节点上
- 副本三 => 与副本二相同rack的另外一个节点上

策略（复制因子大于3）：

- 随机放置其他副本
- 每个机架的副本数不高于上限
- 不允许同一个 `dataNode` 上具有同一个块的多个副本

> 副本容错性较强，三个副本在两个rack上，提高读写效率



## 副本的选择

- 最大限度的减少带宽消耗和读取延迟
- 优先选择距离读取器最近的副本
- 优选选取本地数据中心的副本
- 优先选取同机架上的副本

## 架构的稳定性

### 心跳机制

- datanode向namenode定期发送心跳信息
- 超过时间未发送，则认定datanode已经死亡
- 任何io请求都不会转发给这个死掉的datanode
- datanode死亡会导致一些文件块的副本缺失，namenode会他重新复制

### 数据的完整性

- 针对datanode文件块损坏而制定的机制
- 设置校验和（是否损坏的标志），存储至相同hdfs命名空间下的单独隐藏文件夹中
- 用户读取数据时，验证接收数据的校验和是否与关联校验和文件中的校验和匹配
- 不匹配去其他的datanode上找副本

### 元数据的磁盘故障@CheckPoint

- 针对namenode而制定，`fsimage`和`editlog`是namenode核心数据
- 可设置配置文件支持`fsimage`和`editlog`多副本同步
- `fsimage`和`editlog`任何改变都能同步更新

### 快照

- 设定特定的时刻存储数据副本
- 有问题可回滚至健康状态

# HDFS的特点

[HDFS(Hadoop Distributed File System)](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html "HDFS(Hadoop Distributed File System)")具有以下特点：

 - 分布式

 - commodity hardware(可运行于廉价的硬件上)
 - fault-tolerant (具有容错性)：数据副本机制
 - high throughout (高吞吐量)：支持高吞吐量访问，不支持低延迟
 - large data sets (大文件)： GB级以上
 - 简单一致性模型
    - 支持一次写入多次读取
    - 可将内容追加到文件末尾
    - 不支持文件内容随机访问，不能从文件任意位置新增数据
- 跨平台移植：其他大数据框架都能将其作为数据持久化存储的首选方案

# HDFS的底层实现

## HDFS写数据的流程

```shell
hadoop fs -put jdk.tar.gz /jdk.tar.gz
```

两个block，三个副本

![image-20200304102408952](https://tva1.sinaimg.cn/large/00831rSTly1gcmbdotd80j30pg0bljtd.jpg)

(1)写数据请求

(2)OK

(3)写blk1，3副本

(4)你可以往dn1,dn2,dn3写

(5)找dn1，建立连接

(6)dn2建立连接

(7)dn3建立连接

(8)dn3 ok 

(9)dn2 ok

(10)dn1 ok

(11)传输blk1

## HDFS读数据的流程

```shell
hadoop fs -get /jdk.tar.gz
```

![image-20200304105716730](https://tva1.sinaimg.cn/large/00831rSTly1gchprus153j30po08w3zx.jpg)

（1）读取数据请求

（2）读吧，blk1:dn1,dn2,dn3；blk2:dn2,dn3,dn4

（3）好勒，发送dn1读取数据blk1请求

（4）dn1：给你老铁

（5）谢啦胸弟，发送dn2读取数据blk2请求

（6）dn2：给你老铁

## HDFS CheckPoint

### HDFS元数据管理

元数据：HDFS的目录结构以及每个文件的block信息（id、副本系数、block存放在哪个dn上）

存放在什么地方？

-  `fsimage`：对应配置${hadoop.tmp.dir}/name/...

- 元数据存放在内存中
- 当nn宕机时，采用checkpoint恢复nn所存放的元数据信息

![image-20200308090717534](https://tva1.sinaimg.cn/large/00831rSTly1gcmbds2oorj30ks09h0tu.jpg)

（1）定期将内存中的目录结构和文件块的block信息存储至fsimage快照中（例如两天存储一次）

（2）当运行一天半时，不足以存储，这部分信息需要记录所有与hdfs相关的操作指令edit blog

（3）fsimage备份到second name node上，以便恢复（替nn减少负担）

（4）操作日志也同样备份至second nn 上

（5）加载磁盘文件到内存（反序列化）

（6）将edits中记录的所有对hdfs的操作更新到内存中

（7）生成newfsimage副本

（8）替换原来的`fsimage`

最终利用新的`fsimage`恢复数据

## HDFS SafeMode

Name node 启动后将进入safemode状态：

- 此状态下副本（复制）将不会进行，无法进行数据的写操作
- 在此期间，Name node将接受dn的心跳，人为健康（高于最低的安全系数），此时就可进行副本的写操作了
- 这个动作一般持续30s