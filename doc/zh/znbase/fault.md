# **故障诊断**

## 常见故障

#### **1、clock synchronization error: this node is more than 500ms away from at least half of the known nodes (0 of 1 are within the offset)**

| 集群节点所在服务器之间的时间间隔不能超过500ms，需要给服务器之间设置ntp时钟同步服务。 |

#### **2、ERROR：bini server exited with error：failed to create engines：could not open rocksdb instance：Invalid argument** **：bini_comparator：does not match existing comparator drdb_comparator**

在集群升级或集群中加入新节点时，需要保证启动命令中指定的存储路径是空，如果使用原有存储路径，会导致版本不一致的错误。

#### **3、WARNING 084/172733（9466）：Server psql/DRDB-node1 is DOWN,reason: Layer4 connection problem, info：“Connection refused”，check duration：0ms**

配置haproxy高可用时，需要保证各个节点的监听端口可连通，否则haproxy会误认为该节点已宕机。

#### **4、pg：rejected：DELETE without WHERE clause（sql_safe_updates = true）**

这个错误是数据库的保护机制，如果在执行delete或update操作时，没有指定where语句，会提示此信息。可以加 where 1=1 绕过此报错。

 #### **5、pq: cannot write to http://172.31.0.158:10080/sbtes1.sql: error response from server:**
 **405 Method Not Allowed**

 **Method Not Allowed**

 **The requested method PUT is not allowed for the URL /sbtes1.sql/DUMP-CHECKPOINT.**

使用dump命令导出数据时，如果出现以上错误，需要开启httpd的put操作。