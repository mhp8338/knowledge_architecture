# HBase | 数据模型

![image-20200908091146842](/Users/xuepipi/Library/Application Support/typora-user-images/image-20200908091146842.png)

- RowKey：主键
- column family：列族：一系列列的集合
- Column : 列
- Cell : 存储单元

HBASE 中col and row 确定一个存储单元 

每个cell保存保存同一个数据的多个版本，

在写入数据时时间戳可以由HBASE自动赋值，也可以显式赋值

``{{rowkey,column,version}}==>HBASE中的一个cell``