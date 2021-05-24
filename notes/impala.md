<div align="center"> <font size="6"> <b> Impala知识点总结 </b> </font></div>

---

## 目录
1. [为什么选择Impala](#为什么选择impala)
    + Impala在大数据生态系统中的地位
    + 大数据工作流程的灵活性
    + 高性能的分析
    + 探索商业智能
2. [启动和运行Impala](#启动和运行impala)
    + 安装
    + 连接到Impala
3. [数据库开发人员的Impala](#数据库开发人员的impala)


[//]: 分页

<div style="page-break-after: always;"></div>



### 为什么选择Impala

>   Impala使Hadoop的数据处理工作变得更快速简便，这些工作包括复制、转换或重组和分析数据文件，
>   并且可以可靠地、大规模地、跨网络机器并行处理这些事情。


+ Impala在大数据生态系统中的地位
    + 非常适合当前的大数据领域
    + 为ETL流程带来了高度灵活性，支持查询各种标准的Hadoop格式文件
    + 和其它Hadoop组件一样查询时无需转换和复制数据
    + 可以快速的预人类交互
    + C++、Java和Python可以通过JDBC和ODBC连接Impala
    + 支持传统业务工具，比如Tableau
    
+ 大数据工作流的灵活性
    + Impala集成了现有的hadoop组件、安全性、元数据、存储管理和文件格式，保留这些优点的同时，
    添加了使sql查询比以前更快的功能
    + Impala所使用的数据文件都是公开的、有文件记录的、可互操作的。如果想在使用其它Hadoop组件
    使用Impala时，无需复制或者转换数据。

+ 高性能的分析
    + 与将Hadoop数据分析视为需要大量规划和调度的批处理过程不同，不要在等待每个每个查询完成时
    在心里切换上下文，而是运行查询，并立即评估结果并对其进行微调。
    
+ 探索商业智能
    + 可以建立快速的ETL管道，接入其它数据源的数据。
    + 可以使用parquet存储格式，加快查询



### 启动和运行Impala

>   根据使用的Apache Hadoop的专业水平，以及拥有多少Hadoop基础设施，可以遵循不同的路径来尝试
>   Impala。

+ 安装
    + Cloudera现场演示
        + 最简单的方式是使用Cloudera Live demo。通过Hue web界面使用Impala Query Editor，可以从
        TPC-DS基准套件中查看一些示例表。
    + Cloudera 管理器和CDH 5
        + 对于更严格的测试或者大规模部署，可以下载并且安装Clouderea Impala 软件作为CDH5 发行版的
            一部分，并在真实的集群环境中使用它。
    
+ 连接到Impala
    + 无论以何种方式安装Impala，在进程结束时都会启动一些与Impala相关的守护进程。集群中的每个
    DataNode都有impalad，并且通常会在NameNode上存储目录和状态。Cloudera Manager的安装路径将
    这些守护进程捆绑在一起作为一个Impala服务，可以单独的启动和停止。
    + Impala查询引擎是完全分散的，可以连接到任何DataNode，发出查询，并且工作自动分布到整个集群。
    + 连接：impala-shell -i host:port
    

    
### 数据库开发人员的Impala

>   作为一个了解SQL的数据库开发人员，可以立即开始使用Impala。数据仓库专家应该已经熟悉分区的概念，
    如果之前处理过较小的OLTP分格数据库，那么强调大数据量将扩张视野。

+ SQL语言
    + 支持大部分的标准sql语句、数据类型
    + create table 和 insert 语句合并了一些格式句子，这些句子格式可能单独是数据加载程序的一部分。
    
+ 有限的DML
    + 由于Hadoop分布式文件系统针对批量插入和追加操作进行了优化，Impala目前没有OLTP风格的数据操作语言，
    如delete和update。（尽管这些语句用于Kudu表；）
    + Impala没有索引、约束和外键；尽量减少一些依赖，因为处理大量数据时，这些特性涉及到太多的性能开销。
    + 如果有新的原始数据，可以使用load data移动到impala表中；如果通过查询，可以使用insert into或者
    insert overwrite
    
+ 没有事务
    + Impala只是添加和替换；从不更新现有数据。在写操作中，Impal不是一次处理一行，而是处理数百万行
    + 通过drop table 或者alter table一次性删除大组相关行。删除分区，如果出错，原始数据任然可以从
        HDFS中恢复
    + 数据发生变化时，集群中的所有Impala节点都会得到通知。
    + 当新的文件通过一些非Impala命令存储到一个Impala表的指令时，Impala refresh table_name语句
        就像传统的comont语句一样，导致Impala重新评估表中的数据。
        
+ 最近添加的
    + 添加复杂数据类型支持（struct、array和map），在Impala2.3中实现的。
    
+ HDFS块大小
    + 默认情况下，HDFS的数据块大小为128MB，所以小于128MB的表都可以认为很小。

+ Parquet文件格式
    + 默认块大小为256MB
    + 每个查询只读取查询中引用的列
    
