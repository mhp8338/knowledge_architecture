# Map Reduce概述

Map Reduce是一个分布式计算框架，用于编写批处理的应用程序。编写好的程序可以提交至Hadoop集群上用于并行处理大规模的数据集。

优点：

- 海量数据离线处理
- 易开发
- 易运行

缺点：实时流式计算



- map reduce将输入数据集拆分成独立的块，由map并行处理这些块 

- 框架对map的输出进行排序，输入至reduce中
- map reduce框架用于`<key,value>`键值对的处理，输入键值对，对应输出也是键值对
- `<key,value>`必须实现`writable`接口

# Map Reduce编程模型

map reduce的处理流程

![mapreduceProcess](/Users/xuepipi/Desktop/mapreduceProcess.png)

（1）`input`读取文件

（2）`splitting`将文件进行拆分，此时k1表示行数，v1表示行的内容

（3）`mapping`按照空格进行拆分，拆分得到`list(k2,v2)`，其中`k2`表示每个单词，`v2`表示词频（单个单词的词频：1）

（4）`shuffling`：`maping`操作在不同机器上并行处理，所以通过shuffle的方式将相同的`key`值数据分发到同一节点上合并，对应业务：`k2`对应某一个单词，`list(v2)`为可迭代的集合

（5）`reducing`：统计单词出现的次数，reducing对`list(v2)`归约操作，最终输出

## Map reduce 编程模型的核心思想

![image-20200308113451306](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200308113451306.png)

## Input format

- 对接`hdfs`
- 将文件拆分成多个`input split`
- 便于并行处理

## Reord Readers

- 对接`input format`

- 将`input split`转化成标准的`<key,value>`形式，以便于后续的`map`

  

