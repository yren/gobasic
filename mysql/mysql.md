## DML, DDL
DML是Data Manipulation Language的缩写
DDL是Data definition Language 的缩写

## 全局锁，表锁，行锁

* MySQL 提供了一个加全局读锁的方法， Flush tables with read lock (FTWRL)
让整个库处于只读状态，之后其他线程的以下语句会被阻塞：数据更新语句（数据的增删改）、数据定义语句（包括建表、修改表结构等）和更新类事务的提交语句

全局锁的典型使用场景是，做全库逻辑备份。也就是把整库每个表都 select 出来存成文本。

不用全局锁，在可重复读隔离级别下开启一个事务。官方自带的逻辑备份工具是 mysqldump。当 mysqldump 使用参数–single-transaction 的时候，导数据之前就会启动一个事务，来确保拿到一致性视图。

一致性读是好，但前提是引擎要支持这个隔离级别。

既然要全库只读，为什么不使用 set global readonly=true 的方式呢

1. 在有些系统中，readonly 的值会被用来做其他逻辑，比如用来判断一个库是主库还是备库
2. 二是，在异常处理机制上有差异。如果执行 FTWRL 命令之后由于客户端发生异常断开，那么 MySQL 会自动释放这个全局锁

## 表级锁

* MySQL 里面表级别的锁有两种：一种是表锁，一种是元数据锁（meta data lock，MDL)。

lock tables t1 read, t2 write;

* 另一类表级的锁是 MDL（metadata lock) 
metadata read lock, metadata write lock.

## 行锁
* innoDB 存储引擎支持的行锁

## mysql 的架构
插件式的存储引擎架构将查询处理和其它的系统任务以及数据的存储提取相分离
                                         
客户端 - (链接器 - 分析器 - 优化器 - 执行器） - 存储引擎

## 存储引擎 
不同的存储引擎提供不同的存储机制、索引技巧、锁定水平等功能，使用不同的存储引擎，还可以获得特定的功能

一个数据库中多个表可以使用不同引擎以满足各种性能和实际需求，使用合适的存储引擎，将会提高整个数据库的性能

```
-- 查看支持的存储引擎
SHOW ENGINES

-- 查看默认存储引擎
SHOW VARIABLES LIKE 'storage_engine'

--查看具体某一个表所使用的存储引擎，这个默认存储引擎被修改了！
show create table tablename

--准确查看某个数据库中的某一表所使用的存储引擎
show table status like 'tablename'
show table status from database where name="tablename"

-- 建表时指定存储引擎。默认的就是INNODB，不需要设置
CREATE TABLE t1 (i INT) ENGINE = INNODB;
CREATE TABLE t2 (i INT) ENGINE = CSV;
CREATE TABLE t3 (i INT) ENGINE = MEMORY;

-- 修改存储引擎
ALTER TABLE t ENGINE = InnoDB;

-- 修改默认存储引擎，也可以在配置文件my.cnf中修改默认引擎
SET default_storage_engine=NDBCLUSTER;
```

常见的存储引擎就 InnoDB、MyISAM、Memory、NDB。
InnoDB 现在是 MySQL 默认的存储引擎，支持事务、行级锁定和外键

在 MySQL中建立任何一张数据表，在其数据目录对应的数据库目录下都有对应表的 .frm 文件，.frm 文件是用来保存每个数据表的元数据(meta)信息，包括表结构的定义等，与数据库存储引擎无关，也就是任何存储引擎的数据表都必须有.frm文件，命名方式为 数据表名.frm，如user.frm。

InnoDB 支持事务，MyISAM 不支持事务。这是 MySQL 将默认存储引擎从 MyISAM 变成 InnoDB 的重要原因之一；

InnoDB 必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大

InnoDB 不保存表的具体行数，执行select count(*) from table 时需要全表扫描。而 MyISAM 用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快；

InnoDB 最小的锁粒度是行锁，MyISAM 最小的锁粒度是表锁。一个更新语句会锁住整张表，导致其他查询和更新都会被阻塞，因此并发访问受限。这也是 MySQL 将默认存储引擎从 MyISAM 变成 InnoDB 的重要原因之一；

这跟 InnoDB 的事务特性有关，由于多版本并发控制（MVCC）的原因，InnoDB 表“应该返回多少行”也是不确定的。

## 数据类型

整数类型：BIT、BOOL、TINY INT、SMALL INT、MEDIUM INT、 INT、 BIG INT
浮点数类型：FLOAT、DOUBLE、DECIMAL
字符串类型：CHAR、VARCHAR、TINY TEXT、TEXT、MEDIUM TEXT、LONGTEXT、TINY BLOB、BLOB、MEDIUM BLOB、LONG BLOB
日期类型：Date、DateTime、TimeStamp、Time、Year
其他数据类型：BINARY、VARBINARY、ENUM、SET、Geometry、Point、MultiPoint、LineString、MultiLineString、Polygon、GeometryCollection等

char是固定长度，varchar长度可变

char(n)，varchar(n)中的n都代表字符的个数
超过char，varchar最大长度n的限制后，字符串会被截断。

能存储的最大空间限制不一样：char的存储上限为255字节。

char是适合存储很短的、一般固定长度的字符串。例如，char非常适合存储密码的MD5值，因为这是一个定长的值。对于非常短的列，char比varchar在存储空间上也更有效率。

BLOB是一个二进制对象，可以容纳可变数量的数据。有四种类型的BLOB：TINYBLOB、BLOB、MEDIUMBLO和 LONGBLOB

TEXT是一个不区分大小写的BLOB。四种TEXT类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。

BLOB 保存二进制数据，TEXT 保存字符数据。

## 索引

索引（Index）是帮助MySQL高效获取数据的数据结构

索引本身也很大，不可能全部存储在内存中，一般以索引文件的形式存储在磁盘上

就是B+树（多路搜索树，不一定是二叉树）结构组织的索引

```
CREATE [UNIQUE] INDEX indexName ON mytable(username(length));
ALTER table tableName ADD [UNIQUE] INDEX indexName(columnName)
DROP INDEX [indexName] ON mytable;
SHOW INDEX FROM table_name\G --可以通过添加 \G 来格式化输出信息。

ALTER TABLE tbl_name ADD PRIMARY KEY (column_list): 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。

ALTER TABLE tbl_name ADD UNIQUE index_name (column_list 这条语句创建索引的值必须是唯一的（除了NULL外，NULL可能会出现多次）。

ALTER TABLE tbl_name ADD INDEX index_name (column_list) 添加普通索引，索引值可出现多次。

ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list)该语句指定了索引为 FULLTEXT ，用于全文索引。

```

提高数据检索效率，降低数据库IO成本
降低数据排序的成本，降低CPU的消耗

索引也是一张表，保存了主键和索引字段，并指向实体表的记录，所以也需要占用内存
虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT、UPDATE和DELETE。因为更新表时，MySQL不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段， 都会调整因为更新所带来的键值变化后的索引信息

index 分类

B+树索引
Hash索引
Full-Text全文索引
R-Tree索引

从逻辑角度

主键索引：主键索引是一种特殊的唯一索引，不允许有空值
普通索引或者单列索引：每个索引只包含单个列，一个表可以有多个单列索引
多列索引（复合索引、联合索引）：复合索引指多个字段上创建的索引，只有在查询条件中使用了创建索引时的第一个字段，索引才会被使用。使用复合索引时遵循最左前缀集合
唯一索引或者非唯一索引

B+ 树

非叶子节点只存储键值信息；
所有叶子节点之间都有一个链指针；
数据记录都存放在叶子节点中

哪些情况需要创建索引

主键自动建立唯一索引
频繁作为查询条件的字段
查询中与其他表关联的字段，外键关系建立索引
单键/组合索引的选择问题，高并发下倾向创建组合索引
查询中排序的字段，排序字段通过索引访问大幅提高排序速度
查询中统计或分组字段

哪些情况不要创建索引

表记录太少
经常增删改的表
频繁更新的字段不适合创建索引
where条件里用不到的字段不创建索引

count(*)包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL
count(1)包括了所有列，用1代表代码行，在统计结果的时候，不会忽略列值为NULL
count(列名)只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者0，而是表示null）的计数，即某个字段值为NULL时，不统计。

如果查询的两个表大小相当，那么用in和exists差别不大。
如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in：


```
select column from tA left join tB on tA.key = tB.key
select colomn from tA left join tB on tA.ley = tB.key and tb.key is null;

select column from tA inner join tB on tA.key = tB.key;
select column from tA full outer join tB on tA.key = tB.key;

```

## 事务
ACID
A (Atomicity) 原子性: 整个事务中的所有操作，要么全部完成，要么全部不完成，不可能停滞在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样

C (Consistency) 一致性：在事务开始之前和事务结束以后，数据库的完整性约束没有被破坏

I (Isolation)隔离性：一个事务的执行不能其它事务干扰。即一个事务内部的操作及使用的数据对其它并发事务是隔离的，并发执行的各个事务之间不能互相干扰

D (Durability) 持久性：在事务完成以后，该事务所对数据库所作的更改便持久的保存在数据库之中，并不会被回滚



一种是加锁：在读取数据前，对其加锁，阻止其他事务对数据进行修改。
另一种是数据多版本并发控制（MultiVersion Concurrency Control，简称 MVCC 或 MCC），也称为多版本数据库：不用加任何锁， 通过一定机制生成一个数据请求时间点的一致性数据快照 （Snapshot)， 并用这个快照来提供一定级别 （语句级或事务级） 的一致性读取。从用户的角度来看，好象是数据库可以提供同一数据的多个版本。


## 事务隔离级别

READ-UNCOMMITTED(读未提交)： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。

READ-COMMITTED(读已提交)： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。

REPEATABLE-READ(可重复读)： 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。

SERIALIZABLE(可串行化)： 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

```
show variables like 'tx_isolation'
```

