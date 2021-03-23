# 事务概述

## 概述

ZNBase支持保证ACID的分布式数事务，提供了以时间戳排序的乐观事务模型。

在ZNBase中，事务是通过使用BEGIN和COMMIT语句包围SQL语句来组织的。

可以使用 `BEGIN [TRANSACTION]` 语句，也可以使用 `START TRANSACTION`语句来显式地开启一个新事务，两者效果相同。并通过COMMIT语句用于提交在当前事务中进行的所有修改。

要使用客户端事务重试，还应包括S`AVEPOINT ZNBase_restart，ROLLBACK TO SAVEPOINT ZNBase_restart`和`RELEASE SAVEPOINT`语句。

示例：

```
> BEGIN;

> SAVEPOINT ZNBase_restart;

<transaction statements>

> RELEASE SAVEPOINT ZNBase_restart;

> COMMIT/ROLLBACK;
```

在提交之前的任何时候，都可以通过执行ROLLBACK语句来中止事务。

| 语法                                  | 说明                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| BEGIN                                 | 启动事务，并控制其优先级和隔离级别。                         |
| SET TRANSACTION                       | 控制事务的优先级和隔离级别。                                 |
| SAVEPOINT ZNBase_restart              | 为事务声明保存点。                                           |
| COMMIT                                | 提交事务。                                                   |
| ROLLBACK                              | 回滚事务，以撤销该事务的修改。                               |
| SHOW                                  | 显示当前的事务设置。                                         |
| ROLLBACK TO SAVEPOINT  ZNBase_restart | 在由于更高优先级的事务同时或最近访问了相同数据而导致事务失败时将事务状态返回至保存点。 |
| RELEASE SAVEPOINT ZNBase_restart      | 释放保存点。                                                 |

**显式事务和隐式事务**

ZNBase可以显式地使用事务，通过 `[BEGIN|START TRANSACTION]/COMMIT` 语句定义事务的开始和结束。或者隐式地使用事务，在默认情况下，单独执行的单条SQL语句均认为隐式事务，在隐式事务的情况下，执行语句后立即进行自动提交。

ZNBase不建议在同一个显式事务中，执行DML和DDL，通过开启集群参数kv.transaction.auto_commit_ddl，会自动提交DDL之前的所有语句，将DDL作为单条语句自动执行并提交后，在开启一个新的显式事务来解决该问题。

## 事务参数

每个事务由两个参数控制：优先级和隔离级别。 

**事务优先级**

ZNBase中的每个事务都被分配了初始优先级。 默认情况下，该优先级为NORMAL，但对于在高竞争场景中应优先考虑的事务，客户端可以在BEGIN语句中设置优先级：

```
> BEGIN PRIORITY <LOW | NORMAL | HIGH>;
```

 或者，客户端可以在事务启动后立即设置优先级，如下所示：

```
 > SET TRANSACTION PRIORITY <LOW | NORMAL | HIGH>;
```

 客户端还可以使用`SHOW TRANSACTION PRIORITY`显示事务的当前优先级。

**隔离等级**

现有的ZNBase支持Serializable（串行化）和Read Committed（读已提交）两种。

```
> BEGIN ISOLATION LEVEL <SERIALIZABLE | READ COMMITTED>;
```

 或者，客户端可以在事务启动后立即设置隔离级别：

```
> SET TRANSACTION ISOLATION LEVEL <SERIALIZABLE | READ COMMITTED>;
```

 客户端还可以使用`SHOW TRANSACTION ISOLATION LEVEL`显示事务的当前隔离级别。

**Serializable**

使用SERIALIZABLE隔离级别，事务的行为就好像它在事务执行期间独占整个数据库。 这是ZNBase提供的最高隔离级别，也是默认的隔离级别。

**Read Committed**

使用READ COMMITTED隔离级别，事务获取其他并发的已提交事务所作的修改。隔离程度较SERIALIZABLE低，但可以使事务读取已经提交的数据，从而可以解决SERIALIZABLE级别下的串行化操作执行顺序造成的读等待写现象，并发度较高。

## 事务竞争

ZNBase中的事务在执行期间锁定写入的数据资源。当一个事务的挂起写入与并发事务的写入冲突时，并发事务必须等待先前的事务完成才能继续。当在事务之间检测到依赖性循环时，具有较高优先级的事务中止依赖事务以避免死锁，被中止事务将被重试。