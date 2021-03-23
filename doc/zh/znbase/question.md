# FAQ

## **产品相关FAQ**

**云溪NewSQL是什么？**

云溪NewSQL数据库服务系统是浪潮云提供的HTAP数据库服务。采用对等架构设计，GO语言开发，支持分布式计算和分布式存储。拥有原生强一致、无限横向扩展、多中心、安全可靠高可用等特点，并且提供数据库原生自动部署、自动备份、自动容灾、数据恢复、监控等全套解决方案，彻底解决传统关系型数据库对高并发实时交易支持不佳、对超大规模复杂场景分析实时性慢、实现海量存储访问需人工分库分表价格昂贵、弹性扩展能力受限、使用和运维管理复杂等问题，让您更加专注业务发展。



**云溪NewSQL数据库有哪些特性，产品价值是什么？**

**1.高性价比**

在高并发实时交易中，使用通用x86服务器与浪潮自研云溪NewSQL数据库即可达到甚至超越运行于小型机与高端存储之上的大型商业数据库所带来的极致性能；使用统一逻辑数据库代替原先分布式事务中间件，采用数据库系统原生自动分表配合应用无侵入开发的简单架构替代原先分库分表配合应用侵入式开发的复杂架构，解决了系统性能随硬件性能非线性增长的高原效应瓶颈，实现了系统高性能线性增长。低成本获得高可用、灾备、水平扩展、关系数据库的事务与海量NoSQL规模能力。

**2.无限水平扩展，总体拥有成本低**

采用弹性横向扩展架构彻底解决传统垂直扩展架构扩展能力受限问题，稳定的产品，完善的运维和技术支持，多种实例规格配置覆盖不同业务规模场景，根据业务量调整资源使用，总体拥有成本低。

**3.金融级分布式HTAP数据库**

云溪NewSQL分布式数据库的一致性的同步复制，保证节点间数据强一致性，可以满足金融客户等特定行业业务上云的要求。

**4.基于PostgreSQL生态，易于上手**

云溪NewSQL高度兼容PostgreSQL协议和语法，便于更多有经验的开发者使用，DBA运维轻松容易。

**5.云原生应用**

支持容器化部署，按需使用数据规模。支持使用Kubernetes编排系统进行配置；通过云管理工具实现多租户管理，基础设施即代码，开箱即用服务，应用开发简洁容易

**6.安全可靠自主可控**

浪潮自研云溪NewSQL数据库100%自主知识产权，自主可控。云溪NewSQL原生支持多数据中心容灾机制，无单点故障，保障数据安全。多租户能力，保障云环境下支持多种级别的物理与逻辑隔离。浪潮具备从国产CPU平台服务器、国产操作系统、国产云平台到国产数据库与中间件软件的全线产品，自主可控的“服务器+数据库”生态模式。



**云溪NewSQL数据库有哪些典型的应用场景？**

金融级商业数据库应用场景

企业的核心业务系统一般都是 OLTP应用场景，该领域运行于小型机服务器之上的国外品牌商业数据库一直占据市场主导位置，而在互联网领域以云溪NewSQL为代表的分布式数据库基于通用 x86 服务器便可轻松支撑起上亿的用户访问。

云溪NewSQL完整支持分布式事务、强一致、多副本高可用，满足分布式核心交易业务需求完全基于云计算理念实现，具有更高性价比、更强大的扩展性和更强自动伸缩能力，也更易于维护，同时支持云服务模式与独立部署，既具有云架构的敏捷与弹性，也兼顾了独立性与高性能，既可满足传统核心应用对安全与性能的要求，又能轻松实现业务上云。

经过验证，基于通用x86服务器部署浪潮自研分布式数据库云溪NewSQL在性能和系统稳定性上甚至高于基于小型机服务器与高端存储设备部署的国外品牌商业数据库。

多地部署异地多活场景

政务、银行以及大型企业的组织架构通常包括总部、分部及各分支架构，各类业务信息通过数据库数据向总部聚合。浪潮云云溪NewSQL分布式云数据库具有原生数据强一致性的独特优势，支持统一部署，数据地理分区，高延迟网络条件下的数据一致性技术、权限控制区域数据访问，分布式的多副本强一致，可以满足中央地方多级多地部署需求。

海量数据存储访问场景

面对物联网、交易订单等数据库实例超过 TB 级别且持续快速增长，超过单机数据库存储能力极限的情况，浪潮云分布式云数据库云溪NewSQL服务系统是支持持节点水平扩展，存储容量最大到4EB，完全满足用户的海量数据存储和查询要求。可以广泛应用于工业远程监控和远程控制、智慧城市的延展、智能家居、车联网、充电桩加油站等传感监控设备多、采样率高、数据上报存储数据量大的场景。

高并发实时交易（OLTP）与海量数据分析(OLAP)混合场景（HATP）

云溪NewSQL可同时支持在线事务处理 (OLTP) 与在线分析处理 (OLAP)，用户可基于一套系统HTAP混合负载同时运行联机交易与批处理任务且互不干扰，降低数据存储成本。要在同一个数据库中针对同样的数据在同一时刻运行两种不同类型的业务，数据库服务器中的 CPU、内存、I/O 和网络等硬件资源会形成较多的资源争用，导致对外的联机交易服务性能与稳定性受到影响。

在浪潮云溪NewSQL分布式云数据库中，用户可以针对复制组的多副本，在节点和会话等多个级别指定读写分离策略分别服务于联机交易业务与统计分析业务，做到针对同样数据的联机交易与统计分析业务同时运行且互不干扰。可广泛应用于工业物联网、商业智能分析、电商推荐系统、搜索引擎等业务场景。

高并发实时交易OLTP

传统关系型数据库应对高并发在线事务处理时只能采用分布式事务中间件、手动分库分表、对应用进行侵入式开发的解决方案，但存在架构复杂、应用开发成本高且性能存在瓶颈的弊端。

浪潮云溪NewSQL分布式云数据库原生提供弹性扩展能力，性能随容量扩容而线性提升，能够极大提升数据库处理能力，可以面对类似全网推广、限时秒杀等营销活动，轻松应对单表规模＞5000万行的高并发在线交易事务；配合热点更新，强同步复制能力，即使是敏感交易类业务也可以完全胜任。

海量数据分析OLAP

基于通用的公共技术底座和可选插件，满足100% 的 OLTP 场景和 大部分OLAP场景。

安可应用场景（国产操作系统及CPU等硬件适配）

 

**云溪NewSQL数据库存储容量有多大？横向扩展和纵向扩展能力如何？**

理论上最大可以支撑4EB的数据存储

浪潮云溪横向扩展能力：

1、添加更多节点可以提升整个集群的存储容量，经测试，浪潮云溪数据库可支持512节点大规模部署，每个节点都可以正常运行。

2、客户端的查询请求可以发送到集群任意节点，且每个查询可独立并发执行（无论有无冲突），意味着集群的吞吐能力可以随着节点数的增加线性提升。

3、查询以分布式任务的方式在各个数据节点并发执行，可以通过增加节点数来提升单个查询的性能。

浪潮云溪纵向扩展能力：

1、添加更多的cpu和内存，提升服务器的处理能力。

2、添加更多的磁盘，数据可以平均的存储到各个磁盘当中，客户端的查询请求可以在各个磁盘中并行扫描，提升单个查询性能。经测试，浪潮云溪数据库单物理机可支撑10TB数据场景。

3、支持表分片，将一张表中的数据按照预先设定的逻辑进行分区存储，查询时可以并行扫描每个分区中的数据，并且不去扫描不在查询条件中的分区的数据，从而提升查询性能。

 

**云溪NewSQL数据库是否支持在线扩容和升级？**

在线扩容：浪潮云溪集群的扩容非常简单，只需要将一个节点加入到现有的集群中，扩容操作可以在秒级完成，集群能自动完成数据的重分布，无需人工干预。

在线升级：由于浪潮云溪的多活高可用设计，数据库集群允许滚动升级（rolling upgrade），也就是可以一次升级一个节点，而不会中断群集的整体运行和操作。

 

**云溪NewSQL数据库是否应支持数据同步功能，能实时、离线方式进行数据迁移？****

Change data capture(CDC)功能提供高效的、分布式、行级别的数据变化推送。可以通过CDC将变化推送到下游处理，比如你可能希望将数据的所有内容发送到全文检索，分析型数据库或者大数据系统。CDC的一个核心特性为变化推送（changefeed），通过设置被检视的表格，changefeed会将表格中所有行的每次变化以特定的格式(JSON或Avro)发送记录，发送到配置的Kafka连接中。

浪潮云溪支持从CSV/TSV文件导入数据的能力，并且支持使用mysql，oracle，postgres，DB2等主流数据库的dump文件迁移数据。支持在线和离线迁移两种模式，在线数据迁移时，数据库服务不受影响，可以正常对外服务。

 

**云溪NewSQL数据库是否具备线程池能力？**

浪潮云溪基于go语言开发，具备线程池能力，可以提高程序的执行效率

 

**云溪NewSQL数据库是否具备数据审计能力？**

浪潮云溪分布式数据库具备数据安全审计功能，主要审计类别包括操作行为，审计日志，审计规则，审计动作。审计日志记录用户对数据库的各种操作行为，根据审计规则，触发审计动作（告警、 断开连接等）。

审计日志可用于查询、生成报表、可视化展示等。也可用于挖掘潜在的风险，优化审计规则。一旦出现安全问题，可追踪用户行为。操作行为支持分级分类，审计日志可独立存储，并且一经写入无法更改，同时可以配置日志存储的策略，审计规则中可进行开关控制，支持实时触发审计动作，支持入侵检测，支持告警，特定场景有实时阻断功能。

 

**云溪NewSQL数据库是否支持连接加密和透明加密能力？**

为了保护 数据库集群的节点间和客户端之间的通信安全，通过可视化工具链接数据库时需要提供 Certificate Authority（CA），用于签署密钥和证书（SSLs），签名对象包括：Nodes 和Clients。同时在不同的情况下，数据库允许在线更换安全证书，例如节点或客户端 CA 证书即将到期，安全策略要求定期进行证书更换，密钥已被泄露等，来保证安全。 

  浪潮云溪分布式数据库具备透明加密功能，可通过对未授权用户使用密文的方式保护数据库安全。

 

**云溪NewSQL数据库是否支持权限管控和角色划分，例如避免普通用户误操作、删除关键元数据等**

浪潮云溪分布式数据库的用户需要规划好不同数据库用户的职责，并给它赋予相应的操 作权限，以保证数据库的安全。用户权限分为多个等级，对应有不同的适应级别，用户必须拥有目标数据库或表的 GRANT 权限才能为其他用户或角色进行赋权。

 

**云溪NewSQL数据库是否具备性能分析功能，能输出诊断报告？**

浪潮云溪分布式数据库提供数据库监控平台，包含性能分析功能，通过可视化图形曲线，详细刻划出硬件，存储，分布，队列，副本等相关性能值和趋势，同时可根据选择时间，输出相关诊断报告，用于运维，开发人员的性能优化，故障诊断，扩容升级等工作。

 

**云溪NewSQL数据库是否具备数据库的监控和报警能力？**

浪潮云溪分布式数据库提供数据库监控平台，包含集群节点及其活动状态，存储使用率，Ranges 状态，正常运行时间和关键硬件指标的详细信息。监控平台详细页面中包含多个指标，有硬件，运行时，SQL，存储，副本，分布，队列，慢查询，CDC等，分为节点视图和集群视图。

 

**云溪NewSQL数据库的高可用性和负载均衡是如何实现的？**

浪潮云溪分布式数据库是分布式关系型数据库，使用Raft一致性算法复制您的数据以获得可用性，数据被复制成多副本存储在集群不同的节点中，副本数量可设置。

例如三个副本的环境下，每个副本可以位于不同的位置：

1、如果副本分布于同一台服务器上的多块磁盘，可以容忍单块磁盘故障。

2、如果副本分布于同一机架上的不同服务器，可以容忍单台服务器故障。

3、如果副本分布于同一个数据中心不同机架，可以容忍单个机架电源和网络故障。

4、如果副本分布于不同数据中心，可以容忍大规模网络中断或断电。

N为总副本数，F为可容忍故障副本数，则N=2F+1。（例如，三副本可以容忍一个副本故障，五副本则可以容忍两个副本故障，以此类推）。

对于短时间的故障（例如服务器重启），浪潮云溪通过Raft保持绝大多副本可用，来实现无缝服务。Raft保证了每组副本在前任Leader故障的时候能够选举出新的Leader，使得事务能够继续进行，而受影响的副本再次上线的时候能够再次加入到副本组。

对于长时间的故障（例如服务器或机架长时间故障或是数据中心停运），浪潮云溪能够使用不受故障影响的副本作为数据源，恢复并自动重新平衡丢失节点上的副本。集群能够通过Gossip网络提供的存储空间信息，识别新的节点。然后使用所有的可用节点、硬盘和网络的带宽，以分布式的方式重新进行复制出丢失的副本。

对于负载均衡技术，浪潮云溪使用haproxy来实现，其最高极限支持10G的并发。

1、在性能方面，负载均衡器将客户端流量分布在各个节点上。这样可以避免一个节点处理过大的流量，并提高了整体集群性能（每秒查询）。

2、在高可用性方面，负载均衡器可以尽量避免集群中节点发生故障对客户端产生影响。如果节点发生故障，负载均衡器会将客户端流量重定向到可用节点。

 

## **SQL相关FAQ**

FAQ整理现有常见问题和解决方法，日后再去从项目和实际应用中积累）

**如何批量导入数据到云溪NewSQL？**

目前，我们能够使用一批INSERT语句（每条语句包含数据的大小不超过几MB）来实现批量导入。一行数据的大小决定了一个批量INSERT适合一次性处理多少行，一般来说1,000到10,000行表现最佳。

 

**如何在云溪NewSQL中自动生成唯一的行ID？**

自动生成唯一的行ID的方式是使用[UUID](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/data-types/uuid)列，并设置get_random_uuid()[函数](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/functions-and-operators#id-generation-functions)作为[默认值](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/constraints/default-value)。

```
CREATE TABLE t1 (id UUID PRIMARY KEY DEFAULT gen_random_uuid(), name STRING);

INSERT INTO t1 (name) VALUES ('a'), ('b'), ('c');

SELECT * FROM t1;

+--------------------------------------+------+

|         id         | name |

+--------------------------------------+------+

| 60853a85-681d-4620-9677-946bbfdc8fbc | c  |

| 77c9bc2e-76a5-4ebc-80c3-7ad3159466a1 | b  |

| bd3a56e1-c75e-476c-b221-0da9d74d66eb | a  |

+--------------------------------------+------+

(3 rows)
```

此外，用户也可以使用[BYTES](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/data-types/bytes)类型的列，并设置uuid_v4()函数作为默认值。

```
CREATE TABLE t2 (id BYTES PRIMARY KEY DEFAULT uuid_v4(), name STRING);

INSERT INTO t2 (name) VALUES ('a'), ('b'), ('c');

SELECT * FROM t2;

+---------------------------------------------------+------+

|            id             | name |

+---------------------------------------------------+------+

| "\x9b\x10\xdc\x11\x9a\x9cGB\xbd\x8d\t\x8c\xf6@vP" | a  |

| "\xd9s\xd7\x13\n_L*\xb0\x87c\xb6d\xe1\xd8@"    | c  |

| "\uac74\x1dd@B\x97\xac\x04N&\x9eBg\x86"      | b  |

+---------------------------------------------------+------+

(3 rows)
```

上述两种情况，生成的ID大小都是128bit。数值范围足够大，因此几乎没有机会生成不唯一的值。同时一旦表数据增长达到一个KV Range（默认情况下超过64MB）阈值，新的ID将会分布到表的所有Range，并随着这些Range尽量分布到不同的节点。这意味着负载将由很多个节点分担。

如果用户必须要求生成的ID存储在同一个键值Range内，则可以使用[SERIAL](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/datatypes/serial)数据类型，此时相当于使用unique_rowid作为默认值的INT类型。



```
CREATE TABLE t3 (id SERIAL PRIMARY KEY, name STRING);

INSERT INTO t3 (name) VALUES ('a'), ('b'), ('c');

SELECT * FROM t3;

+--------------------+------+

|     id     | name |

+--------------------+------+

| 293807573840855041 | a  |

| 293807573840887809 | b  |

| 293807573840920577 | c  |

+--------------------+------+

(3 rows)
```

在插入过程中，unique_rowid函数根据时间戳和节点ID生成的默认值。像这样按时间有序的值能够保证全局唯一性，除非每个节点在一秒内生成了超量的ID（100,000+）。

 

** **如何在云溪NewSQL中生成唯一且递增的有序数列？**

在云溪NewSQL中使用unique_rowid()内置函数或[SQL序列](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/sql-statements/create-sequence)可以生成的有序数列。然而用户需要注意以下几个方面：

除非用户需要严格有序的数字，否则我们推荐使用[UUID](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/data-types/uuid)替代，更多细节可以查看上一个的FAQ。

序列能够生成唯一的ID，但是并非所有值都能最终生效（例如一个事务在获取序列值之后被取消了），且这些值在底层可能会被细微地重新排序（例如一个使用较小序列值的事务在另一个使用较大序列值的事务之后提交）。

从性能最佳的角度考虑，推荐用户尽量避免使用序列或unique_rowid()函数生成行ID或是索引列。同时使用这两种方式生成的值在数值上十分接近，可能会导致在插入数据的时候部分Range的资源竞争。因此，我们推荐使用[UUID](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/data-types/uuid)。

UUID、sequences和unique_rowid()的区别是？

| 属性                 | uuid_v4()生成的UUID      | unique_rowid()生成的INT            | 序列                       |
| -------------------- | ------------------------ | ---------------------------------- | -------------------------- |
| 大小                 | 16字节                   | 8字节                              | 1到8字节                   |
| 属性有序列           | 无序                     | 高度时间有序                       | 高度时间有序               |
| 生成的性能开销       | 小，可伸缩               | 小，可伸缩                         | 随机，可能导致竞争         |
| 值分布               | 均匀分布   （128 bits）  | 包含时间和位置（节点ID）的两部分   | 分布紧密，生成值较小       |
| 数据局限性           | 最大程度地分散数值的分布 | 短时间内生成的值在同一数值范围内   | 高度局部性                 |
| 作为主键INSERT时延   | 小，对并发度不敏感       | 小，但是时延会随着并发度提高而增加 | 较大                       |
| 作为主键INSERT吞吐量 | 最高                     | 受限于一个节点的最大吞吐量         | 受限于一个节点的最大吞吐量 |
| 作为主键时的读吞吐量 | 最高（最大限度的并发度） | 受限                               | 受限                       |

 

 

**如何根据云溪NewSQL的时间，对表的写入操作进行排序？**

大多数要求严格基于时间保证写入顺序的用例，可以替换成其他的分布式友好的解决方案来解决。

例如，可以考虑使用云溪NewSQL的[Time Travel查询AS OF SYSTEM TIME](https://www.cockroachlabs.com/blog/time-travel-queries-select-witty_subtitle-the_future/)，它支持以下特性：

对表或数据集的所有更改进行分页。

判断所有修改的时间顺序。

判定过去某些时刻的数据状态

判定两个时间点之间数据的变化。

此外，还可以考虑使用在上一个FAQ中unique_rowid()，生成大致时间有序的值。

然而，如果你的应用程序确实是需要严格基于时间保证写入顺序，云溪NewSQL能够提供一个严格的单调计数器，顺序递增：

\#初始化

```
CREATE TABLE cnt(val INT PRIMARY KEY); INSERT INTO cnt(val) VALUES(1);
```

\#在每次事务中

```
INSERT INTO cnt(val) SELECT max(val)+1 FROM cnt RETURNING val;
```



这将会导致INSERT事务相互之间产生冲突，强制限定集群中事务在同一时刻内只允许一个事务运行，从而保证这种方式生成的值是严格有序且无缝的。此时，性能将受到严重的影响。

如果您对这个问题感兴趣，欢迎[联系我们](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/troubleshoot/support-resources)，描述你应用场景。我们很乐意帮助你寻找替代的方案，以便在以后的版本中更好地满足你的需求。

 

**如何获取插入到表当中的最后的ID/SERIAL值？**

云溪NewSQL目前没有办法获取最后插入的值，但是用户可以选择使用带RETURNING子句的INSERT语句。 例如，使用[RETURNING](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/sql-statements/insert#insert-and-return-values)子句返回通过SERIAL或unique_rowid()自动生成的值的SQL语句：

```
CREATE TABLE users (id SERIAL, name STRING);
INSERT INTO users (name) VALUES ('mike') RETURNING id;
```

 

 

**什么是事务冲突？**

当多个客户端在同一时间内提交多个事务操作同一个数据的时候，会发生事务冲突。这会导致事务之间相互进行等待，性能降低，就像是在商店里很多人尝试通过同一个收银员进行结账。

 

**如何知道云溪NewSQL在一个查询中选择了哪些索引？**

可以使用[EXPLAIN](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/sql-statements/explain)语句打印查询计划（其中包括索引使用信息），来查看给定查询在云溪NewSQL中使用哪些索引：

```
EXPLAIN SELECT col1 FROM tbl1;
```

如果需要显示地指定查询计划器针对某个查询使用哪个索引，可以使用[带索引提示的特殊语法](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/sql-syntax/table-expressions#force-index-selection)。

```
SELECT col1 FROM tbl1@idx1;
```

 

**如何记录慢查询？**

有几种方式来记录SQL查询，记录类型取决于用户的需求。

对于表级别的日志，请打开SQL审计日志；

对于系统级的故障追踪定位和性能调优，请查看集群范围的执行日志；

对于本地测试，可以打开单节点执行日志；

l SQL审计日志

WARNING: 这是实验性的功能，该功能的接口和输出可能会发生改变。

如果用户需要记录发生在特定表上的所有查询，SQL审计日志将会非常有用。

相关向导可以查看[SQL审计日志](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/maintain/sql-audit-logging-(experimental))。

相关的SQL文档，可以查看[ALTER TABLE ... EXPERIMENTAL_AUDIT](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/sql-statements/experimental-audit)。

l 集群范围的执行日志

对于生产集群，记录所有查询的最佳方式是打开[集群配置项](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/deploy/cluster-settings)sql.trace.log_statement_execute：

```
 SET CLUSTER SETTING sql.trace.log_statement_execute = true;
```

一旦该配置项打开，集群中的每个节点将输出其执行的所有查询到节点日志文件。在不需要该功能的时候则可以关闭该配置项：

```
SET CLUSTER SETTING sql.trace.log_statement_execute = false;
```

l 单节点执行日志

如果用户需要本地测试云溪NewSQL，想要记录指定节点执行的查询，则用户可以在节点启动的时候传递CLI Flag，也可以在一个已经运行的节点上执行一个SQL函数打开单节点日志记录功能。

使用CLI启动一个新的节点，需要启动节点时指定--vmoduleFlag，例如执行以下命令启动一个本地节点并记录该节点执行的所有查询：

```
cockroach start --insecure --host=localhost --vmodule=exec_log=2
```

另一种方式是在一个运行中节点的SQL提示符处，执行crdb_internal.set_vmodule()[函数](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/functions-and-operators)：

```
SELECT crdb_internal.set_vmodule('exec_log=2');
+---------------------------+
| crdb_internal.set_vmodule |
+---------------------------+
|                         0 |
+---------------------------+
(1 row)
```

一旦日志打开，该节点的所有查询将通过以下格式输出到[云溪NewSQL日志文件](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/troubleshoot/understand-debug-logs)中：

```
I180402 19:12:28.112957 394661 sql/exec_log.go:173  [n1,client=127.0.0.1:50155,user=root] exec "psql" {} "SELECT version()" {} 0.795 1 ""
```

**不使用**ORDER BY**的时候，云溪NewSQL如何排序结果？**

若一个查询没有使用[ORDER BY](http://doc.cockroachchina.baidu.com/#faqs/sql-faqs/develop/sql-syntax/ordering-query-results)子句，行的处理或返回将以非确定的顺序进行。“非确定”意味着实际的顺序受到逻辑计划、硬盘上存储的数据的顺序、云溪NewSql集群的拓扑结构的影响，而以上影响因子会在系统运行过程中随时变化。

 

## **操作相关FAQ**

**当用户尝试以后台启动的方式启动Cockroach进程，进程为什么会挂住？**

首先需要确定您在此之前是否使用同样的数据文件夹去启动另一个多节点集群。如果没有，则查看[集群配置相关的问题追踪与定位](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/troubleshoot/troubleshoot-cluster-setup)文档。如果用户先前启动并已经停止一个多节点集群，当前正在进行该集群的重启过程，则该挂起现象属于正常现象。

为了保证数据的一致性，只有在集群的绝大多数节点处于运行状态时云溪NewSql才能开始工作。这意味着如果3节点集群只有1个节点正在运行，该节点则无法做任何事情。cockroach start的--backgroundFlag会使得启动命令一直处于等待状态，直到节点完全初始化并且能够开始处理查询的为止。

总结来说，在绝大多数节点正常运行之前，--backgroundFlag将导致cockroach start命令挂起。为了重启集群，用户需要开启多个终端开启每个节点，或者，在一个终端中以当前shell的后台启动方式启动所有节点。

 

**在节点没有多少流量的时候，为什么内存使用量还会增加？**

像大多数数据库一样，云溪NewSql会缓存最近最常访问的数据到内存当中，以提供更好的读性能。另一方面云溪NewSql周期性写时间序列数据会导致数据量不断增长，直到达到某个阈值。

 

**在没有多少写操作的时候，为什么硬盘的使用量还会增加？**

集群中存储的时间序列数据将支撑Admin界面的部分监控功能，在被删除之前的将在集群中保存30天。结果将是在集群整个生命周期的前30天，用户将会看到硬盘使用量在稳定增长。即使不写入业务数据，Range数量也不断变多。

 

**是否能够减少或关闭时间序列数据的存储？**

是的。默认情况下云溪NewSql存储最近30天的时间序列数据，将在Admin界面监控界面显示。用户可以减少时间序列数据的存储周期，或是关闭整个时间序列数据的存储。

**NOTE:** 在减少或关闭时间序列数据存储之后，云溪NewSql将花费24小时来删除时间序列数据，变更结果将反映到Admin界面上。

为了减少时间序列数据的存储周期，用户需要将集群配置项timeseries.resolution_10s.storage_duration修改成小于720h0m0s（30 days）的值。例如，如果想要存储最近15天内的时间序列数据，则执行以下命令：

```
SET CLUSTER SETTING timeseries.resolution_10s.storage_duration = '360h0m0s';
SHOW CLUSTER SETTING timeseries.resolution_10s.storage_duration;
 
+--------------------------------------------+
| timeseries.resolution_10s.storage_duration |
+--------------------------------------------+
| 360h                                       |
+--------------------------------------------+
(1 row)
```

关闭整个时间序列数据存储

**NOTE:** 只有用户使用了例如Prometheus这样的第三方监控工具进行时间序列监控，才推荐关闭时间序列数据存储。像Prometheus这样的工具能够不依赖于云溪NewSql去存储时间序列数据，只是从云溪NewSql中获取内存中的各项指标数据并保存到自己的存储设备上。

执行以下命令关闭整个时间序列数据存储：

```
SET CLUSTER SETTING timeseries.storage.enabled = false;
SHOW CLUSTER SETTING timeseries.storage.enabled;
+----------------------------+
| timeseries.storage.enabled |
+----------------------------+
| false                      |
+----------------------------+
(1 row)
```

如果用户需要删除所有的时间序列数据，需要修改相关集群配置项：

```
SET CLUSTER SETTING timeseries.resolution_10s.storage_duration = '0s';
```

 

**为什么增加节点的数量无法提高整体IOPS？**

如果查询操作的是不同的数据，则增加节点的数量能够提高整体的吞吐量（TPS或QPS）。然而，如果查询发现在同样的数据上，则可能会出现事务冲突。更多细节可以查看[理解和避免事务冲突](https://www.cockroachlabs.com/docs/stable/performance-best-practices-overview.html#understanding-and-avoiding-transaction-contention)。

**云溪NewSql在默认情况下为什么要收集集群使用相关的匿名信息？**

收集云溪NewSql在真实世界中的使用信息能够帮助我们调整产品功能开发的优先级。我们默认选择为“选择加入”，以增加我们从收集工作中获取到的信息。我们也会慎重地只发送加密后的聚合统计数据。更多信息可以查看[诊断报告](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/maintain/diagnostics-reporting)，了解哪些数据会被发送。

**节点时钟未正确地同步会导致什么结果？**

云溪NewSql需要中等程度的时钟同步来保持数据的一致性。基于这个原因，当节点检测到它的时钟与集群至少一半节点的时钟不同步且偏移量超过最大允许阈值的80%（默认500ms）的时候，该节点会自发地关闭。这能够避免了违反可序列化一致性级别，否则将导致的陈旧数据的读取（stale read）和写倾斜（write skew）。通过在每个节点上运行NTP或是其他时间同步软件来避免时钟在最开始的位置就出现过多的偏移，是十分重要的。

一个不常见的用例是，一个节点的时钟在节点检测到变化之前突然跳跃，偏移量超过了最大允许的阈值。这种情况不常见，但是还是可能发生的。例如，当云溪NewSql运行在VM中，VM管理程序决定将VM迁移到另一台具有不同时间的设备上。在这种情况下，从时钟变得不同步开始，到节点检测到并自发关闭，两个时间点之间会有一个小的时间窗口。在这个时间窗口内，客户端可能会读取陈旧数据，并写入基于陈旧数据生成的新数据。

更多关于时钟同步的向导，可以查看对应环境的部署教程：

| 部署环境                                                     | 选择建议                                                |
| ------------------------------------------------------------ | ------------------------------------------------------- |
| [内部部署](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/manual-deployment/on-premises) | 将NTP与Google的外部NTP服务配合使用                      |
| [AWS](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/manual-deployment/aws) | 使用亚马逊时间同步服务                                  |
| [Azure](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/manual-deployment/azure) | 关闭Hyper-V时间同步，将NTP与Google的外部NTP服务配合使用 |
| [Digital Ocean](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/manual-deployment/digital-ocean) | 将NTP与Google的外部NTP服务配合使用                      |
| [GCE](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/manual-deployment/google-cloud-platform-gce) | 将NTP与Google的外部NTP服务配合使用                      |

**TIPS**: 在多数情况下，我们推荐将NTP与Google的外部NTP服务配合使用，原因是Google提供了闰秒涂抹的技术。如果你采用了不提供[闰秒涂抹](https://developers.google.com/time/smear)的NTP服务，你必须手动配置客户端进行闰秒抹除，并保证在每台机器上均以相同的方式运行。

**如何判断节点时钟同步情况的好坏？**

根据[监控相关的文档](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/monitoring-and-alerting/overview#prometheus-endpoint)里的详细描述，用户可以通过每个云溪NewSql节点的http://<host>:<http-port>/_status/vars获取一系列指标，内容的格式能够直接被主流的Prometheus时间序列数据库解析使用。其中有2个指标能够描述每个节点跟其他节点的时钟偏移程度。

| 指标                     | 定义                                           |
| ------------------------ | ---------------------------------------------- |
| clock_offset_meannanos   | 节点与其他节点的时钟偏差的平均值，单位为纳秒。 |
| clock_offset_stddevnanos | 节点与其他节点的时钟偏差的标准差，单位为纳秒。 |

 

如果一个节点与其他节点相比的时钟偏差的平均值超过最大允许偏差的80%，则这个节点会自发关闭。因此推荐监控clock_offset_meannanos指标，并将集群配置的最大允许偏移量的80%作为报警阈值。

用户可以在v2.0以上的版本，在Admin界面的时间偏差图中查看到的相关指标。

**节点维护时需要准备什么？**

默认情况下，如果一个节点下线超过5分钟，则集群将认为该节点已死亡，会将数据重分布到其他节点。如果需要临时停止节点并进行有计划性的维护（例如更新系统软件），且维护时间超过5分钟，则此时用户可以根据维护时长去增加集群配置项server.time_until_store_dead的值，来避免不必要的数据重平衡。

例如，如果用户想要维护一组服务器，集群节点运行在这些服务器上，每个服务器需要下线超过15分钟，则在关闭节点之前，用户需要修改集群配置项：

```
SET CLUSTER SETTING server.time_until_store_dead = '15m0s';
```

在完成维护，[重启节点](http://doc.cockroachchina.baidu.com/#faqs/operational-faqs/deploy/start-a-node)之后，再将集群配置项修改成原来的值：

```
SET CLUSTER SETTING server.time_until_store_dead = '5m0s';
```

需要确保负载均衡器不会将客户端流量路由到关闭的节点上，即使服务器只是下线几秒钟。如果发现负载均衡器的健康检查不能把即将关闭的节点识别成unready状态，则需要增加集群配置项server.shutdown.drain_wait的值，该配置能够告诉节点在特定时间内以unready的状态进行等待。

```
SET CLUSTER SETTING server.shutdown.drain_wait = '10s';
```

