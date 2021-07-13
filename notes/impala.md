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
3. [Impala的特点与结构](#impala的特点与结构)
    + SQL语言
    + 有限的DML
    + 没有事务
    + 最近添加的
    + HDFS块大小
    + Parquet文件格式
    + Impala核心组件
    + Impala高级概念
4. [Impala教程](#impala教程)
    + Impala SQL语言参考
5. [Impala的常见开发人员任务](#impala的常见开发人员任务)
    + Impala性能优化
    
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
    

    
### Impala的特点与结构

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
    
+ Impala核心组件
    + Impala守护进程
        + 安装在每个DataNode，由一个名为impalad的实际进程表示
        + 负责Impala-shell、API和其它通过ODBC/JDBC连接器或hue连接的第三方应用程序提交的查询
        + 查询可以提交给任何一个Impalad，该守护进程称为'coordinate node'，然后将数据分发到其它节点处理
        + 数据结果返回给'coordinate node'，可以根据需要将查询结果提交给其它节点
        
    + Impala statestore
        + 该节点检查每个Impala守护进程的健康状况，然后经常将每个Impala守护进程的健康状况发送给其它守护
            进程
        + 该节点可以在Impala服务器或者集群内任何其它节点运行的同一节点上运行
        + 该节点不影响Impala其它节点的正常执行，只是不健壮而已
    
    + Impala元数据和元存储
        + Impala使用传统的Mysql或者Postgresql数据库来存储表定义
        + 如果元数据存储在metastore中，hive的云存储也使用相同的数据库，那么hive创建和加载的
            表的所有列都使用支持的数据类型、数据格式和数据压缩，Impala也可以使用这些表
        + 每个Impala节点在本地缓存所有的云数据
        + 当数据文件添加到现有表时，Impala使用refresh，当新表或删除表或删除数据文件或者启动DFS重均衡
            操作来均衡HDFS中的数据块时，使用invalidate matadata
 
 + Impala高级概念
    + MapReduce框架执行SQL语句性能低下的原因主要是磁盘访问，Impala使用内存处理数据
    + Impala与Hive的区别
        + Impala比Hive跟耗内存
        + Impala使用MPP计算框架，Hive使用MapReduce计算框架
        + Hive支持UDF
        + Impala依赖Hive的元存储
        + Impala查询设计HiveQL的子集
    + Impala为什么比Hive快
        + 在处理SQL查询时，不会将中间结果写入磁盘，在内存中完成完整的SQL处理
        + 比较MapReduce，MPP立即执行查询
        + 比较MapReduce，Impala Query Planer使用智能算法在并行节点的多个阶段执行查询，
            避免排序和shuffle步骤
        + Impala拥有HDFS中每个数据块的信息，利用这些信息可将查询更均匀的分布在所有DataNode
        + Impala将每个查询生成汇编级代码

       
### Impala教程

[//]: 官方文档：https://impala.apache.org/docs/build/html/index.html

+ Impala SQL语言参考
    + 基本数据类型
        ```sql
        boolean
        tinyint
        smallint
        int
        bigint
        decimal
        float
        double
        rela
        char
        varchar
        string
        date
        timestamp
        ```

    + 复合数据类型
        ```sql
        --创建数组
        CREATE TABLE contacts
        (
            name STRING
            , address STRING
            , phone ARRAY <STRING>
        )
        STORED AS PARQUET;
        DESCRIBE contacts;
        +---------+---------------+---------+
        | name    | type          | comment |
        +---------+---------------+---------+
        | name    | string        |         |
        | address | string        |         |
        | phone   | array<string> |         |
        +---------+---------------+---------+
        DESCRIBE contacts.phone;
        +------+--------+---------+
        | name | type   | comment |
        +------+--------+---------+
        | item | string |         |
        | pos  | bigint |         |
        +------+--------+---------+
        --数组类型特殊的两列，列名称固定
        --查询数组中数据，使用join
        SELECT name, address, a.item, a.pos
        FROM contacts c, c.phone a
        WHERE
            name LIKE 'John %'
            AND a.item LIKE '416-%'
            AND a.pos BETWEEN 0 AND 5
        ORDER BY a.pos;
        
        --创建字典
        CREATE TABLE animals
        (
            name STRING
            , aspect MAP<STRING, STRING>
        )
        STORED AS PARQUET;
      
        DESCRIBE animals;
        +--------+--------------------+---------+
        | name   | type               | comment |
        +--------+--------------------+---------+
        | name   | string             |         |
        | aspect | map<string,string> |         |
        +--------+--------------------+---------+
      
        DESCRIBE animals.aspect;
        +-------+--------+---------+
        | name  | type   | comment |
        +-------+--------+---------+
        | key   | string |         |
        | value | string |         |
        +-------+--------+---------+
        --字典特殊的两列，名称固定
        SELECT name, m.key, m.value
            FROM animals a, a.aspect m
            WHERE key IN ('Phylum', 'Class', 'Color', 'Legs', 'Hairless')
        ORDER BY name, m.key;
        
        --创建结构体
        CREATE TABLE RELATIONSHIPS
        (
            id BIGINT
            , person STRUCT <name: STRING, address: STRING>
            , spouse STRUCT <name: STRING, years_married: INT>
            , pet STRUCT <name: STRING, species: STRING, friendly: BOOLEAN>
        )
        STORED AS PARQUET;
        
        DESCRIBE relationships;
        +--------+---------------------+---------+
        | name   | type                | comment |
        +--------+---------------------+---------+
        | id     | bigint              |         |
        | person | struct<             |         |
        |        | name:string,        |         |
        |        | address:string      |         |
        |        | >                   |         |
        | spouse | struct<             |         |
        |        | name:string,        |         |
        |        | years_married:int   |         |
        |        | >                   |         |
        | pet    | struct<             |         |
        |        | name:string,        |         |
        |        | species:string,     |         |
        |        | friendly:boolean    |         |
        |        | >                   |         |
        +--------+---------------------+---------+
      
        DESCRIBE relationships.pet;
        +----------+---------+---------+
        | name     | type    | comment |
        +----------+---------+---------+
        | name     | string  |         |
        | species  | string  |         |
        | friendly | boolean |         |
        +----------+---------+---------+     
        --每个结构体不同的列名称
        
        --查询结构体列直接使用结构体名称
        SELECT id, person.name, pet.name, pet.species
            FROM relationships
        WHERE
             pet.species IN ('Dog','Cat')
             AND pet.friendly = true
             AND spouse.years_married < 5;    
      
        --组合数据类型
        CREATE TABLE array_of_structs
        (
            family_id INT
            , person ARRAY <STRUCT <name: STRING
                                    ,height: INT
                                    ,weight: INT
                                    ,eye_color: STRING
                                   >
                            >
        )
        STORED AS PARQUET;
        --item列可以省略
        SELECT a.family_id, p.name, p.height, p.weight, p.item.eye_color
            FROM array_of_structs a, a.person p
        
        --使用Hive的collect_list()和named_struct()函数分别构造数组和结构体对象，然后插入数据
        ```
    
    + SQL语句
        ```sql
        --创建数据库
        create (database | schema) [if not exists] database_name [comment 'database_comment']
        [location hdfs_path];
        
        --创建普通表
        CREATE [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
            (col_name data_type
            [constraint_specification]
            [COMMENT 'col_comment']
            [, ...]
            )
        [PARTITIONED BY (col_name data_type [COMMENT 'col_comment'], ...)]
        [SORT BY ([column [, column ...]])]
        [COMMENT 'table_comment']
        [ROW FORMAT row_format]
        [WITH SERDEPROPERTIES ('key1'='value1', 'key2'='value2', ...)]
        [STORED AS file_format]
        [LOCATION 'hdfs_path']
        [CACHED IN 'pool_name' [WITH REPLICATION = integer] | UNCACHED]
        [TBLPROPERTIES ('key1'='value1', 'key2'='value2', ...)];
        --create external table语句相当于一个符合链接，将Impala指向一个满是HDFS文件的目录，
        --数据文件不会被移动或者更改。如果删除该表，文件将保持不变
      
        --创建kudu表
        CREATE TABLE [IF NOT EXISTS] [db_name.]table_name
            (col_name data_type
            [kudu_column_attribute ...]
            [COMMENT 'col_comment']
            [, ...]
            [PRIMARY KEY (col_name[, ...])]
            )
        [PARTITION BY kudu_partition_clause]
        [COMMENT 'table_comment']
        STORED AS KUDU
        [TBLPROPERTIES ('key1'='value1', 'key2'='value2', ...)]
        
        --创建试图
        CREATE VIEW [IF NOT EXISTS] view_name
        [(column_name [COMMENT 'column_comment'][, ...])]
        [COMMENT 'view_comment']
        AS select_statement
        
        --执行计划
        [localhost:21000] > set explain_level=0;
        [localhost:21000] > explain select one.*, two.*, three.*
                          > from t1 one join [shuffle] t1 two join t1 three
                          > where one.x = two.x and two.x = three.x;
        +---------------------------------------------------------+
        | Explain String                                          |
        +---------------------------------------------------------+
        | Estimated Per-Host Requirements: Memory=4.00GB VCores=3 |
        |                                                         |
        | 08:EXCHANGE [PARTITION=UNPARTITIONED]                   |
        | 04:HASH JOIN [INNER JOIN, BROADCAST]                    |
        | |--07:EXCHANGE [BROADCAST]                              |
        | |  02:SCAN HDFS [explain_plan.t1 three]                 |
        | 03:HASH JOIN [INNER JOIN, PARTITIONED]                  |
        | |--06:EXCHANGE [PARTITION=HASH(two.x)]                  |
        | |  01:SCAN HDFS [explain_plan.t1 two]                   |
        | 05:EXCHANGE [PARTITION=HASH(one.x)]                     |
        | 00:SCAN HDFS [explain_plan.t1 one]                      |
        +---------------------------------------------------------+
        
        --describe声明
        DESCRIBE [DATABASE] [FORMATTED|EXTENDED] object_name
        object_name ::= [db_name.]table_name[.complex_col_name ...] | db_name
        
        --刷新元数据
        INVALIDATE METADATA并且REFRESH是对应的：
            + INVALIDATE METADATA是一种异步操作，它简单地从目录和协调器缓存中丢弃加载的元数据。该操作之后，目录和所有Impala协调器只知
                道数据库和表的存在，仅此而已。表的元数据加载由任何后续查询触发。
            + REFRESH同步重新加载元数据。 REFRESH比在表失效后执行完整元数据加载更轻量级。REFRESH无法检测由 HDFS 平衡器等操作触发的块位
                置更改，因此会在查询执行期间导致远程读取，对性能产生负面影响。
        
        --将文件复制到HDFS文件系统中，并加载到Impala表
        --加载数据
        hadoop fs -mkdir /user/hive/staging
        hadoop fs -put csv.txt /user/hive/staging
        impala-shell
        create database food_colors;
        use food_colors;
        create table food_data
        (id int, color string, food string, weight float)
        row format delimited fields terminated by ',';
        load data inpath '/user/hive/staging' into table food_data;
        --查看表所在目录
        hadoop fs -ls -R /user/hing/warehouse/food_colors.db
      
        --show语句
        SHOW DATABASES [[LIKE] 'pattern']
        SHOW SCHEMAS [[LIKE] 'pattern'] - an alias for SHOW DATABASES
        SHOW TABLES [IN database_name] [[LIKE] 'pattern']
        SHOW [AGGREGATE | ANALYTIC] FUNCTIONS [IN database_name] [[LIKE] 'pattern']
        SHOW CREATE TABLE [database_name].table_name
        SHOW CREATE VIEW [database_name].view_name
        SHOW TABLE STATS [database_name.]table_name
        SHOW COLUMN STATS [database_name.]table_name
        SHOW PARTITIONS [database_name.]table_name
        SHOW [RANGE] PARTITIONS [database_name.]table_name
        SHOW FILES IN [database_name.]table_name [PARTITION (key_col_expression [, key_col_expression]]
        
        SHOW ROLES
        SHOW CURRENT ROLES
        SHOW ROLE GRANT GROUP group_name
        
        SHOW GRANT USER user_name ON SERVER
        SHOW GRANT USER user_name ON DATABASE database_name
        SHOW GRANT USER user_name ON TABLE database_name.table_name
        SHOW GRANT USER user_name ON URI uri
        SHOW GRANT USER user_name ON COLUMN database_name.table_name.column_name
        SHOW FUNCTIONS IN _IMPALA_BUILTINS
        
        --清空数据
        trancate [table] [if exists] [db_name.]table_name
        ```


### Impala的常见开发人员任务


+ Impala性能优化
    + Impala存储层的调优
        + 为数据选择合适的文件格式：对于大量数据使用parquet格式最好
        + 避免产生许多小文件的数据摄取过程：尽量不使用insert values插入大量数据或
            关键性能表，因为每个这样的语句都会产生一个单独的小数据文件
        + 根据实际数据量选择分区粒度：选择在每个分区中至少放入256MB的数据，将表中的分区数据
            保持在3万以下，如果要进一步降低粒度，可以考虑分桶技术
        + 对分区键列使用最小的适当的整数类型：使用包含适当值范围的最小整数类型
        + 选择合适的Parquet块大小：不同版本impala默认值不一样，需要在“许多小文件”和“单个
            巨型文件”之间找到平衡批量I/O和并行处理的最佳块大小
        + 收集性能关键或大容量连接查询中使用的所有表的统计信息：compute stats
        
    + Impala连接查询的调优
        + 使用compute stats语句收集表的统计信息
        + 统计信息不可用时，使用STRAIGHT_JOIN关键字使查询按顺序连接，表的顺序使用启发式方法，
            最大的表最前，后面从小到大
        + 合理使用连接机制，一个大表，一个小表，用广播机制，两个大表用分区连接机制
        
    + compute stats
        + 对于非常大的表，使用采样和外推，启用ALTER TABLE mytable test_table SET TBLPROPERTIES("impala.enable.stats.extrapolation"="true"
            + 采样：COMPUTE STATS test_table TABLESAMPLE SYSTEM(10)
            + 外推：启用统计信息外推会改变 COMPUTE STATS 的行为，以及表扫描的基数估计
        + 分区表：COMPUTE INCREMENTAL STATS
        + 可手动更改统计信息