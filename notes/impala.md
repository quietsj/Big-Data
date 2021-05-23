<div align="center"> <font size="6"> <b> Impala知识点总结 </b> </font></div>

---

## 目录
1. [为什么选择Impala](#第一章、为什么选择impala)
    + Impala在大数据生态系统中的地位
    + 大数据工作流程的灵活性
    + 
2. [启动和运行Impala](#第二章、启动和运行impala)

[//]: 分页

<div style="page-break-after: always;"></div>



### 第一章、为什么选择impala

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



### 第二章、启动和运行Impala

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
    
