# Django两种开发方式的分享

首先明确：**前端页面看到的数据均由后端传出**，而目前存在两种开发方式：纯后端开发（前后端不分离）开发，前后端分离式开发

## 纯后端（前后端不分离）

数据交互如图

<img src="https://i.loli.net/2020/10/21/KiH5J6SpIoXAedF.png" alt="image-20201021083855556" style="zoom: 67%;" />

**前后端分不离的系统，没有前端，浏览器所看到的页面由后端提供的，相当于没有前端**

## 前后端分离

主要利用DRF包（Django REST framework），数据交互如图

<img src="https://i.loli.net/2020/10/21/1XiIgjUhSHFxZmf.png" alt="image-20201021094724368" style="zoom:67%;" />

该应用模式下，**后端仅返回前端所需的数据（一般式JSON），不再渲染HTML页面，不再控制前端的效果**。 至于在前后端分离的应用模式中 ，前端与后端的耦合度相对较低，通常将后端开发的每个视图都称为一个接口，或者API，**前端通过访问接口来对数据进行增删改查**。

## 优劣分析

纯后端：

| 优点                                                         | 缺点                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **开发过程简单**：不需要MOCK服务端返回数据的响应             | **耦合度较高**：前端页面都是由后端渲染页面或重定向， 也就是后端需要控制前端的展示，开发者需要兼顾前后端开发 |
| **DEBUG方便**：可直接利用编辑器（例如pycharm）DEBUG，数据流动非常清晰，充分利用编辑器的调试便利 | **对于交互实时性的需求不友好**：例如Django写一个评论功能，需要用Django的`JS`操纵库直接操纵DOM（相当于利用直接利用JS写页面） |
| **部署容易**：仅需要构建后端包                               | **`template>>HTML`文件比较乱**：HTML与CSS、JS混在一起，当系统大的时候不易维护 |

前后端分离：

| 优点                     | 缺点                                 |
| ------------------------ | ------------------------------------ |
| 松耦合                   | 开发复杂：要兼顾前后端的开发         |
| 能快速解决一些复杂的需求 | DEBUG困难：需要打印日志              |
| 文件条理清晰分工明确     | 部署较于前者复杂：需要前后端分别部署 |

不分离开发——前端代码

```javascript

<!DOCTYPE html>
<html>
<body>
<p>请输入两个数字</p>
<form action="/oneapp/add/" method="get">
    a: <input type="text" id="a" name="a"> <br>
    b: <input type="text" id="b" name="b"> <br>
    <p>result: <span id='result'></span></p>
    <button type="button" id='sum'>提交</button>
</form>
 
<script src="http://apps.bdimg.com/libs/jquery/1.11.1/jquery.min.js"></script>
<script>
    $(document).ready(function(){
      $("#sum").click(function(){
        var a = $("#a").val();
        var b = $("#b").val();
 
        $.get("/oneapp/add/",{'a':a,'b':b}, function(ret){
            $('#result').html(ret)
        })
      });
    });
</script>
</body>
</html>
```

分离式开发——前端代码（VUE）

```vue
  
<template>
    <div>
        <!--面包屑导航区-->
        <el-breadcrumb separator-class="el-icon-arrow-right">
            <el-breadcrumb-item :to="{ path: '/home' }">首页</el-breadcrumb-item>
            <el-breadcrumb-item>用户管理</el-breadcrumb-item>
            <el-breadcrumb-item>用户列表</el-breadcrumb-item>
        </el-breadcrumb>
        <!--卡片视图区-->
        <el-card>
            <!--搜索与添加区域-->
            <el-row :gutter="20">
                <el-col :span="8">
                    <el-input placeholder="请输入内容" v-model="queryInfo.query" :clearable="true" @clear="getUserList">
                        <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
                    </el-input>
                </el-col>
                <el-col :span="4">
                    <el-button type="primary" @click="addDialogVisible=true">添加用户</el-button>
                </el-col>
            </el-row>
</template>

<script>
    export default {
        data() {
            /*验证邮箱*/
            var checkEmail = (rule, value, cb) => {
                const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/
                if (regEmail.test(value)) {
                    return cb()
                }
                cb(new Error('请输入合法的邮箱'))
            }
            
</script>

<style lang="less" scoped>
</style>
```

# 前端框架介绍

## WHY

封装：将一些底层原理封装到框架中，不需要直接操纵dom，由框架操纵

生态：存在成熟的社区，以及**前端UI框架**（可选择框架比较多），加快开发速率

易维护：组件式Component编程，组件之间的解耦

声明式编程：传统前端（JQuery...）是命令式编程，涉及到编码目的有两种：对状态的判断，对DOM的操作；而声明式编程**通过描述状态与视图之间的映射关系**，然后通过这样的一个映射关系来操作DOM，可以说用这样的映射关系来生成一个DOM节点插入到页面去，从逻辑上只需要考虑状态即可，不需要考虑DOM怎么操作，可以大大降低前端编码的难度

## WHAT

目前大厂常见的框架：VUE，Angular，React

### VUE

**优点：**

- 比react更轻量
- 渲染效率更高
- 文档健全，文档形式更友好（国人开发的前端框架）
- 适用于简单的页面开发

**缺点：**

- 相对于angular，历史不久的Vue，部分功能还不够完善
- 支持的库和拓展的丰富性还有待提升
- 浏览器兼容性问题：同时对于**老浏览器的支持也不太好**

### Angular

**优点：**Angular是一个比较完善的前端框架，数据双向绑定、模块化、路由、过滤器、依赖注入等功能相当完整，同时模板功能强大，自带丰富的指令，易于操作。思想、语法跟JAVA类似，有JAVA基础比较好上手。

**缺点：**

- 初始渲染慢
- 版本兼容性差
- 整体比较重
- 官方文档不全

### React

**优点：**相比于Angular更轻量，更新DOM次数少（优化做的较好），速度较快，采用声明式设计，可以轻松描述应用，更加灵活，也能和已知的框架

或库很好的配合。

**缺点：**

- 缺少大项目的实际应用
- 一些功能存在BUG
- ReactJS与服务器通信时需要复杂的异步编程

# 常用的UI模板

UI模板实际上是一些大公司制作的前端常用组件，可以直接使用，大大缩短前端开发周期，一般是与前端框架配套使用，当然Django也存在可以直接使用的UI模板（只是很少）

下边介绍的只涉及PC端的UI，实际上还存在许多移动端的UI

## Django前后端不分离的UI

### ACE UI框架

Github：https://github.com/bopoda/ace

demo：http://active-themes.com/ace/demo/html/calendar.html



## 前端框架UI

### Ant Design UI 框架 

官网地址: [ant.design/index-cn](https://ant.design/index-cn)

Github: [github.com/ant-design](https://github.com/ant-design)

Demo：https://preview.pro.ant.design/dashboard/analysis

### Element UI 框架

官网地址: [element.eleme.cn/#/zh-CN](https://element.eleme.cn/#/zh-CN)

Github: [github.com/elemefe](https://github.com/elemefe)

### Vant UI 框架

官网地址: [youzan.github.io/vant/#/zh-C…](https://youzan.github.io/vant/#/zh-CN/)

Github: [github.com/youzan](https://github.com/youzan)

### iView UI 框架

官网地址: [www.iviewui.com/](https://www.iviewui.com/)

Github: [github.com/TalkingData](https://github.com/TalkingData)

### Muse UI 框架

官网地址: [muse-ui.org/#/zh-CN](https://muse-ui.org/#/zh-CN)

Github: [github.com/museui](https://github.com/museui)

# 数据大屏

## VUE

DataV-team（Github）： https://github.com/DataV-Team/DataV

gitee：https://gitee.com/MTrun/big-screen-vue-datav

## React

gitee：https://gitee.com/MTrun/react-big-screen

## 原生JS

iDataV：https://github.com/yyhsong/iDataV

## 其他

腾讯云图（付费）

阿里云（付费）

百度surge（付费）

# 时间序列数据库

## **Timescale** 

这个数据库其实就是一个基于传统[关系型数据库](https://cloud.tencent.com/product/cdb-overview?from=10680)[postgresql](https://cloud.tencent.com/product/postgresql?from=10680)改造的时间序列数据库。了解postgresql的同学都知道，postgresql是一个强大的，开源的，可扩展性特别强的一个数据库系统。

于是timescale.inc在postgresql架构上开发了Timescale，一款兼容sql的时序数据库。作为一个postgresql的扩展提供服务。其特点如下：

**基础：**

- 支持所有PostgreSQL原生SQL，包含完整SQL接口（包括辅助索引，非时间聚合，子查询，JOIN，窗口函数）。
- 用PostgreSQL的客户端或工具，可以直接应用到该数据库，不需要更改。
- 时间为导向的特性，API功能和相应的优化。
- 可靠的数据存储。

**扩展：**

- 透明时间/空间分区，用于放大（单个节点）和扩展。
- 高数据写入速率（包括批量提交，内存中索引，事务支持，数据备份支持)。
- 单个节点上的大小合适的块（二维数据分区），以确保即使在大数据量时也可快速读取。
- 块之间和服务器之间的并行操作。

**劣势：**

- 因为TimescaleDB没有使用列存技术，它对时序数据的压缩效果不太好，压缩比最高在4X左右
- 目前暂时不完全支持分布式的扩展（正在开发相关功能），所以会对服务器单机性能要求较高

## **Influxdb** 

Influxdb是业界比较流行的一个时间序列数据库，特别是在IOT和监控领域十分常见。其使用go语言开发，突出特点是性能。**特性：**

- 高效的时间序列数据写入性能。自定义TSM引擎，快速数据写入和高效数据压缩。
- 无额外存储依赖。
- 简单，高性能的HTTP查询和写入API。
- 以插件方式支持许多不同协议的数据摄入，如：graphite，collectd，和openTSDB
- SQL-like查询语言，简化查询和聚合操作。
- 索引Tags，支持快速有效的查询时间序列。
- 保留策略有效去除过期数据。
- 连续查询自动计算聚合数据，使频繁查询更有效。

Influxdb已经将分布式版本转为闭源。所以在分布式集群这块是一个弱点，需要自己实现。

## **OpenTSDB** 

The Scalable Time Series Database. 打开OpenTSDB官网，第一眼看到的就是这句话。可见其将Scalable作为自己重要的”卖点“。OpenTSDB运行在Hadoop和HBase上，其充分利用HBase的特性。通过独立的Time Series Demon(TSD)提供服务，所以它可以通过增减服务节点来轻松扩缩容。

![OpenTSDB](https://gitee.com/ma-yunfei/picture/raw/master/img/OpenTSDB.png)

- Opentsdb是一个基于Hbase的时间序列数据库（新版也支持Cassandra）。 其基于Hbase的分布式列存储特性实现了数据高可用，高性能写的特性。受限于Hbase，存储空间较大，压缩不足。依赖整套HBase, ZooKeeper
- 采用无模式的tagset数据结构(sys.cpu.user 1436333416 23 host=web01 user=10001) 结构简单，多value查询不友好
- HTTP-DSL查询

## **Druid** 

Druid是一个实时在线分析系统(LOAP)。其架构融合了实时在线数据分析，全文检索系统和时间序列系统的特点，使其可以满足不同使用场景的数据存储。

- 采用列式存储：支持高效扫描和聚合，易于压缩数据。
- 可伸缩的分布式系统：Druid自身实现可伸缩，可容错的分布式集群架构。部署简单。
- 强大的并行能力：Druid各集群节点可以并行地提供查询服务。
- 实时和批量数据摄入：Druid可以实时摄入数据，如通过Kafka。也可以批量摄入数据，如通过Hadoop导入数据。
- 自恢复，自平衡，易于运维：Druid自身架构即实现了容错和高可用。不同的服务节点可以根据负载需求添加或减少节点。
- 容错架构，保证数据不丢失：Druid数据可以保留多副本。另外可以采用HDFS作为深度存储，来保证数据不丢失。
- 索引：Druid对String列实现反向编码和Bitmap索引，所以支持高效的filter和groupby。
- 基于时间分区：Druid对原始数据基于时间做分区存储，所以Druid对基于时间的范围查询将更高效。
- 自动预聚合：Druid支持在数据摄入期就对数据进行预聚合处理。

Druid架构蛮复杂的。其按功能将整个系统细分为多种服务，query、data、master不同职责的系统独立部署，对外提供统一的存储和查询服务。其以分布式集群服务的方式提供了一个底层数据存储的服务。

![Druid](https://gitee.com/ma-yunfei/picture/raw/master/img/Druid.jpeg)

Druid在架构上的设计很值得我们学习。如果你不仅仅对时间序列存储感兴趣，对分布式集群架构也有兴趣，不妨看看Druid的架构。另外Druid在segment(Druid的数据存储结构)的设计上也是一大亮点，即实现了列式存储，又实现了反向索引。

## **Elasticsearch** 

Elasticsearch 是一个分布式的开源搜索和分析引擎，适用于所有类型的数据，包括文本、数字、地理空间、结构化和非结构化数据。Elasticsearch 在 Apache Lucene 的基础上开发而成，由 Elasticsearch N.V.（即现在的 Elastic）于 2010 年首次发布。Elasticsearch 以其简单的 REST 风格 API、分布式特性、速度和可扩展性而闻名。

Elasticsearch以ELK stack被人所熟知。许多公司基于ELK搭建日志分析系统和实时搜索系统。之前我所在团队在ELK的基础上开始开发metric监控系统。即想到了使用Elasticsearch来存储时间序列数据库。对Elasticserach的mapping做相应的优化，使其更适合存储时间序列数据模型，收获了不错的效果，完全满足了业务的需求。后期发现Elasticsearch新版本竟然也开始发布Metrics组件和APM组件，并大量的推广其全文检索外，对时间序列的存储能力。真是和我们当时的想法不谋而合。

Elasticsearch的时序优化可以参考一下这篇文章:《elasticsearch-as-a-time-series-data-store》

也可以去了解一下Elasticsearch的Metric组件Elastic Metrics

## **Beringei** 

Beringei是Facebook在2017年最新开源的一个高性能内存时序数据存储引擎。其具有快速读写和高压缩比等特性。

2015年Facebook发表了一篇论文《Gorilla: A Fast, Scalable, In-Memory Time Series Database 》，Beringei正是基于此想法实现的一个时间序列数据库。

Beringei使用Delta-of-Delta算法存储数据，使用XOR编码压缩数值。使其可以用很少的内存即可存储下大量的数据。

## **如何选择一个适合自己的时间序列数据库** 

- Data model 时间序列数据模型一般有两种，一种无schema，具有多tag的模型，还有一种name、timestamp、value型。前者适合多值模式，对复杂业务模型更适合。后者更适合单维数据模型。
- Query language 目前大部分TSDB都支持基于HTTP的SQL-like查询。
- Reliability 可用性主要体现在系统的稳定高可用上，以及数据的高可用存储上。一个优秀的系统，应该有一个优雅而高可用的架构设计。简约而稳定。
- Performance 性能是我们必须考虑的因素。当我们开始考虑更细分领域的数据存储时，除了数据模型的需求之外，很大的原因都是通用的数据库系统在性能上无法满足我们的需求。大部分时间序列库倾向写多读少场景，用户需要平衡自身的需求。下面会有一份各库的性能对比，大家可以做一个参考。
- Ecosystem 我一直认为生态是我们选择一个开源组件必须认真考虑的一个问题。一个生态优秀的系统，使用的人多了，未被发现的坑也将少了。另外在使用中遇到问题，求助于社区，往往可以得到一些比较好的解决方案。另外好的生态，其周边边界系统将十分成熟，这让我们在对接其他系统时会有更多成熟的方案。
- Operational management 易于运维，易于操作。
- Company and support 一个系统其背后的支持公司也是比较重要的。背后有一个强大的公司或组织，这在项目可用性保证和后期维护更新上都会有较大的体验。

## **性能对比** 

|                    | Timescale | InfluxDB | OpenTSDB | Druid    | Elasticsearch | Beringei |
| :----------------- | :-------- | :------- | :------- | :------- | :------------ | :------- |
| write(single node) | 15K/sec   | 470k/sec | 32k/sec  | 25k/sec  | 30k/sec       | 10m/sec  |
| write(5 node)      |           |          | 128k/sec | 100k/sec | 120k/sec      |          |

## **总结** 

最后总结一下：

- 如果你想要一个极限性能的系统可以考虑Beringei和InfluxDB，在数据高可用方面，可以采用客户端双写模式来对数据做一个副本，保证数据的可用性。
- 如果你数据量不大，性能要求也不是特别高，却又点查询，删除和关联查询等需求，不妨考虑一下Timescale。
- 如果你间距索引和时间序列的需求。那么Druid和Elasticsearch是最好的选择。其性能都不差，并且都是高可用容错架构。

## **最后** 

之后我们可以来深入了解一两个TSDB，比如Influxdb，Druid，Elasticsearch等。并可以学习一下行存储与列存储的不同，LSM的实现原理，数值数据的压缩，MMap提升读写性能的知识等。