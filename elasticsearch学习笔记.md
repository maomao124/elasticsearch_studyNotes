# --elasticsearch学习笔记--



----





# 数据库做搜索弊端

站内搜索（垂直搜索）：数据量小，简单搜索，可以使用数据库

* 存储问题。电商网站商品上亿条时，涉及到单表数据过大必须拆分表，数据库磁盘占用过大必须分库 （mycat）。
* 性能问题：解决上面问题后，查询“笔记本电脑”等关键词时，上亿条数据的商品名字段逐行扫描，性能 跟不上。
* 不能分词。如搜索“笔记本电脑”，只能搜索完全和关键词一样的数据，那么数据量小时，搜索“笔记电 脑”，“电脑”数据要不要给用户。



互联网搜索，肯定不会使用数据库搜索。数据量太大。PB级



# 全文检索

全文检索是一种非结构化数据的搜索方式。

# 倒排索引

倒排索引源于实际应用中需要根据属性的值来查找记录。这种索引表中的每一项都包括一个属性值和具有该属性值的各记录的地址。由于不是由记录来确定属性值，而是由属性值来确定记录的位置，因而称 为倒排索引(inverted index)。带有倒排索引的文件我们称为倒排索引文件，简称倒排文件(inverted file)。

倒排索引（英语：Inverted index），也常被称为反向索引、置入档案或反向档案，是一种索引方法，被用来存储在全文搜索下某个单词在一个文档或者一组文档中的存储位置的映射。它是文档检索系统中最常用的数据结构。通过倒排索引，可以根据单词快速获取包含这个单词的文档列表。倒排索引主要由两个部分组成：“单词词典”和“倒排文件”。

倒排索引有两种不同的反向索引形式：

* 一条记录的水平反向索引（或者反向档案索引）包含每个引用单词的文档的列表。

* 一个单词的水平反向索引（或者完全反向索引）又包含每个单词在一个文档中的位置。

后者的形式提供了更多的兼容性（比如短语搜索），但是需要更多的时间和空间来创建。现代搜索引擎的索引都是基于倒排索引。相比“签名文件”、“后缀树”等索引结构，倒排索引是实现单词到文档映射关系的最佳实现方式和最有效的索引结构。







# elasticsearch是什么？

elasticsearch是目前最流行的准实时全文检索引擎，具有高速检索大数据的能力。

Elasticsearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。

Elasticsearch 是使用java开发，基于Lucene、分布式、通过Restful方式进行交互的近实时搜索平台框架。它的特点有：分布式，零配置，自动发现，索引自动分片，索引副本机制，restful风格接口，多数据源，自动搜索负载等。





# Elasticsearch的功能

* 分布式的搜索引擎和数据分析引擎

  搜索：互联网搜索、电商网站站内搜索、OA系统查询 

  数据分析：电商网站查询近一周哪些品类的图书销售前十；新闻网站，最近3天阅读量最高的十个关键 词，舆情分析。

* 全文检索，结构化检索，数据分析

  全文检索：搜索商品名称包含java的图书select * from books where book_name like "%java%"。 

  结构化检索：搜索商品分类为spring的图书都有哪些，select * from books where category_id='spring' 

  数据分析：分析每一个分类下有多少种图书，select category_id,count(*) from books group by category_id

* 对海量数据进行近实时的处理

  分布式：ES自动可以将海量数据分散到多台服务器上去存储和检索,经行并行查询，提高搜索效率。相对 的，Lucene是单机应用。

  近实时：数据库上亿条数据查询，搜索一次耗时几个小时，是批处理（batch-processing）。而es只需秒级即可查询海量数据，所以叫近实时。秒级。



# Elasticsearch的使用场景

* 维基百科，类似百度百科，“网络七层协议”的维基百科，全文检索，高亮，搜索推荐 
* Stack Overflow（国外的程序讨论论坛），相当于程序员的贴吧。遇到it问题去上面发帖，热心网 友下面回帖解答。 
* GitHub（开源代码管理），搜索上千亿行代码。 
* 电商网站，检索商品日志数据分析，logstash采集日志，ES进行复杂的数据分析（ELK技术， elasticsearch+logstash+kibana） 
* 商品价格监控网站，用户设定某商品的价格阈值，当低于该阈值的时候，发送通知消息给用户，比 如说订阅《java编程思想》的监控，如果价格低于27块钱，就通知我，我就去买。 
* BI系统，商业智能（Business Intelligence）。大型连锁超市，分析全国网点传回的数据，分析各个商品在什么季节的销售量最好、利润最高。成本管理，店面租金、员工工资、负债等信息进行分析。从而部署下一个阶段的战略目标。
* 百度搜索，第一次查询，使用es。
* OA、ERP系统站内搜索。



# Elasticsearch的特点

* 可拓展性：大型分布式集群（数百台服务器）技术，处理PB级数据，大公司可以使用。小公司数据量小，也可以部署在单机。大数据领域使用广泛。 
* 技术整合：将全文检索、数据分析、分布式相关技术整合在一起：lucene（全文检索），商用的数 据分析软件（BI软件），分布式数据库（mycat） 
* 部署简单：开箱即用，很多默认配置不需关心，解压完成直接运行即可。拓展时，只需多部署几个实例即可，负载均衡、分片迁移集群内部自己实施。 
* 接口简单：使用restful api经行交互，跨语言。 
* 功能强大：Elasticsearch作为传统数据库的一个补充，提供了数据库所不不能提供的很多功能，如 全文检索，同义词处理，相关度排名。



# lucene和elasticsearch的关系

Lucene：最先进、功能最强大的搜索库，直接基于lucene开发，非常复杂，api复杂 Elasticsearch：基于lucene，封装了许多lucene底层功能，提供简单易用的restful api接口和许多语言 的客户端，如java的高级客户端（Java High Level REST Client）和底层客户端（Java Low Level REST Client）



# elasticsearch核心概念

## NRT（Near Realtime）：近实时

* 写入数据时，过1秒才会被搜索到，因为内部在分词、录入索引。
* es搜索时：搜索和分析数据需要秒级出结果。



## Cluster：集群

包含一个或多个启动着es实例的机器群。通常一台机器起一个es实例。同一网络下，集名一样的多个es 实例自动组成集群，自动均衡分片等行为。默认集群名为“elasticsearch”。



## Node：节点

每个es实例称为一个节点。节点名自动分配，也可以手动配置。



##  Index：索引

包含一堆有相似结构的文档数据。

索引创建规则：

* 仅限小写字母 
* 不能包含\、/、 *、?、"、<、>、|、#以及空格符等特殊符号
*  从7.0版本开始不再包含冒号 
* 不能以-、_或+开头 
* 不能超过255个字节（注意它是字节，因此多字节字符将计入255个限制）



## Document：文档

es中的最小数据单元。一个document就像数据库中的一条记录。通常以json格式显示。多个document 存储于一个索引（Index）中。



```json
book document
{
"book_id": "1",
"book_name": "java编程思想",
"book_desc": "从Java的基础语法到最高级特性（深入的[面向对象]
(https://baike.baidu.com/item/面向对象)概念、多线程、自动项目构建、单元测试和调试等），本书
都能逐步指导你轻松掌握。",
"category_id": "2",
"category_name": "java"
}

```



##  Field:字段

就像数据库中的列（Columns），定义每个document应该有的字段。



## Type：类型

每个索引里都可以有一个或多个type，type是index中的一个逻辑数据分类，一个type下的document， 都有相同的field。

注意：6.0之前的版本有type（类型）概念，type相当于关系数据库的表，ES官方将在ES9.0版本中彻底 删除type。本教程typy都为_doc。



## shard：分片

index数据过大时，将index里面的数据，分为多个shard，分布式的存储在各个服务器上面。可以支持 海量数据和高并发，提升性能和吞吐量，充分利用多台机器的cpu。



##  replica：副本

在分布式环境下，任何一台机器都会随时宕机，如果宕机，index的一个分片没有，导致此index不能搜 索。所以，为了保证数据的安全，我们会将每个index的分片经行备份，存储在另外的机器上。保证少数 机器宕机es集群仍可以搜索。 

能正常提供查询和插入的分片我们叫做主分片（primary shard），其余的我们就管他们叫做备份的分片 （replica shard）。

es6默认新建索引时，5分片，2副本，也就是一主一备，共10个分片。所以，es集群最小规模为两台。



# elasticsearch核念和数据库概念



| **关系型数据库（比如Mysql）** | **非关系型数据库（Elasticsearch）** |
| ----------------------------- | ----------------------------------- |
| 数据库Database                | 索引Index                           |
| 表Table                       | 索引Index（原为Type）               |
| 数据行Row                     | 文档Document                        |
| 数据列Column                  | 字段Field                           |
| 约束 Schema                   | 映射Mapping                         |



# elasticsearch相关配置

配置格式是YAML：elasticsearch.yml

常用的配置项如下

```yaml
cluster.name: 
	配置elasticsearch的集群名称，默认是elasticsearch。建议修改成一个有意义的名称。
node.name:
	节点名，通常一台物理服务器就是一个节点，es会默认随机指定一个名字，建议指定一个有意义的名称，方便管理
	一个或多个节点组成一个cluster集群，集群是一个逻辑的概念，节点是物理概念，后边章节会详细介绍。
path.conf: 
	设置配置文件的存储路径，tar或zip包安装默认在es根目录下的config文件夹，rpm安装默认在/etc/ elasticsearch
path.data:
	设置索引数据的存储路径，默认是es根目录下的data文件夹，可以设置多个存储路径，用逗号隔开。
path.logs:
	设置日志文件的存储路径，默认是es根目录下的logs文件夹
path.plugins: 
	设置插件的存放路径，默认是es根目录下的plugins文件夹
bootstrap.memory_lock: true
	设置为true可以锁住ES使用的内存，避免内存与swap分区交换数据。
network.host: 
	设置绑定主机的ip地址，设置为0.0.0.0表示绑定任何ip，允许外网访问，生产环境建议设置为具体的ip。
http.port: 9200
	设置对外服务的http端口，默认为9200。
transport.tcp.port: 9300  集群结点之间通信端口
node.master: 
	指定该节点是否有资格被选举成为master结点，默认是true，如果原来的master宕机会重新选举新的master。
node.data: 
	指定该节点是否存储索引数据，默认为true。
discovery.zen.ping.unicast.hosts: ["host1:port", "host2:port", "..."]
	设置集群中master节点的初始列表。
discovery.zen.ping.timeout: 3s
	设置ES自动发现节点连接超时的时间，默认为3秒，如果网络延迟高可设置大些。
discovery.zen.minimum_master_nodes:
	主结点数量的最少值 ,此值的公式为：(master_eligible_nodes / 2) + 1 ，比如：有3个符合要求的主结点，那么这里要设置为2。
node.max_local_storage_nodes: 
	单机允许的最大存储结点数，通常单机启动一个结点建议设置为1，开发环境如果单机启动多个节点可设置大于1。
```



**jvm.options**

设置最小及最大的JVM堆内存大小：

在jvm.options中设置 -Xms和-Xmx：

1） 两个值设置为相等

2） 将Xmx 设置为不超过物理内存的一半。



**log4j2.properties**

日志文件设置，ES使用log4j，注意日志级别的配置。



ES8默认开启了 ssl 认证

修改elasticsearch.yml配置文件：

xpack.security.http.ssl:enabled 设置成 false

xpack.security.enabled 设置成false



# 启动

进入bin目录，双击elasticsearch.bat

```sh
PS C:\Users\mao\Desktop> elasticsearch
warning: ignoring JAVA_HOME=C:\Users\mao\.jdks\openjdk-16.0.2; using bundled JDK
warning: ignoring JAVA_HOME=C:\Users\mao\.jdks\openjdk-16.0.2; using ES_JAVA_HOME
[2022-05-24T21:35:52,947][INFO ][o.e.n.Node               ] [MAO] version[8.1.3], pid[16952], build[default/zip/39afaa3c0fe7db4869a161985e240bd7182d7a07/2022-04-19T08:13:25.444693396Z], OS[Windows 10/10.0/amd64], JVM[Eclipse Adoptium/OpenJDK 64-Bit Server VM/18/18+36]
[2022-05-24T21:35:52,952][INFO ][o.e.n.Node               ] [MAO] JVM home [C:\Program Files\elasticsearch-8.1.3\jdk], using bundled JDK [true]
[2022-05-24T21:35:52,953][INFO ][o.e.n.Node               ] [MAO] JVM arguments [-Des.networkaddress.cache.ttl=60, -Des.networkaddress.cache.negative.ttl=10, -Djava.security.manager=allow, -XX:+AlwaysPreTouch, -Xss1m, -Djava.awt.headless=true, -Dfile.encoding=UTF-8, -Djna.nosys=true, -XX:-OmitStackTraceInFastThrow, -XX:+ShowCodeDetailsInExceptionMessages, -Dio.netty.noUnsafe=true, -Dio.netty.noKeySetOptimization=true, -Dio.netty.recycler.maxCapacityPerThread=0, -Dio.netty.allocator.numDirectArenas=0, -Dlog4j.shutdownHookEnabled=false, -Dlog4j2.disable.jmx=true, -Dlog4j2.formatMsgNoLookups=true, -Djava.locale.providers=SPI,COMPAT, --add-opens=java.base/java.io=ALL-UNNAMED, -Xms1g, -Xmx8g, -XX:+UseG1GC, -Djava.io.tmpdir=C:\Users\mao\AppData\Local\Temp\elasticsearch, -XX:+HeapDumpOnOutOfMemoryError, -XX:+ExitOnOutOfMemoryError, -XX:HeapDumpPath=data, -XX:ErrorFile=logs/hs_err_pid%p.log, -Xlog:gc*,gc+age=trace,safepoint:file=logs/gc.log:utctime,pid,tags:filecount=32,filesize=64m, -XX:MaxDirectMemorySize=4294967296, -XX:InitiatingHeapOccupancyPercent=30, -XX:G1ReservePercent=25, -Delasticsearch, -Des.path.home=C:\Program Files\elasticsearch-8.1.3, -Des.path.conf=C:\Program Files\elasticsearch-8.1.3\config, -Des.distribution.flavor=default, -Des.distribution.type=zip, -Des.bundled_jdk=true]
[2022-05-24T21:35:56,022][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [aggs-matrix-stats]
[2022-05-24T21:35:56,023][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [analysis-common]
[2022-05-24T21:35:56,023][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [constant-keyword]
[2022-05-24T21:35:56,023][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [data-streams]
[2022-05-24T21:35:56,023][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [frozen-indices]
[2022-05-24T21:35:56,024][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [ingest-common]
[2022-05-24T21:35:56,024][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [ingest-geoip]
[2022-05-24T21:35:56,024][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [ingest-user-agent]
[2022-05-24T21:35:56,024][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [kibana]
[2022-05-24T21:35:56,024][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [lang-expression]
[2022-05-24T21:35:56,025][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [lang-mustache]
[2022-05-24T21:35:56,025][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [lang-painless]
[2022-05-24T21:35:56,025][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [legacy-geo]
[2022-05-24T21:35:56,025][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [mapper-extras]
[2022-05-24T21:35:56,026][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [mapper-version]
[2022-05-24T21:35:56,026][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [old-lucene-versions]
[2022-05-24T21:35:56,026][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [parent-join]
[2022-05-24T21:35:56,026][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [percolator]
[2022-05-24T21:35:56,027][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [rank-eval]
[2022-05-24T21:35:56,027][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [reindex]
[2022-05-24T21:35:56,027][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [repositories-metering-api]
[2022-05-24T21:35:56,027][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [repository-azure]
[2022-05-24T21:35:56,027][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [repository-encrypted]
[2022-05-24T21:35:56,028][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [repository-gcs]
[2022-05-24T21:35:56,028][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [repository-s3]
[2022-05-24T21:35:56,028][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [repository-url]
[2022-05-24T21:35:56,028][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [runtime-fields-common]
[2022-05-24T21:35:56,028][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [search-business-rules]
[2022-05-24T21:35:56,029][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [searchable-snapshots]
[2022-05-24T21:35:56,029][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [snapshot-based-recoveries]
[2022-05-24T21:35:56,029][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [snapshot-repo-test-kit]
[2022-05-24T21:35:56,029][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [spatial]
[2022-05-24T21:35:56,030][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [transform]
[2022-05-24T21:35:56,030][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [transport-netty4]
[2022-05-24T21:35:56,030][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [unsigned-long]
[2022-05-24T21:35:56,030][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [vector-tile]
[2022-05-24T21:35:56,031][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [vectors]
[2022-05-24T21:35:56,031][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [wildcard]
[2022-05-24T21:35:56,031][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-aggregate-metric]
[2022-05-24T21:35:56,032][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-analytics]
[2022-05-24T21:35:56,033][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-async]
[2022-05-24T21:35:56,033][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-async-search]
[2022-05-24T21:35:56,033][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-autoscaling]
[2022-05-24T21:35:56,034][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-ccr]
[2022-05-24T21:35:56,034][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-core]
[2022-05-24T21:35:56,035][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-deprecation]
[2022-05-24T21:35:56,035][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-enrich]
[2022-05-24T21:35:56,035][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-eql]
[2022-05-24T21:35:56,036][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-fleet]
[2022-05-24T21:35:56,036][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-graph]
[2022-05-24T21:35:56,037][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-identity-provider]
[2022-05-24T21:35:56,041][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-ilm]
[2022-05-24T21:35:56,041][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-logstash]
[2022-05-24T21:35:56,041][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-ml]
[2022-05-24T21:35:56,042][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-monitoring]
[2022-05-24T21:35:56,042][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-ql]
[2022-05-24T21:35:56,043][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-rollup]
[2022-05-24T21:35:56,043][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-security]
[2022-05-24T21:35:56,044][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-shutdown]
[2022-05-24T21:35:56,044][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-sql]
[2022-05-24T21:35:56,044][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-stack]
[2022-05-24T21:35:56,045][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-text-structure]
[2022-05-24T21:35:56,045][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-voting-only-node]
[2022-05-24T21:35:56,046][INFO ][o.e.p.PluginsService     ] [MAO] loaded module [x-pack-watcher]
[2022-05-24T21:35:56,047][INFO ][o.e.p.PluginsService     ] [MAO] no plugins loaded
[2022-05-24T21:35:56,636][INFO ][o.e.e.NodeEnvironment    ] [MAO] using [1] data paths, mounts [[(C:)]], net usable_space [74.5gb], net total_space [237.8gb], types [NTFS]
[2022-05-24T21:35:56,637][INFO ][o.e.e.NodeEnvironment    ] [MAO] heap size [8gb], compressed ordinary object pointers [true]
[2022-05-24T21:35:56,739][INFO ][o.e.n.Node               ] [MAO] node name [MAO], node ID [QrII3Cg2Sh-RnBrmVzmV9Q], cluster name [elasticsearch], roles [data_hot, transform, data_content, data_warm, master, remote_cluster_client, data, data_cold, ingest, data_frozen, ml]
[2022-05-24T21:36:00,682][INFO ][o.e.x.m.p.l.CppLogMessageHandler] [MAO] [controller/1664] [Main.cc@123] controller (64 bit): Version 8.1.3 (Build 92d8267e6ebfb7) Copyright (c) 2022 Elasticsearch BV
[2022-05-24T21:36:00,966][INFO ][o.e.x.s.Security         ] [MAO] Security is enabled
[2022-05-24T21:36:01,316][INFO ][o.e.x.s.a.Realms         ] [MAO] license mode is [trial], currently licensed security realms are [reserved/reserved,file/default_file,native/default_native]
[2022-05-24T21:36:01,328][INFO ][o.e.x.s.a.s.FileRolesStore] [MAO] parsed [0] roles from file [C:\Program Files\elasticsearch-8.1.3\config\roles.yml]

[2022-05-24T21:36:01,896][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb]
[2022-05-24T21:36:01,897][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb_COPYRIGHT.txt]
[2022-05-24T21:36:01,898][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb_elastic-geoip-database-service-agreement-LICENSE.txt]
[2022-05-24T21:36:01,899][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb_LICENSE.txt]
[2022-05-24T21:36:01,899][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-City.mmdb]
[2022-05-24T21:36:01,905][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-City.mmdb_COPYRIGHT.txt]
[2022-05-24T21:36:01,906][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-City.mmdb_elastic-geoip-database-service-agreement-LICENSE.txt]
[2022-05-24T21:36:01,907][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-City.mmdb_LICENSE.txt]
[2022-05-24T21:36:01,908][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-City.mmdb_README.txt]
[2022-05-24T21:36:01,908][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-Country.mmdb]
[2022-05-24T21:36:01,909][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-Country.mmdb_COPYRIGHT.txt]
[2022-05-24T21:36:01,910][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-Country.mmdb_elastic-geoip-database-service-agreement-LICENSE.txt]
[2022-05-24T21:36:01,911][INFO ][o.e.i.g.DatabaseNodeService] [MAO] deleting stale file [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-Country.mmdb_LICENSE.txt]
[2022-05-24T21:36:02,472][INFO ][o.e.t.n.NettyAllocator   ] [MAO] creating NettyAllocator with the following configs: [name=elasticsearch_configured, chunk_size=1mb, suggested_max_allocation_size=1mb, factors={es.unsafe.use_netty_default_chunk_and_page_size=false, g1gc_enabled=true, g1gc_region_size=4mb}]
[2022-05-24T21:36:02,499][INFO ][o.e.i.r.RecoverySettings ] [MAO] using rate limit [40mb] with [default=40mb, read=0b, write=0b, max=0b]
[2022-05-24T21:36:02,529][INFO ][o.e.d.DiscoveryModule    ] [MAO] using discovery type [multi-node] and seed hosts providers [settings]
[2022-05-24T21:36:03,571][INFO ][o.e.n.Node               ] [MAO] initialized
[2022-05-24T21:36:03,572][INFO ][o.e.n.Node               ] [MAO] starting ...
[2022-05-24T21:36:03,621][INFO ][o.e.x.s.c.f.PersistentCache] [MAO] persistent cache index loaded
[2022-05-24T21:36:03,622][INFO ][o.e.x.d.l.DeprecationIndexingComponent] [MAO] deprecation component started
[2022-05-24T21:36:03,812][INFO ][o.e.t.TransportService   ] [MAO] publish_address {127.0.0.1:9300}, bound_addresses {127.0.0.1:9300}, {[::1]:9300}
[2022-05-24T21:36:04,207][WARN ][o.e.b.BootstrapChecks    ] [MAO] initial heap size [1073741824] not equal to maximum heap size [8589934592]; this can cause resize pauses
[2022-05-24T21:36:04,209][INFO ][o.e.c.c.Coordinator      ] [MAO] cluster UUID [9R1mejGTSbCX20057B9IdQ]
[2022-05-24T21:36:04,296][INFO ][o.e.c.s.MasterService    ] [MAO] elected-as-master ([1] nodes joined)[{MAO}{QrII3Cg2Sh-RnBrmVzmV9Q}{xKQM768NSTSeKF_YYHTyJw}{127.0.0.1}{127.0.0.1:9300}{cdfhilmrstw} completing election, _BECOME_MASTER_TASK_, _FINISH_ELECTION_], term: 18, version: 365, delta: master node changed {previous [], current [{MAO}{QrII3Cg2Sh-RnBrmVzmV9Q}{xKQM768NSTSeKF_YYHTyJw}{127.0.0.1}{127.0.0.1:9300}{cdfhilmrstw}]}
[2022-05-24T21:36:04,381][INFO ][o.e.c.s.ClusterApplierService] [MAO] master node changed {previous [], current [{MAO}{QrII3Cg2Sh-RnBrmVzmV9Q}{xKQM768NSTSeKF_YYHTyJw}{127.0.0.1}{127.0.0.1:9300}{cdfhilmrstw}]}, term: 18, version: 365, reason: Publication{term=18, version=365}
[2022-05-24T21:36:04,509][INFO ][o.e.h.AbstractHttpServerTransport] [MAO] publish_address {172.18.128.1:9200}, bound_addresses {127.0.0.1:9200}, {[::1]:9200}, {172.18.128.1:9200}, {172.23.16.1:9200}, {192.168.202.1:9200}, {192.168.73.1:9200}, {172.28.0.1:9200}, {172.30.64.1:9200}, {172.30.176.1:9200}
[2022-05-24T21:36:04,509][INFO ][o.e.n.Node               ] [MAO] started
[2022-05-24T21:36:04,736][INFO ][o.e.l.LicenseService     ] [MAO] license [ffaa99ef-66ed-4580-95bf-7a6049a44720] mode [basic] - valid
[2022-05-24T21:36:04,738][INFO ][o.e.x.s.a.Realms         ] [MAO] license mode is [basic], currently licensed security realms are [reserved/reserved,file/default_file,native/default_native]
[2022-05-24T21:36:04,745][INFO ][o.e.g.GatewayService     ] [MAO] recovered [10] indices into cluster_state
[2022-05-24T21:36:05,552][INFO ][o.e.i.g.DatabaseNodeService] [MAO] retrieve geoip database [GeoLite2-ASN.mmdb] from [.geoip_databases] to [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb.tmp.gz]
[2022-05-24T21:36:05,554][INFO ][o.e.i.g.DatabaseNodeService] [MAO] retrieve geoip database [GeoLite2-Country.mmdb] from [.geoip_databases] to [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-Country.mmdb.tmp.gz]
[2022-05-24T21:36:05,556][INFO ][o.e.i.g.DatabaseNodeService] [MAO] retrieve geoip database [GeoLite2-City.mmdb] from [.geoip_databases] to [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-City.mmdb.tmp.gz]
[2022-05-24T21:36:05,718][INFO ][o.e.c.r.a.AllocationService] [MAO] current.health="GREEN" message="Cluster health status changed from [RED] to [GREEN] (reason: [shards started [[.kibana-event-log-8.1.3-000001][0]]])." previous.health="RED" reason="shards started [[.kibana-event-log-8.1.3-000001][0]]"
[2022-05-24T21:36:05,746][INFO ][o.e.i.g.DatabaseNodeService] [MAO] successfully loaded geoip database file [GeoLite2-Country.mmdb]
[2022-05-24T21:36:05,806][INFO ][o.e.i.g.DatabaseNodeService] [MAO] successfully loaded geoip database file [GeoLite2-ASN.mmdb]
[2022-05-24T21:36:06,530][INFO ][o.e.i.g.DatabaseNodeService] [MAO] successfully loaded geoip database file [GeoLite2-City.mmdb]
[2022-05-24T21:36:17,573][INFO ][o.e.i.g.DatabaseNodeService] [MAO] retrieve geoip database [GeoLite2-ASN.mmdb] from [.geoip_databases] to [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb.tmp.gz]
[2022-05-24T21:36:28,200][INFO ][o.e.i.g.GeoIpDownloader  ] [MAO] successfully downloaded geoip database [GeoLite2-ASN.mmdb]
[2022-05-24T21:36:28,296][INFO ][o.e.i.g.DatabaseReaderLazyLoader] [MAO] evicted [0] entries from cache after reloading database [C:\Users\mao\AppData\Local\Temp\elasticsearch\geoip-databases\QrII3Cg2Sh-RnBrmVzmV9Q\GeoLite2-ASN.mmdb]
[2022-05-24T21:36:28,297][INFO ][o.e.i.g.DatabaseNodeService] [MAO] successfully loaded geoip database file [GeoLite2-ASN.mmdb]
```



## 检查ES是否启动成功

浏览器访问 http://localhost:9200/

结果：

```json
{
  "name" : "MAO",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "9R1mejGTSbCX20057B9IdQ",
  "version" : {
    "number" : "8.1.3",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "39afaa3c0fe7db4869a161985e240bd7182d7a07",
    "build_date" : "2022-04-19T08:13:25.444693396Z",
    "build_snapshot" : false,
    "lucene_version" : "9.0.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

* name: node名称，取自机器的hostname

* cluster_name: 集群名称（默认的集群名称就是elasticsearch）

* version.number: 7.3.0，es版本号

* version.lucene_version:封装的lucene版本号



## 查询集群状态

浏览器访问 http://localhost:9200/_cluster/health

结果：

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 1,
    "number_of_data_nodes": 1,
    "active_primary_shards": 10,
    "active_shards": 10,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "task_max_waiting_in_queue_millis": 0,
    "active_shards_percent_as_number": 100
}
```

Status：集群状态。Green 所有分片可用。Yellow所有主分片可用。Red主分片不可用，集群不可用。



# Kibana

kibana是es数据的前端展现，数据分析时，可以方便地看到数据。作为开发人员，可以方便访问es。

启动Kibana：bin\kibana.bat

结果：

```sh
PS C:\Users\mao\Desktop> kibana
[2022-05-24T22:07:56.816+08:00][INFO ][plugins-service] Plugin "metricsEntities" is disabled.
[2022-05-24T22:07:56.883+08:00][INFO ][http.server.Preboot] http server running at http://localhost:5601
[2022-05-24T22:07:56.921+08:00][INFO ][plugins-system.preboot] Setting up [1] plugins: [interactiveSetup]
[2022-05-24T22:07:56.933+08:00][INFO ][preboot] "interactiveSetup" plugin is holding setup: Validating Elasticsearch connection configuration…
[2022-05-24T22:07:56.964+08:00][INFO ][root] Holding setup until preboot stage is completed.
[2022-05-24T22:07:56.995+08:00][WARN ][config.deprecation] The default mechanism for Reporting privileges will work differently in future versions, which will affect the behavior of this cluster. Set "xpack.reporting.roles.enabled" to "false" to adopt the future behavior before upgrading.
[2022-05-24T22:07:57.160+08:00][INFO ][plugins-system.standard] Setting up [112] plugins: [translations,licensing,globalSearch,globalSearchProviders,features,mapsEms,licenseApiGuard,usageCollection,taskManager,telemetryCollectionManager,telemetryCollectionXpack,kibanaUsageCollection,sharedUX,share,embeddable,uiActionsEnhanced,screenshotMode,screenshotting,banners,telemetry,newsfeed,fieldFormats,expressions,dataViews,charts,esUiShared,bfetch,data,savedObjects,presentationUtil,expressionShape,expressionRevealImage,expressionRepeatImage,expressionMetric,expressionImage,customIntegrations,home,searchprofiler,painlessLab,grokdebugger,management,watcher,licenseManagement,advancedSettings,spaces,security,savedObjectsTagging,reporting,lists,fileUpload,ingestPipelines,encryptedSavedObjects,dataEnhanced,cloud,snapshotRestore,eventLog,actions,alerting,triggersActionsUi,transform,stackAlerts,ruleRegistry,savedObjectsManagement,console,controls,graph,fleet,indexManagement,remoteClusters,crossClusterReplication,indexLifecycleManagement,visualizations,canvas,visTypeXy,visTypeVislib,visTypeVega,visTypeTimeseries,rollup,visTypeTimelion,visTypeTagcloud,visTypeTable,visTypeMetric,visTypeHeatmap,visTypeMarkdown,dashboard,maps,dashboardEnhanced,expressionTagcloud,expressionPie,visTypePie,expressionMetricVis,expressionHeatmap,expressionGauge,dataViewFieldEditor,lens,cases,timelines,discover,osquery,observability,discoverEnhanced,dataVisualizer,ml,uptime,securitySolution,infra,upgradeAssistant,monitoring,logstash,enterpriseSearch,apm,dataViewManagement]
[2022-05-24T22:07:57.177+08:00][INFO ][plugins.taskManager] TaskManager is identified by the Kibana UUID: 25e0275f-cbbb-424d-86e9-06058cb8dfb3
[2022-05-24T22:07:57.317+08:00][WARN ][plugins.security.config] Generating a random key for xpack.security.encryptionKey. To prevent sessions from being invalidated on restart, please set xpack.security.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
[2022-05-24T22:07:57.318+08:00][WARN ][plugins.security.config] Session cookies will be transmitted over insecure connections. This is not recommended.
[2022-05-24T22:07:57.337+08:00][WARN ][plugins.security.config] Generating a random key for xpack.security.encryptionKey. To prevent sessions from being invalidated on restart, please set xpack.security.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
[2022-05-24T22:07:57.338+08:00][WARN ][plugins.security.config] Session cookies will be transmitted over insecure connections. This is not recommended.
[2022-05-24T22:07:57.357+08:00][WARN ][plugins.reporting.config] Generating a random key for xpack.reporting.encryptionKey. To prevent sessions from being invalidated on restart, please set xpack.reporting.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
[2022-05-24T22:07:57.367+08:00][WARN ][plugins.encryptedSavedObjects] Saved objects encryption key is not set. This will severely limit Kibana functionality. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
[2022-05-24T22:07:57.381+08:00][WARN ][plugins.actions] APIs are disabled because the Encrypted Saved Objects plugin is missing encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
[2022-05-24T22:07:57.396+08:00][WARN ][plugins.alerting] APIs are disabled because the Encrypted Saved Objects plugin is missing encryption key. Please set xpack.encryptedSavedObjects.encryptionKey in the kibana.yml or use the bin/kibana-encryption-keys command.
[2022-05-24T22:07:57.419+08:00][INFO ][plugins.ruleRegistry] Installing common resources shared between all indices
[2022-05-24T22:07:58.081+08:00][INFO ][plugins.screenshotting.config] Chromium sandbox provides an additional layer of protection, and is supported for Win32 OS. Automatically enabling Chromium sandbox.
[2022-05-24T22:07:58.995+08:00][INFO ][savedobjects-service] Waiting until all Elasticsearch nodes are compatible with Kibana before starting saved objects migrations...
[2022-05-24T22:07:58.996+08:00][INFO ][savedobjects-service] Starting saved objects migrations
[2022-05-24T22:07:59.377+08:00][INFO ][savedobjects-service] [.kibana] INIT -> OUTDATED_DOCUMENTS_SEARCH_OPEN_PIT. took: 283ms.
[2022-05-24T22:07:59.439+08:00][INFO ][savedobjects-service] [.kibana_task_manager] INIT -> OUTDATED_DOCUMENTS_SEARCH_OPEN_PIT. took: 343ms.
[2022-05-24T22:07:59.468+08:00][INFO ][savedobjects-service] [.kibana] OUTDATED_DOCUMENTS_SEARCH_OPEN_PIT -> OUTDATED_DOCUMENTS_SEARCH_READ. took: 91ms.
[2022-05-24T22:07:59.479+08:00][INFO ][savedobjects-service] [.kibana_task_manager] OUTDATED_DOCUMENTS_SEARCH_OPEN_PIT -> OUTDATED_DOCUMENTS_SEARCH_READ. took: 40ms.
[2022-05-24T22:07:59.494+08:00][INFO ][plugins.screenshotting.chromium] Browser executable: H:\opensoft\kibana-8.1.3\x-pack\plugins\screenshotting\chromium\chrome-win\chrome.exe
[2022-05-24T22:07:59.521+08:00][INFO ][savedobjects-service] [.kibana_task_manager] OUTDATED_DOCUMENTS_SEARCH_READ -> OUTDATED_DOCUMENTS_SEARCH_CLOSE_PIT. took: 42ms.
[2022-05-24T22:07:59.524+08:00][INFO ][savedobjects-service] [.kibana] OUTDATED_DOCUMENTS_SEARCH_READ -> OUTDATED_DOCUMENTS_SEARCH_CLOSE_PIT. took: 56ms.
[2022-05-24T22:07:59.529+08:00][INFO ][savedobjects-service] [.kibana_task_manager] OUTDATED_DOCUMENTS_SEARCH_CLOSE_PIT -> UPDATE_TARGET_MAPPINGS. took: 8ms.
[2022-05-24T22:07:59.532+08:00][INFO ][savedobjects-service] [.kibana] OUTDATED_DOCUMENTS_SEARCH_CLOSE_PIT -> UPDATE_TARGET_MAPPINGS. took: 8ms.
[2022-05-24T22:07:59.576+08:00][INFO ][savedobjects-service] [.kibana_task_manager] UPDATE_TARGET_MAPPINGS -> UPDATE_TARGET_MAPPINGS_WAIT_FOR_TASK. took: 47ms.
[2022-05-24T22:07:59.623+08:00][INFO ][savedobjects-service] [.kibana] UPDATE_TARGET_MAPPINGS -> UPDATE_TARGET_MAPPINGS_WAIT_FOR_TASK. took: 91ms.
[2022-05-24T22:07:59.867+08:00][INFO ][savedobjects-service] [.kibana] UPDATE_TARGET_MAPPINGS_WAIT_FOR_TASK -> DONE. took: 244ms.
[2022-05-24T22:07:59.867+08:00][INFO ][savedobjects-service] [.kibana] Migration completed after 773ms
[2022-05-24T22:07:59.869+08:00][INFO ][savedobjects-service] [.kibana_task_manager] UPDATE_TARGET_MAPPINGS_WAIT_FOR_TASK -> DONE. took: 293ms.
[2022-05-24T22:07:59.870+08:00][INFO ][savedobjects-service] [.kibana_task_manager] Migration completed after 774ms
[2022-05-24T22:08:00.097+08:00][INFO ][plugins-system.preboot] Stopping all plugins.
[2022-05-24T22:08:00.099+08:00][INFO ][plugins-system.standard] Starting [112] plugins: [translations,licensing,globalSearch,globalSearchProviders,features,mapsEms,licenseApiGuard,usageCollection,taskManager,telemetryCollectionManager,telemetryCollectionXpack,kibanaUsageCollection,sharedUX,share,embeddable,uiActionsEnhanced,screenshotMode,screenshotting,banners,telemetry,newsfeed,fieldFormats,expressions,dataViews,charts,esUiShared,bfetch,data,savedObjects,presentationUtil,expressionShape,expressionRevealImage,expressionRepeatImage,expressionMetric,expressionImage,customIntegrations,home,searchprofiler,painlessLab,grokdebugger,management,watcher,licenseManagement,advancedSettings,spaces,security,savedObjectsTagging,reporting,lists,fileUpload,ingestPipelines,encryptedSavedObjects,dataEnhanced,cloud,snapshotRestore,eventLog,actions,alerting,triggersActionsUi,transform,stackAlerts,ruleRegistry,savedObjectsManagement,console,controls,graph,fleet,indexManagement,remoteClusters,crossClusterReplication,indexLifecycleManagement,visualizations,canvas,visTypeXy,visTypeVislib,visTypeVega,visTypeTimeseries,rollup,visTypeTimelion,visTypeTagcloud,visTypeTable,visTypeMetric,visTypeHeatmap,visTypeMarkdown,dashboard,maps,dashboardEnhanced,expressionTagcloud,expressionPie,visTypePie,expressionMetricVis,expressionHeatmap,expressionGauge,dataViewFieldEditor,lens,cases,timelines,discover,osquery,observability,discoverEnhanced,dataVisualizer,ml,uptime,securitySolution,infra,upgradeAssistant,monitoring,logstash,enterpriseSearch,apm,dataViewManagement]
[2022-05-24T22:08:02.827+08:00][INFO ][plugins.fleet] Beginning fleet setup
[2022-05-24T22:08:02.850+08:00][INFO ][plugins.monitoring.monitoring] config sourced from: production cluster
[2022-05-24T22:08:04.150+08:00][INFO ][http.server.Kibana] http server running at http://localhost:5601
[2022-05-24T22:08:04.355+08:00][INFO ][plugins.monitoring.monitoring.kibana-monitoring] Starting monitoring stats collection
[2022-05-24T22:08:04.445+08:00][INFO ][plugins.ruleRegistry] Installed common resources shared between all indices
[2022-05-24T22:08:04.445+08:00][INFO ][plugins.ruleRegistry] Installing resources for index .alerts-observability.uptime.alerts
[2022-05-24T22:08:04.447+08:00][INFO ][plugins.ruleRegistry] Installing resources for index .alerts-security.alerts
[2022-05-24T22:08:04.448+08:00][INFO ][plugins.ruleRegistry] Installing resources for index .preview.alerts-security.alerts
[2022-05-24T22:08:04.448+08:00][INFO ][plugins.ruleRegistry] Installing resources for index .alerts-observability.logs.alerts
[2022-05-24T22:08:04.449+08:00][INFO ][plugins.ruleRegistry] Installing resources for index .alerts-observability.metrics.alerts
[2022-05-24T22:08:04.450+08:00][INFO ][plugins.ruleRegistry] Installing resources for index .alerts-observability.apm.alerts
[2022-05-24T22:08:04.546+08:00][INFO ][plugins.ruleRegistry] Installed resources for index .alerts-observability.apm.alerts
[2022-05-24T22:08:04.547+08:00][INFO ][plugins.ruleRegistry] Installed resources for index .alerts-observability.logs.alerts
[2022-05-24T22:08:04.548+08:00][INFO ][plugins.ruleRegistry] Installed resources for index .alerts-observability.uptime.alerts
[2022-05-24T22:08:04.549+08:00][INFO ][plugins.ruleRegistry] Installed resources for index .alerts-observability.metrics.alerts
[2022-05-24T22:08:04.550+08:00][INFO ][plugins.ruleRegistry] Installed resources for index .alerts-security.alerts
[2022-05-24T22:08:04.607+08:00][INFO ][plugins.ruleRegistry] Installed resources for index .preview.alerts-security.alerts
[2022-05-24T22:08:04.635+08:00][INFO ][plugins.fleet] Deleting preconfigured output fleet-default-output
[2022-05-24T22:08:04.900+08:00][INFO ][plugins.fleet] Fleet setup completed
[2022-05-24T22:08:04.906+08:00][INFO ][plugins.securitySolution] Dependent plugin setup complete - Starting ManifestTask
[2022-05-24T22:08:05.408+08:00][INFO ][status] Kibana is now degraded
[2022-05-24T22:08:07.461+08:00][INFO ][plugins.securitySolution.endpoint:metadata-check-transforms-task:0.0.1] no endpoint installation found
[2022-05-24T22:08:11.101+08:00][INFO ][status] Kibana is now available (was degraded)

```



浏览器访问 http://localhost:5601 进入Dev Tools界面





# 文档（document）的数据格式

（1）应用系统的数据结构都是面向对象的，具有复杂的数据结构

（2）对象存储到数据库，需要将关联的复杂对象属性插到另一张表，查询时再拼接起来。

（3）es面向文档，文档中存储的数据结构，与对象一致。所以一个对象可以直接存成一个文档。

（4）es的document用json数据格式来表达。

例如：班级和学生关系

```java
public class Student 
{
  private String id;
  private String name;
  private String classInfoId;  
}

private class ClassInfo 
{
  private String id;
  private String className;
}
```

数据库中要设计所谓的一对多，多对一的两张表，外键等。查询出来时，还要关联，mybatis写映射文件，很繁琐。

而在es中，一个学生存成文档如下：

```json
{
    "id":"1",
    "name": "张三",
    "last_name": "zhang",
    "classInfo": 
    {
        "id": "1",
        "className": "三年二班",     
    }
}
```



# 简单的集群管理

## 快速检查集群的健康状况

es提供了一套api，叫做cat api，可以查看es中各种各样的数据

GET http://localhost:9200/_cat/health?v

结果：

```sh
epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1653402721 14:32:01  elasticsearch green           1         1     14  14    0    0        0             0                  -                100.0%
```



green：每个索引的primary shard和replica shard都是active状态的

yellow：每个索引的primary shard都是active状态的，但是部分replica shard不是active状态，处于不可用的状态

red：不是所有索引的primary shard都是active状态的，部分索引有数据丢失了



## 快速查看集群中有哪些索引

GET /_cat/indices?v

```sh
health status index                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   kibana_sample_data_ecommerce DY7cNTGNSxOPPVU2Awqu5A   1   0       4675            0      4.1mb          4.1mb
green  open   kibana_sample_data_logs      jrrXk1ITQvW8lQDYmWB7wg   1   0      14074            0      8.1mb          8.1mb
yellow open   demo_index                   1hX6S-rxTAmBCy_R9Rw8Xg   1   1          0            0       225b           225b

```





## 简单的索引操作

创建索引：PUT /demo_index?pretty

```json
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "demo_index"
}
```

查看索引：GET /demo_index?pretty

```JSON
{
    "demo_index": {
        "aliases": {},
        "mappings": {},
        "settings": {
            "index": {
                "routing": {
                    "allocation": {
                        "include": {
                            "_tier_preference": "data_content"
                        }
                    }
                },
                "number_of_shards": "1",
                "provided_name": "demo_index",
                "creation_date": "1653402994280",
                "number_of_replicas": "1",
                "uuid": "1hX6S-rxTAmBCy_R9Rw8Xg",
                "version": {
                    "created": "8010399"
                }
            }
        }
    }
}
```





删除索引：DELETE /demo_index?pretty

```json
{
    "acknowledged": true
}
```



# CRUD操作

## 建立索引

首先建立图书索引 book

语法：put /index

PUT /book

结果：

```json
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "book"
}
```



## 新增文档

语法：PUT /index/type/id



语法：PUT /index/type/id

```json
PUT /book/_doc/1

{
"name": "Bootstrap开发",
"description": "Bootstrap是由Twitter推出的一个前台页面开发css框架，是一个非常流行的开发框架，此框架集成了多种页面效果。此开发框架包含了大量的CSS、JS程序代码，可以帮助开发者（尤其是不擅长css页面开发的程序人员）轻松的实现一个css，不受浏览器限制的精美界面css效果。",
"studymodel": "201002",
"price":38.6,
"timestamp":"2019-08-25 19:11:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "bootstrap", "dev"]
}

```

结果：

```json
{
    "_index": "book",
    "_id": "1",
    "_version": 2,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 1,
    "_primary_term": 1
}
```





```json
PUT /book/_doc/2
{
"name": "java编程思想",
"description": "java语言是世界第一编程语言，在软件开发领域使用人数最多。",
"studymodel": "201001",
"price":68.6,
"timestamp":"2019-08-25 19:11:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "java", "dev"]
}
```

结果：

```json
{
    "_index": "book",
    "_id": "2",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 2,
    "_primary_term": 1
}
```




```java
PUT /book/_doc/3
{
"name": "spring开发基础",
"description": "spring 在java领域非常流行，java程序员都在用。",
"studymodel": "201001",
"price":88.6,
"timestamp":"2019-08-24 19:11:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "spring", "java"]
}
```

结果：

```json
{
    "_index": "book",
    "_id": "3",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 3,
    "_primary_term": 1
}
```



## 查询文档

语法：GET /index/type/id

查看图书:GET /book/_doc/1  就可看到json形式的文档。方便程序解析。



get /book/_doc/1

结果：

```json
{
    "_index": "book",
    "_id": "1",
    "_version": 2,
    "_seq_no": 1,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "name": "Bootstrap开发",
        "description": "Bootstrap是由Twitter推出的一个前台页面开发css框架，是一个非常流行的开发框架，此框架集成了多种页面效果。此开发框架包含了大量的CSS、JS程序代码，可以帮助开发者（尤其是不擅长css页面开发的程序人员）轻松的实现一个css，不受浏览器限制的精美界面css效果。",
        "studymodel": "201002",
        "price": 38.6,
        "timestamp": "2019-08-25 19:11:35",
        "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
        "tags": [
            "bootstrap",
            "dev"
        ]
    }
}
```



get /book/_doc/3

结果：

```json
{
    "_index": "book",
    "_id": "3",
    "_version": 1,
    "_seq_no": 3,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "name": "spring开发基础",
        "description": "spring 在java领域非常流行，java程序员都在用。",
        "studymodel": "201001",
        "price": 88.6,
        "timestamp": "2019-08-24 19:11:35",
        "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
        "tags": [
            "spring",
            "java"
        ]
    }
}
```



## 修改文档

方法一：整体覆盖，要带上所有信息

PUT /book/_doc/3

```json
{
"name": "spring开发基础",
"description": "spring 在java领域非常流行，java程序员都在用。",
"studymodel": "201001",
"price":88.6,
"timestamp":"2019-08-24 19:21:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "spring", "java"]
}
```

结果：

```json
{
    "_index": "book",
    "_id": "3",
    "_version": 2,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 4,
    "_primary_term": 1
}
```



方法二：局部替换

语法：POST  /{index}/type /{id}/_update

或者POST  /{index}/_update/{id}



POST /book/_update/3

```json
{
  "doc": {
   "price": 78.6
  }
}
```

结果：

```json
{
    "_index": "book",
    "_id": "3",
    "_version": 3,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 5,
    "_primary_term": 1
}
```



## 删除文档

语法：DELETE  /book/_doc/{id}

```
DELETE /book/_doc/1
```

结果：

```json
{
    "_index": "book",
    "_id": "1",
    "_version": 3,
    "result": "deleted",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 6,
    "_primary_term": 1
}
```



再次查询：

```json
{
    "_index": "book",
    "_id": "1",
    "found": false
}
```



## 批量查询 

单条查询 GET  /test_index/_doc/1，如果查询多个id的文档一条一条查询，网络开销太大。

```json
GET /_mget
{
   "docs" : 
    [
      {
         "_index" : "book",
         "_id" :    2
      },
      {
         "_index" : "book",
         "_id" :    3
      }
   ]
}
```

结果：

```json
{
    "docs": [
        {
            "_index": "book",
            "_id": "2",
            "_version": 1,
            "_seq_no": 2,
            "_primary_term": 1,
            "found": true,
            "_source": {
                "name": "java编程思想",
                "description": "java语言是世界第一编程语言，在软件开发领域使用人数最多。",
                "studymodel": "201001",
                "price": 68.6,
                "timestamp": "2019-08-25 19:11:35",
                "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
                "tags": [
                    "java",
                    "dev"
                ]
            }
        },
        {
            "_index": "book",
            "_id": "3",
            "_version": 3,
            "_seq_no": 5,
            "_primary_term": 1,
            "found": true,
            "_source": {
                "name": "spring开发基础",
                "description": "spring 在java领域非常流行，java程序员都在用。",
                "studymodel": "201001",
                "price": 78.6,
                "timestamp": "2019-08-24 19:21:35",
                "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
                "tags": [
                    "spring",
                    "java"
                ]
            }
        }
    ]
}
```



 同一索引下批量查询：

```
GET /book/_mget
{
   "docs" : 
   [
      {
         "_id" :2
      },
      {
         "_id" :3
      }
   ]
}
```

结果：

```json
{
    "docs": [
        {
            "_index": "book",
            "_id": "2",
            "_version": 1,
            "_seq_no": 2,
            "_primary_term": 1,
            "found": true,
            "_source": {
                "name": "java编程思想",
                "description": "java语言是世界第一编程语言，在软件开发领域使用人数最多。",
                "studymodel": "201001",
                "price": 68.6,
                "timestamp": "2019-08-25 19:11:35",
                "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
                "tags": [
                    "java",
                    "dev"
                ]
            }
        },
        {
            "_index": "book",
            "_id": "3",
            "_version": 3,
            "_seq_no": 5,
            "_primary_term": 1,
            "found": true,
            "_source": {
                "name": "spring开发基础",
                "description": "spring 在java领域非常流行，java程序员都在用。",
                "studymodel": "201001",
                "price": 78.6,
                "timestamp": "2019-08-24 19:21:35",
                "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
                "tags": [
                    "spring",
                    "java"
                ]
            }
        }
    ]
}
```



搜索写法:

```json
post /book/_doc/_search
{
    "query": 
    {
        "ids" : 
        {
            "values" : ["2", "3"]
        }
    }
}
```



## 批量增删改

Bulk 操作解释将文档的增删改查一些列操作，通过一次请求全都做完。减少网络传输次数。

语法：

```
POST /_bulk
{"action": {"metadata"}}
{"data"}
```

如下操作，删除5，新增14，修改2。

```
POST /_bulk
{ "delete": { "_index": "test_index",  "_id": "5" }} 
{ "create": { "_index": "test_index",  "_id": "14" }}
{ "test_field": "test14" }
{ "update": { "_index": "test_index",  "_id": "2"} }
{ "doc" : {"test_field" : "bulk test"} }
```



# 文档document

## 字段说明

示例：

```json
{
    "_index": "book",
    "_id": "3",
    "_version": 3,
    "_seq_no": 5,
    "_primary_term": 1,
    "found": true,
    "_source": {
        "name": "spring开发基础",
        "description": "spring 在java领域非常流行，java程序员都在用。",
        "studymodel": "201001",
        "price": 78.6,
        "timestamp": "2019-08-24 19:21:35",
        "pic": "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
        "tags": [
            "spring",
            "java"
        ]
    }
}
```



### _index

-  含义：此文档属于哪个索引
-  原则：类似数据放在一个索引中。数据库中表的定义规则。如图书信息放在book索引中，员工信息放在employee索引中。各个索引存储和搜索时互不影响。
-  定义规则：英文小写。尽量不要使用特殊字符。order user 



### _id

含义：文档的唯一标识。就像表的id主键。结合索引可以标识和定义一个文档。

生成：手动（put /index/_doc/id）、自动



### _version

版本信息，每次修改，版本+1



###  _source

含义：插入数据时的所有字段和值。在get获取数据时，在_source字段中原样返回。

GET  /book/_doc/1



## 生成文档id

用法：POST /index/_doc



```json
POST /test_index/_doc
{
  "test_field": "test1"
}

```

结果：

```json
{
    "_index": "book",
    "_id": "P2Gd9oABt1R14CTMIiwV",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 7,
    "_primary_term": 1
}
```



## 定制返回字段

就像sql不要select *,而要select name,price from book …一样。

GET  /book/_doc/1?__source_includes=name,price    

```json
{
  "_index" : "book",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 10,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "price" : 38.6,
    "name" : "Bootstrap开发教程1"
  }
}
```



## 文档的替换

### 全量替换

执行两次，返回结果中版本号（_version）在不断上升。此过程为全量替换。

实质：旧文档的内容不会立即删除，只是标记为deleted。适当的时机，集群会将这些文档删除。



### 强制创建

为防止覆盖原有数据，我们在新增时，设置为强制创建，不会覆盖原有文档。

语法：PUT /index/ _doc/id/_create



```json
PUT /test_index/_doc/1/_create
{
  "test_field": "test"
}
```

返回

```json
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[2]: version conflict, document already exists (current version [1])",
        "index_uuid": "lqzVqxZLQuCnd6LYtZsMkg",
        "shard": "0",
        "index": "test_index"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[2]: version conflict, document already exists (current version [1])",
    "index_uuid": "lqzVqxZLQuCnd6LYtZsMkg",
    "shard": "0",
    "index": "test_index"
  },
  "status": 409
}
```





内部与全量替换是一样的，旧文档标记为删除，新建一个文档。

优点：

- 大大减少网络传输次数和流量，提升性能
- 减少并发冲突发生的概率。



# 悲观锁与乐观锁机制

为控制并发问题，我们通常采用锁机制。分为悲观锁和乐观锁两种机制。

* 悲观锁：很悲观，所有情况都上锁。此时只有一个线程可以操作数据。具体例子为数据库中的行级锁、表级锁、读锁、写锁等。

​		特点：优点是方便，直接加锁，对程序透明。缺点是效率低。

* 乐观锁：很乐观，对数据本身不加锁。提交数据时，通过一种机制验证是否存在冲突，如es中通过版本号验证。

​		特点：优点是并发能力高。缺点是操作繁琐，在提交数据时，可能反复重试多次。



es对于文档的增删改都是基于版本号。

## 手动控制版本号

背景：已有数据是在数据库中，有自己手动维护的版本号的情况下，可以使用external version控制。hbase。

要求：修改时external version要大于当前文档的_version

对比：基于_version时，修改的文档version等于当前文档的版本号。

使用?version=1&version_type=external



# 总结

-  delete：删除一个文档，只要1个json串就可以了
-  create：相当于强制创建  PUT /index/type/id/_create 
-  index：普通的put操作，可以是创建文档，也可以是全量替换文档
-  update：执行的是局部更新partial update操作



* 每个json不能换行。相邻json必须换行。

* 每个操作互不影响。操作失败的行会返回其失败信息。

* bulk请求一次不要太大，否则一下积压到内存中，性能会下降。所以，一次请求几千个操作、大小在几M正好。









# Java api 实现文档管理

java api有两种：

low : 偏向底层。

high：高级封装。足够。



## maven依赖

```xml
```

