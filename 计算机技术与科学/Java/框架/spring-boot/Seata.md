1. 什么是Seata？
   开源的分布式事务解决方案（高性能、简单易用）
2. 事务模式
   AT 
   TCC
   SAGA
   XA
3. Seata模型图
   ![[附件/Pasted image 20230321164119.png]]
4. AT模式
- 前提条件：
	1. 基于本地ACID事务的关系型数据库
	2. 使用JDBC连接数据库的Java应用
-  整体机制：
  两阶段提交：
	一阶段：业务数据和回滚日志记录在同一个事务提交，释放本地锁和连接资源。
	二阶段：
		- 提交异步化
		- 回滚通过一阶段的回滚日志进行反向补偿
# 写隔离

-   一阶段本地事务提交前，需要确保先拿到 **全局锁** 。
-   拿不到 **全局锁** ，不能提交本地事务。
-   拿 **全局锁** 的尝试被限制在一定范围内，超出范围将放弃，并回滚本地事务，释放本地锁。 

以一个示例来说明：

两个全局事务 tx1 和 tx2，分别对 a 表的 m 字段进行更新操作，m 的初始值 1000。

tx1 先开始，开启本地事务，拿到本地锁，更新操作 m = 1000 - 100 = 900。本地事务提交前，先拿到该记录的 **全局锁** ，本地提交释放本地锁。 tx2 后开始，开启本地事务，拿到本地锁，更新操作 m = 900 - 100 = 800。本地事务提交前，尝试拿该记录的 **全局锁** ，tx1 全局提交前，该记录的全局锁被 tx1 持有，tx2 需要重试等待 **全局锁** 。

![Write-Isolation: Commit](https://img.alicdn.com/tfs/TB1zaknwVY7gK0jSZKzXXaikpXa-702-521.png)

tx1 二阶段全局提交，释放 **全局锁** 。tx2 拿到 **全局锁** 提交本地事务。

![Write-Isolation: Rollback](https://img.alicdn.com/tfs/TB1xW0UwubviK0jSZFNXXaApXXa-718-521.png)

如果 tx1 的二阶段全局回滚，则 tx1 需要重新获取该数据的本地锁，进行反向补偿的更新操作，实现分支的回滚。

此时，如果 tx2 仍在等待该数据的 **全局锁**，同时持有本地锁，则 tx1 的分支回滚会失败。分支的回滚会一直重试，直到 tx2 的 **全局锁** 等锁超时，放弃 **全局锁** 并回滚本地事务释放本地锁，tx1 的分支回滚最终成功。

因为整个过程 **全局锁** 在 tx1 结束前一直是被 tx1 持有的，所以不会发生 **脏写** 的问题。
# 读隔离

在数据库本地事务隔离级别 **读已提交（Read Committed）** 或以上的基础上，Seata（AT 模式）的默认全局隔离级别是 **读未提交（Read Uncommitted）** 。

如果应用在特定场景下，必需要求全局的 **读已提交** ，目前 Seata 的方式是通过 SELECT FOR UPDATE 语句的代理。

![Read Isolation: SELECT FOR UPDATE](https://img.alicdn.com/tfs/TB138wuwYj1gK0jSZFuXXcrHpXa-724-521.png)

SELECT FOR UPDATE 语句的执行会申请 **全局锁** ，如果 **全局锁** 被其他事务持有，则释放本地锁（回滚 SELECT FOR UPDATE 语句的本地执行）并重试。这个过程中，查询是被 block 住的，直到 **全局锁** 拿到，即读取的相关数据是 **已提交** 的，才返回。

出于总体性能上的考虑，Seata 目前的方案并没有对所有 SELECT 语句都进行代理，仅针对 FOR UPDATE 的 SELECT 语句。
**select ...  for update**
在MySQL数据库中，SELECT ... FOR UPDATE语句是一种**用于对选定的行进行加锁**的方式。通常情况下，当多个事务同时对同一行数据进行修改时，就可能出现数据不一致的情况。通过使用SELECT ... FOR UPDATE语句，可以在事务中对选定的行进行加锁，从而避免并发修改引起的数据不一致问题。

SELECT ... FOR UPDATE语句的**语法**如下：
```sql
SELECT ... FROM table_name WHERE condition FOR UPDATE;
```
其中，table_name是要查询的表名，condition是查询的条件。使用FOR UPDATE关键字可以将查询结果中的行进行加锁，防止其他事务修改。

需要注意的是，使用SELECT ... FOR UPDATE语句会导致**性能损失**，因为其他事务无法同时访问被加锁的行，可能会造成死锁。因此，在使用SELECT ... FOR UPDATE时，需要谨慎考虑并发访问的情况，避免对系统性能造成过大的影响。
**如何更新**
在MySQL数据库中，使用SELECT ... FOR UPDATE语句加锁后，可以通过UPDATE语句来修改被加锁的行。

UPDATE语句的语法如下：

```sql
UPDATE table_name SET column1=value1, column2=value2, ... WHERE condition;
```
其中，table_name是要更新的表名，column1、column2等是要更新的列名，value1、value2等是要更新的值，condition是更新的条件。

当使用SELECT ... FOR UPDATE语句加锁后，被加锁的行将不能被其他事务修改，因此在执行UPDATE语句时可以保证数据的一致性。

需要注意的是，使用SELECT ... FOR UPDATE语句和UPDATE语句时，要考虑事务的隔离级别和并发访问的情况，避免出现死锁等问题。另外，在使用SELECT ... FOR UPDATE语句加锁后，需要及时释放锁，以便其他事务能够访问被锁定的行，避免出现性能问题。
**如何释放锁**
在MySQL数据库中，使用SELECT ... FOR UPDATE语句加锁后，需要手动释放锁，以便其他事务能够访问被锁定的行。

释放锁可以通过以下两种方式来完成：

1.  执行COMMIT语句：在事务中执行COMMIT语句可以提交事务，并释放SELECT ... FOR UPDATE语句加的锁。这样其他事务就可以访问被锁定的行了。
    
2.  执行ROLLBACK语句：在事务中执行ROLLBACK语句可以回滚事务，并释放SELECT ... FOR UPDATE语句加的锁。这样其他事务就可以访问被锁定的行了。
    

需要注意的是，如果在事务中使用了SELECT ... FOR UPDATE语句加锁，而没有显式执行COMMIT或ROLLBACK语句来释放锁，那么该事务会一直持有锁，直到MySQL服务器自动回滚或者该事务被杀死为止。因此，在使用SELECT ... FOR UPDATE语句加锁时，需要及时考虑并发访问的情况，并在合适的时候释放锁，以便其他事务能够访问被锁定的行。


# 工作机制

以一个示例来说明整个 AT 分支的工作过程。

业务表：`product`

Field

Type

Key

id

bigint(20)

PRI

name

varchar(100)

since

varchar(100)

AT 分支事务的业务逻辑：

```sql
update product set name = 'GTS' where name = 'TXC';
```

## 一阶段

过程：

1.  解析 SQL：得到 SQL 的类型（UPDATE），表（product），条件（where name = 'TXC'）等相关的信息。
2.  查询前镜像：根据解析得到的条件信息，生成查询语句，定位数据。

```sql
select id, name, since from product where name = 'TXC';
```

得到前镜像：

id

name

since

1

TXC

2014

3.  执行业务 SQL：更新这条记录的 name 为 'GTS'。
4.  查询后镜像：根据前镜像的结果，通过 **主键** 定位数据。

```sql
select id, name, since from product where id = 1;
```

得到后镜像：

id

name

since

1

GTS

2014

5.  插入回滚日志：把前后镜像数据以及业务 SQL 相关的信息组成一条回滚日志记录，插入到 `UNDO_LOG` 表中。

```json
{
	"branchId": 641789253,
	"undoItems": [{
		"afterImage": {
			"rows": [{
				"fields": [{
					"name": "id",
					"type": 4,
					"value": 1
				}, {
					"name": "name",
					"type": 12,
					"value": "GTS"
				}, {
					"name": "since",
					"type": 12,
					"value": "2014"
				}]
			}],
			"tableName": "product"
		},
		"beforeImage": {
			"rows": [{
				"fields": [{
					"name": "id",
					"type": 4,
					"value": 1
				}, {
					"name": "name",
					"type": 12,
					"value": "TXC"
				}, {
					"name": "since",
					"type": 12,
					"value": "2014"
				}]
			}],
			"tableName": "product"
		},
		"sqlType": "UPDATE"
	}],
	"xid": "xid:xxx"
}
```

6.  提交前，向 TC 注册分支：申请 `product` 表中，主键值等于 1 的记录的 **全局锁** 。
7.  本地事务提交：业务数据的更新和前面步骤中生成的 UNDO LOG 一并提交。
8.  将本地事务提交的结果上报给 TC。

## 二阶段-回滚

1.  收到 TC 的分支回滚请求，开启一个本地事务，执行如下操作。
2.  通过 XID 和 Branch ID 查找到相应的 UNDO LOG 记录。
3.  数据校验：拿 UNDO LOG 中的后镜与当前数据进行比较，如果有不同，说明数据被当前全局事务之外的动作做了修改。这种情况，需要根据配置策略来做处理，详细的说明在另外的文档中介绍。
4.  根据 UNDO LOG 中的前镜像和业务 SQL 的相关信息生成并执行回滚的语句：

```sql
update product set name = 'TXC' where id = 1;
```

5.  提交本地事务。并把本地事务的执行结果（即分支事务回滚的结果）上报给 TC。

## 二阶段-提交

1.  收到 TC 的分支提交请求，把请求放入一个异步任务的队列中，马上返回提交成功的结果给 TC。
2.  异步任务阶段的分支提交请求将异步和批量地删除相应 UNDO LOG 记录。

# 附录

## 回滚日志表

UNDO_LOG Table：不同数据库在类型上会略有差别。

以 MySQL 为例：

Field

Type

branch_id

bigint PK

xid

varchar(100)

context

varchar(128)

rollback_info

longblob

log_status

tinyint

log_created

datetime

log_modified

datetime

```sql
-- 注意此处0.7.0+ 增加字段 context
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```