# sql优化
## sql 语句优化
### 优化步骤一 开启慢查询日志
通过慢查询日志定位sql

1.索引 2.语法问题 3.技巧提升查询效率

### 优化步骤二 使用Explain分析sql语句
sql优化主要解决的问题：
	就是查询大量数据时的效率为问题
使用工具：
	EXPLAIN 在执行语句前加上
	需要关注的列
	-   type列，连接类型。一个好的sql语句至少要达到range级别。杜绝出现all级别
	-   key列，使用到的索引名。如果没有选择索引，值是NULL。可以采取强制索引方式
	-   key_len列，索引长度
	-   rows列，扫描行数。该值是个预估值
	-   extra列，详细说明。注意常见的不太友好的值有：Using filesort, Using temporary

### 优化步骤三 创建合理的索引

优化的方向有：
1.建立索引
	索引的分类:
		1. 一般索引
			1. 最普通的索引
			2. 创建销毁语句
				1. create index normal_index on cxuan003(id);
				2. drop index normal_index on cxuan003;
		2. 唯一索引
			1. 唯一索引列的值必须唯一，允许有空值，如果是组合索引，则列值的组合必须唯
			2. 创建 create unique index normal_index on cxuan003(id);
		3. 主键索引
			1. 是一种特殊的索引，一个表只能有一个主键，不允许有空值。一般是在建表的时候同时创建主键索引。
			2. 创建
				1. CREATE TABLE `table` (
				  id int(11) NOT NULL AUTO_INCREMENT , 
				  title char(255) NOT NULL ,
				  PRIMARY KEY (`id`) )
		4. 联合索引
			1. 指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用组合索引时遵循最左前缀原则
			2. 
		5. 联合索引
			1. 主要用来查找文本中的关键字，而不是直接与索引中的值相比较，目前只有 char、varchar，text 列上可以创建全文索引，创建表的适合添加全文索引
			2. 创建
			CREATE TABLE `table` ( 
				`id` int(11) NOT NULL AUTO_INCREMENT , 
				`title` char(255) CHARACTER NOT NULL ,
				 `content` text CHARACTER NULL , 
				 `time` int(10) NULL DEFAULT NULL ,
				  PRIMARY KEY (`id`), 
				  FULLTEXT (content) );
				或者 
				CREATE FULLTEXT INDEX index_content ON article(content)

2.避免低效率的查询语句
	1. SQL语句中IN包含的值不应过多



