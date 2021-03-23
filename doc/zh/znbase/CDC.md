# CDC

## 概述

​		Change data capture(CDC)功能提供高效的、分布式、行级别的数据变化推送。

​		可以通过CDC将变化推送到下游处理，比如你可能希望将数据的所有内容发送到全文检索，分析型数据库或者大数据系统。

​		CDC的一个核心特性为变化推送（changefeed），通过设置被检视的表格，changefeed会将表格中所有行的每次变化以特定的格式(JSON或Avro)发送记录，发送到配置的Kafka连接中。

#### **顺序保证**

-   节点错误或网络分裂等问题出现时可能使得重复发送changefeed，这可以保证changefeed至少交付一次。

-   带有时间戳的变化被交付后，不会再有对应行带有更早时间戳的变化被发送。

-   如果一行数据在一则事务中被多次修改，只会发送最后的变化结果。

-   数据到Kafka的分区中依据数据库中行的主键进行划分。

-   使用选项UPDATED在发送时为每一行添加一个"updated"时间戳。也可以用RESOLVED选项发送周期性的"resolved"时间戳信息到每一个Kafka分区。一个"resolved"的时间戳可以保证更早时间戳的行不会被发送到该分区。

-   刨去重复发送的情况，一行数据的发出顺序与事务更新该行的顺序相同。但是不保证不同行的数据变化总与事务更新它们的顺序相同。根据"updated"时间戳可以比较两行变化的发生顺序，这适用于同一集群中任意两个部分（如不同的表格间，不同的节点间）。"resolved"时间戳可以比较不同时间戳并缓存这些记录，来保证强顺序性和全局一致性。

#### **带有列回填的模式变化**

​		当被检视的行发生了带有列回填的模式变化（例如，添加一个带有默认值的列，添加一个计算的列，添加一个NOT NULL的列，删除一列）时，changefeed将在回填阶段重复发送一些变化。结束后，用一个新的模式输出所有受检视的行。使用Avro时，被模式变化回填的行也会被重复发送。

举一个带有列回填的模式变化的例子，创建一个changefeed像下面这样发送信息：

> [1] {"id": 1, "name": "Petee H"} [2] {"id": 2, "name": "Carl"} [3] {"id": 3, "name": "Ernie"} 


添加一列到受检视的表格中：

> \>ALTER TABLE office_dogs ADD COLUMN likes_treats BOOL DEFAULT TRUE; 


在使用新的模式输出记录前，重复发送三条changefeed信息：

> [1] {"id": 1, "name": "Petee H"} 
>
> [2] {"id": 2, "name": "Carl"} 
>
> [3] {"id": 3, "name": "Ernie"} 
>
> [1] {"id": 1, "name": "Petee H"}Duplicate 
>
> [2] {"id": 2, "name": "Carl"} \# Duplicate 
>
> [3] {"id": 3, "name": "Ernie"} \# Duplicate 
>
> [1] {"id": 1, "likes_treats": true, "name": "Petee H"} 
>
> [2] {"id": 2, "likes_treats": true, "name": "Carl"} 
>
> [3] {"id": 3, "likes_treats": true, "name": "Ernie"}



#### **使用rangefeed减少发送延迟**

​		原本在创建changefeed后，要通过定期发送请求获得最近的变更。现在它可以保持一个长时间的连接(即rangefeed)，在变化发生的时候推送这些记录。这将减少行变化的等待时间，也减少受检视的表格在一些工作负载中发生的事务重启。

​		要打开rangefeed，设置cluster setting中的kv.rangefeed.enabled到true。如果这个设置开启，任何的创建changefeed都将会报错。注意目前开启rangefeed会有一点性能消耗(大约5%-10%的延迟提高)，无论是否在changefeed中使用了rangefeed。

如果遇到了问题，可以回退之前的设置，将kv.changefeed.push.enabled设为false。在未来的版本这个设置将会移除。

注意：

​		为已经存在的changefeed启动rangefeed必须先重启changefeed。暂停并恢复job实现重新启用changefeed。

​		cluster setting的kv.closed_timestamp.target_duration设置可以用在集群推送的changefeed中。已完成的时间戳将至少在这个设定的时间长度后，减少时间长度会导致集群中事务的多次重启，继而影响性能。

#### **配置changefeed**

可以创建，暂停，恢复，取消，监控，调试changefeed。

##### 创建

创建一个changefeed：

> \>CREATE CHANGEFEED FOR TABLE name INTO ‘scheme://host:port’; 


更多详情，查看CREATE CHANGEFEED。

##### 暂停

暂停一个changefeed：

> \>PAUSE JOB job_id; 


更多详情，查看PAUSE JOB。

##### 恢复

恢复一个暂停的changefeed：

> \>RESUME JOB job_id; 


更多详情，查看RESUME JOB。

##### 取消

取消一个changefeed：

> \>CANCEL JOB job_id;


更多详情，查看CANCEL JOB。



#### 监控changefeed

​		changefeed进程以一个高水位的时间戳暴露给用户，随着changefeed的运行而后移。这保证了所有此前的或者正在本时间戳的所有变化全部发送。你可以像这样监控changefeed：

-   在Admin UI上的Changefeed Dashboard

-   在Admin UI的Jobs page，将鼠标悬停在高水位的时间戳上查看系统时间。

-   使用ZNBase_internal.jobs：

> \> SELECT \* FROM ZNBase_internal.jobs WHERE job_id = \<job_id\>;
>
> job_id \| job_type \| description \| ... \| high_water_timestamp \| error \| coordinator_id 
>
> +--------------------+------------+------------------------------------------------------------------------+ ... +--------------------------------+-------+----------------+ 383870400694353921 \| CHANGEFEED \| CREATE CHANGEFEED FOR TABLE office_dogs2 INTO 'kafka://localhost:9092' \| ... \| 1537279405671006870.0000000000 \| 

在统计数据changefeed.max_behind_nanos上设置警报，追踪changefeed的高水位标记时间戳是否存在落后于集群垃圾回收窗口的风险。更多的内容，查看监控与报警。

注意：

在另一个changefeed结束时可以使用高水位时间戳启动一个新的changefeed。

#### 调试changefeed

​		使用日志信息调试changefeed使用中的连接问题(比如，kafka: client has run out of available brokers to talk to (Is your cluster reachable?)) 寻找日志中存在kafka-producer的行来调试：

> I190312 18:56:53.535646 585 vendor/github.com/Shopify/sarama/client.go:123 [kafka-producer] Initializing new client I190312 18:56:53.535714 585 vendor/github.com/Shopify/sarama/client.go:724 [kafka-producer] client/metadata fetching metadata for all topics from broker localhost:9092 I190312 18:56:53.536730 569 vendor/github.com/Shopify/sarama/broker.go:148 [kafka-producer] Connected to broker at localhost:9092 (unregistered) I190312 18:56:53.537661 585 vendor/github.com/Shopify/sarama/client.go:500 [kafka-producer] client/brokers registered new broker \#0 at 172.16.94.87:9092 I190312 18:56:53.537686 585 vendor/github.com/Shopify/sarama/client.go:170 [kafka-producer] Successfully initialized new client |

#### 使用范例

###### 创建一个连接到Kafka的changefeed

在本示例中，你将为一个单节点集群创建changefeed连接到Kafka sink。

1.  启动服务：

> \$ bini start --insecure --listen-addr=localhost --background 


2.  下载并解压Confluent Open Source platform(包含Kafka服务)

3.  将解压文件移动到confluent-\<version\>文件夹并启动服务：

> \$ ./bin/confluent start 


只需要zookeeper和kafka两个文件。confluent的使用如遇问题，请查看他们的文档。

4. 创建一个Kafka topic：

> \$ ./bin/kafka-topics \\ --create \\ --zookeeper localhost:2181 \\ --replication-factor 1 \\ --partitions 1 \\ --topic office_dogs 

5. 以root用户打开内置的SQL client：

> \$ ZNBase sql --insecure 

6. 打开cluster setting的kv.rangefeed.enabled：

> \> SET CLUSTER SETTING kv.rangefeed.enabled = true; 

7. 创建一个数据库：

> \> CREATE DATABASE cdc_demo; 

8. 设置为默认数据库：

> \> SET DATABASE cdc_demo; 

9. 创建表格并添加数据：

> \> CREATE TABLE office_dogs ( id INT PRIMARY KEY, name STRING); 
>
> \> INSERT INTO office_dogs VALUES (1, 'Petee'), (2, 'Carl'); 
>
> \> UPDATE office_dogs SET name = 'Petee H' WHERE id = 1;

10. 启动changefeed：

> \> CREATE CHANGEFEED FOR TABLE office_dogs INTO 'kafka://localhost:9092'; 
>
> job_id 
>
> +--------------------+ 
>
> 360645287206223873 


这将在后台启动changefeed并返回job_id。changefeed会写入到Kafka。

11. 在一个新的终端进入解压缩的文件夹confluent-\<version\>并启动查看Kafka topic：

> \$ ./bin/kafka-console-consumer \\ --bootstrap-server=localhost:9092 \\ --property print.key=true \\ --from-beginning \\ --topic=office_dogs \$ [1] {"id": 1, "name": "Petee H"} [2] {"id": 2, "name": "Carl"} 


注意初始化扫描显示changefeed启动时的表格状态(因此初始化的”Petee”被忽略掉)。

12. 回到SQL client插入更多数据：

> \> INSERT INTO office_dogs VALUES (3, 'Ernie'); 

13. 回到查看Kafka topic的终端，可以看到下面的输出：

> \$ [3] {"id": 3, "name": "Ernie"} 

14. 完成后退出SQL shell(\\q)

15. 停止服务，执行以下命令：

> \$ ZNBase quit --insecure

16. 停止Kafka，进入解压缩的confluent-\<version\>文件夹并停止Confluent：

> \$ ./bin/confluent stop FEED



## CREATE CHANGEFEED

CREATE CHANGEFEED 语句创建一个新的数据变化推送，提供行级别的推送服务。

#### **权限需求**

changefeed只能被超级用户创建。例如admin角色的成员。admin角色默认存在root用户作为成员。

#### **语法格式**

![](D:/项目/开源项目/文档/znbase/znbase_doc-main/doc/zh/znbase/assets/reference/150a2f35743fc3b748f75330823451e0.png)

#### **参数说明**

| 参数         | 描述                                                    |
| ------------ | ------------------------------------------------------- |
| table_name   | 表格名或以逗号分隔的表格名列表，用来创建changefeed      |
| sink         | sink的位置。URI的schema标明类型。更多信息见下方Sink URI |
| option/value | 可选的选项和对应的值。见下方选项。                      |

**Sink URI**

Sink URI的基本格式如下：

> '[scheme]://[host]"[port]?[query_parameters]' 


Scheme可以是kafka或者下面的任何的云存储sink。

1、Kafka

Kafka sink URI示例：

> 'kafka://broker.address.com:9092?topic_prefix=bar_&tls_enable=true&ssl_user=petee&sasl_password=bones' 


查询参数包括：

| 参数             | 值类型 | 描述                                                         |
| ---------------- | ------ | ------------------------------------------------------------ |
| topic_prefix     | STRING | 在所有的topic名前添加前缀。 例如，CREATE CHANGEFEED FOR TABLE foo INTO ‘kafka://…?topic_prefix=bar_’将会发送在topic为bar_foo下而不是foo下。 |
| tls_enabled=true | BOOL   | 允许 Kafka 连接使用TLS。可以和ca_cert一起使用。              |
| ca_cert          | STRING | 编码的 ca_cert 文件。 如果要编码ca.cert文件，执行base64 -w 0 ca.cert |
| sasl_enabled     | BOOL   | 如果为 true，则使用 SASL/PLAIN 认证。需要 sasl_user 和 sasl_pasword。 |
| sasl_user        | STRING | SASL 的用户名。                                              |
| sasl_password    | STRING | SASL 的密码。                                                |

2、云存储sink

用云存储sink将changefeed数据发送到OLAP数据库或者大数据系统，而无需通过Kafka进行传输。

注意：目前云存储 sink 只支持 JSON 格式，并且使用发送换行符分割的JSON文件。一个云存储sink(即AWS S3) URI的例子：

> 'experimental-s3://test-s3encryption/test?AWS_ACCESS_KEY_ID=ABCDEFGHIJKLMNOPQ&AWS_SECRET_ACCESS_KEY=LS0tLS1CRUdJTiBDRVJUSUZ' 


注意：云存储的scheme应该使用前缀experimental-。

下面的云存储都可以用作 sink：

| [scheme]://[host]/[path]?[parameters] |           |             |                                                              |
| ------------------------------------- | --------- | ----------- | ------------------------------------------------------------ |
| 位置                                  | Scheme    | 主机        | 参数                                                         |
| Amazon S3                             | S3        | Bucket名    | AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_SESSION_TOKEN  |
| Azure                                 | azure     | N/A         | AZURE_ACCOUNT_KEY, AZURE_ACCOUNT_NAME                        |
| Google Cloud                          | gs        | Bucket名    | AUTH (optional; default, implicit, 或 specified), CREDENTIALS |
| HTTP                                  | http      | 远端主机    | N/A                                                          |
| NFS/Local                             | nodelocal | 空或 nodeID | N/A                                                          |
| S3-compatible services                | S3        | Bucket名    | AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, AWS_SESSION_TOKEN, AWS_REGION (可选), AWS_ENDPOINT |

警告：

如果写到多节点的集群的nodelocal本地存储，则单个数据文件将被写入任意节点extern目录，且有可能无法按照预期工作。为了正确运行，每个节点必须使用--external-io-dir选项，指向相同的NFS或其他网络支持的共享存储。

注意：

位置的参数经常包含需要以URI形式编码的特殊的字符，可以用Javascript的encodeURIComponent函数或者Go语言的url.QueryEscape函数编码。其他语言也会提供类似的函数转转义这些特殊字符。

注意：

如果你的环境需要一个HTTP或者HTTPS代理服务器进行Go语言的相关连接，你可以在启动服务前设置基础的HTTP_PROXY和HTTPS_PROXY环境变量。

- 如果AUTH未指定参数，则cluster
  setting的cloudstorage.gs.default.key选项非空，否则AUTH将使用implicit。如果AUTH参数是implicit，所有GCS连接会使用Google的默认认证策略。如果AUTH参数是default，cloudstorage.gs.default.key需要被设为授权过程中使用的服务账户文件的内容。如果AUTH参数是specified，则根据每个语句对GCS连接进行身份验证，允许JSON键对象在CREDENTIALS参数中被发送。JSON键要基于64位编码。

- 你可以用Caddy或者ngix创建自己的HTTP服务。通过设置cloudstorage.http.customer_ca，一个常规的根CA可以追加到系统的默认CA，从HTTPS
  URL确认证书的时候起到作用。

- NFS
  驱动上的文件系统备份位置取决于启动节点--external-io-dir指定的路径。如果该标志设置为disable，则禁止从本地目录和NFS驱动器导入。

- NFS/Local的主机可以为空，也可以为nodeID。如果指定nodeID，也忽略（即，可以向任何节点发送，并且看上去像本地的文件的输入输出）。但是nodeID未来可能会有用。

  3、文件URL例子

| 位置         | 例子                                                         |
| ------------ | ------------------------------------------------------------ |
| Amazon S3    | s3://acme-co/employees.sql?AWS_ACCESS_KEY_ID=123&AWS_SECRET_ACCESS_KEY=456 |
| Azure        | azure://employees.sql?AZURE_ACCOUNT_KEY=123&AZURE_ACCOUNT_NAME=acme-co |
| Google Cloud | gs://acme-co/employees.sql                                   |
| HTTP         | http://localhost:8080/employees.sql                          |
| NFS/Local    | nodelocal:///emloyees.sql nodelocal://2/employees.sql        |

**选项**

| 选项                      | 值                      | 描述                                                         |
| ------------------------- | ----------------------- | ------------------------------------------------------------ |
| updated                   | N/A                     | 包含每行更新的时间戳。 如果提供一个cursor，"updated"时间戳将与发出行的 MVCC 时间戳匹配，并且没有初始化扫描。如果没有提供cursor，changefeed将会初始化扫描（changefeed创建的时候），初始化扫描发出的每个更改记录的"updated"时间戳将为初始化扫描的时间戳。相似的，当为schema更改回填时，将在新的schema有效时将"updated"时间戳设置为第一个时间戳。 |
| resolved                  | INTERVAL                | 定期将resolved的时间戳发送到changefeed。可选择设置发送resolved时间戳之间的最小持续时间。如果未指定，则发送所有 resolved时间戳。 例：resolved='10s' |
| envelope                  | key_only/wrapped        | 用key_only选项只发送键不发送值，可以更快地了解键的变化情况。 默认：envelope=wrapped |
| cursor                    | Timestamp               | 发送指定时间戳后的所有变化，但是不会先发送目前的表状态。如果cursor没有被指定，changefeed开始后先进行初始化扫描并发送当前的值，然后移动，发送扫描后的所有变化。 如果在一个特定的 cursor 后面开始一个changefeed，cursor不能在垃圾回收窗口（gc.ttlseconds）的设定之前；否则changefeed将会错误。如果使用默认的垃圾回收设置，则无法创建过去25小时以前的changefeed。 前面的changefeed全部结束了，可以用cursor开始一个新的changefeed。 例子： CURSOR='1536242855577149065.0000000000' |
| format                    | json/experimental_avro  | 发送记录的格式。目前Avro格式的支持为实验性的。 查看下面的表格，对应格式到Avro。 默认：format=json |
| confluent_schema_registry | Schema Registry address | 使用experimental_avro的时候需要Schema Registry地址。         |
| key_in_value              | N/A                     | 使sink中被删除的行主键可找回（大部分的消息中都有键和值）。这些sink自动使用key_in_value（目前只针对云存储sink）。 |
| diff                      | N/A                     | 使用diff，可以显示出before的状态，即操作之前的状态。 示例（json格式）： CREATE CHANGEFEED FOR TABLE name   INTO 'kafka://host:port'   WITH diff; Avro格式添加diff参数方法一致。 |
| type                      | N/A                     | 使用type，可以显示出每次的操作的类型，包括delete，update和insert。 注意：在使用type时，必须要有diff参数，如果仅仅使用type，就会报错; 示例（json格式）：CREATE CHANGEFEED FOR TABLE name   INTO 'kafka://host:port'   WITH diff，type; Avro格式添加type参数方一致。 |

1、Avro限制

目前对 Avro 的支持是实验性的。下面是 Avro 不支持的 SQL 类型和值：

-   Decimal 必须要明确精度。

-   带有 NaN 或者无限的 Decimal 类型的值在 Avro 中不能被重写。

注意：

为了避免 NaN 或者无限的值，添加一个CHECK的约束来防止这些值插入到decimal的列中。

-   TIME, DATE, INTERVAL, UUID, INET, ARRAY, JSONB,
    BIT和校对的STRING目前在Avro中还不支持。

2、Avro类型

下面是到Avro的类型转换：

| 类型       | Avro 类型 | Avro 逻辑类型 |
| ---------- | --------- | ------------- |
|            |           |               |
| INT        | LONG      |               |
| BOOL       | BOOLEAN   |               |
| FLOAT      | DOUBLE    |               |
| STRING     | STRING    |               |
| DATE       | INT       | DATE          |
| TIME       | LONG      | TIME-MICROS   |
| TIMESTAMP  | LONG      | TIME-MICROS   |
| TIMESTAMPZ | LONG      | TIME-MICROS   |
| DECIMAL    | BYTES     | DECIMAL       |
| UUID       | STRING    |               |
| INET       | STRING    |               |
| JSONB      | STRING    |               |

####  **响应**

信息（即键和值）发送到一个Kafka的topic被指定到一个envelope。默认的格式是 wrapped，输出信息由以下组成：

- 键：一行数据由行的主键字段构成键（如，JSON 的[1]或 Avro 的{"id":{"long":1}}）
- 值：

​          1、三种可能的字段之一：

​                1）、after：包含行在更新后的状态（或DELETE后的null状态）

​                2）、updated：包含更新后的时间戳

​                3）、resolved：用于表示已完成的时间戳记录。这些记录不包含after值，因为本函数仅作为一个检查点。

​        2、 INSERT和UPDATE，表示目前行状态为被插入的或被更新的。

​        3、DELETE操作，null状态。

实例：

| 语句                                          | 返回                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| INSERT INTO office_dogs VALUES (1, 'Petee');  | JSON: [1] {"after": {"id": 1, "name": "Petee"}}                                                                                                                                            Avro: {"id":{"long":1}} {"after":{"office_dogs":{"id":{"long":1},"name":{"string":"Petee"}}}} |
| DELETE FROM office_dogs WHERE name = 'Petee'; | JSON: [1] {"after": null}                                                                                                                                                                     Avro: {"id":{"long":1}} {"after":null} |

#### 语法示例

1.  **创建一个连接到 Kafka 的 changefeed**

> \> CREATE CHANGEFEED FOR TABLE name   INTO 'kafka://host:port'   WITH updated, resolved; 
>
> +--------------------+ 
>
> \|       job_id       \| 
>
> +--------------------+ 
>
> \| 360645287206223873 \| 
>
> +--------------------+ 
>
> (1 row) |

​    创建一个连接到Kafka的changefeed，包含before功能

> \> CREATE CHANGEFEED FOR TABLE name   INTO 'kafka://host:port'   WITH updated, resolved,diff,type; 
>
> +--------------------+ 
>
> \|       job_id       \| 
>
> +--------------------+ 
>
> \| 360645287206223873 \| 
>
> +--------------------+ 
>
> (1 row) |


json格式显示，使用type时，必须有diff

2、**使用 Avro 创建一个连接到 Kafka 的 changefeed**

> \> CREATE CHANGEFEED FOR TABLE name   INTO 'kafka://host:port'   WITH format = experimental_avro, confluent_schema_registry = \<schema_registry_address\>;
>
> +--------------------+ 
>
> \|       job_id       \| 
>
> +--------------------+ 
>
> \| 360645287206223873 \| 
>
> +--------------------+ 
>
> (1 row) |

   使用 Avro 创建一个连接到 Kafka 的 changefeed，包含before功能

> \> CREATE CHANGEFEED FOR TABLE name   INTO 'kafka://host:port'   WITH format = experimental_avro, confluent_schema_registry = \<schema_registry_address\>,diff,type; 
>
> +--------------------+
>
> \|       job_id       \| 
>
> +--------------------+ 
>
> \| 360645287206223873 \| 


使用type时，必须有diff

3、**管理 changefeed**

​		使用如下的语句暂停，继续，取消一个 changefeed。

1）暂停一个 changefeed

> \> PAUSE JOB job_id; 


更多信息查看 PAUSE JOB。

2）继续一个暂停的 changefeed

> \> RESUME JOB job_id;


更多信息查看 RESUME JOB。

3）取消一个 changefeed

> \> CANCEL JOB job_id; 


更多信息查看 CANCEL JOB。

4）从另一个结束的地方开始一个新的 changefeed

找到结束的 changefeed 最后的时间戳：

> \> SELECT \* FROM crdb_internal.jobs WHERE job_id = \<job_id\>;         
>
> job_id       \|  job_type  \| ... \|      high_water_timestamp      \| error \| coordinator_id 
>
> +--------------------+------------+ ... +--------------------------------+-------+----------------+   
>
> 383870400694353921 \| CHANGEFEED \| ... \| 1537279405671006870.0000000000 \|       \|              


用 high_water_timestamp 开始一个新的 changefeed：

> \> CREATE CHANGEFEED FOR TABLE name INTO 'kafka//host:port' WITH cursor = '\<high_water_timestamp\>';


注意:由于提供了cursor，不会进行初始化扫描。

