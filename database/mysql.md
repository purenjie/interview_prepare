<!-- TOC -->

- [事务相关](#事务相关)
  - [什么是事务？](#什么是事务)
  - [事物的四大特性 (ACID) 介绍一下？](#事物的四大特性-acid-介绍一下)
  - [并发事务带来哪些问题？](#并发事务带来哪些问题)
  - [事务隔离级别有哪些？MySQL 的默认隔离级别是？](#事务隔离级别有哪些mysql-的默认隔离级别是)
- [其他](#其他)
  - [主键和外键](#主键和外键)
- [索引相关](#索引相关)
  - [什么是索引？](#什么是索引)
  - [为什么要使用索引？](#为什么要使用索引)
  - [为什么索引能提高查询速度？](#为什么索引能提高查询速度)
  - [索引数据结构](#索引数据结构)
    - [哈希索引](#哈希索引)
  - [为什么不对表中每一列都创建一个索引？](#为什么不对表中每一列都创建一个索引)
  - [索引如何提高查询速度？](#索引如何提高查询速度)
  - [使用索引的注意事项](#使用索引的注意事项)
  - [什么是最左前缀原则？](#什么是最左前缀原则)
  - [Mysql 如何为表字段添加索引？](#mysql-如何为表字段添加索引)
  - [MyISAM 与 InnoDB 的区别？](#myisam-与-innodb-的区别)
  - [乐观锁与悲观锁的区别](#乐观锁与悲观锁的区别)
  - [乐观锁常见的两种实现方式](#乐观锁常见的两种实现方式)
  - [乐观锁的缺点](#乐观锁的缺点)
  - [锁机制与 InnoDB 锁算法](#锁机制与-innodb-锁算法)
  - [大表优化](#大表优化)
  - [一条 SQL 语句在 MySQL 中如何执行的](#一条-sql-语句在-mysql-中如何执行的)

<!-- /TOC -->

MySQL 的默认端口号是 **3306**

## 事务相关

### 什么是事务？

事务是逻辑上的一组操作，要么都执行，要么都不执行。（e.g. 转账操作）

### 事物的四大特性 (ACID) 介绍一下？

- 原子性（Atomicity）：要么都执行，要么都不执行

- 一致性（Consistency）

一致性指的是**事务中包含的处理要满足数据库提前设置的约束**，如主键约束或者 NOT NULL 约束等。（转账 100 账户只有 90，不满足约束无法完成事务）

- 隔离性（Isolation）

隔离性指的是保证**不同事务之间互不干扰**的特性。即使某个事务向表中添加了记录，在没有提交之前，其他事务也是看不到新添加的记录的。

- 持久性（Durability）

持久性也可以称为耐久性，指的是在事务（不论是提交还是回滚）结束后， DBMS 能够保证该时间点的数据状态会被保存的特性。即使由于系统故障导致数据丢失，数据库也一定能通过某种手段进行恢复。

### 并发事务带来哪些问题？

- 脏读（Dirty read）：一个事务对数据进行修改但是没有提交，另一个事务读取该未提交的数据，这个数据就是“脏数据”，这就是脏读

  丢失修改（Lost to modify）：两个事务对同一数据进行修改，数据值为后一个事务的值，前一个事务的值丢失

- 不可重复读（Unrepeatableread）：在一个事务内多次读同一数据。读到的结果不一样。（其实原因和脏读相同，都是两个事务的执行顺序导致的）

- 幻读（Phantom read）：一个事务能够读到之前不存在的数据（另一个事务插入的）

**不可重复度和幻读区别：** 不可重复读的重点是修改，幻读的重点在于插入或者删除。

### 事务隔离级别有哪些？MySQL 的默认隔离级别是？

|     隔离级别     | 脏读 | 不可重复读 | 幻影读 |
| :--------------: | :--: | :--------: | :----: |
| READ-UNCOMMITTED |  √   |     √      |   √    |
|  READ-COMMITTED  |  ×   |     √      |   √    |
| REPEATABLE-READ  |  ×   |     ×      |   √    |
|   SERIALIZABLE   |  ×   |     ×      |   ×    |

MySQL InnoDB 存储引擎的默认支持的隔离级别是 **REPEATABLE-READ（可重读）**

## 其他

### 主键和外键

关系型数据库中一条记录中有若干个属性，若其中某一个属性组（注意是组）能唯一标识一条记录，该属性就可以成为一个主键。

本表的外键会是其他表的主键。

## 索引相关

### 什么是索引？

索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。

### 为什么要使用索引？

- 可以大大加快数据的检索速度

- 通过创建唯一性索引，可以保证数据库中每一行数据的唯一性

### 为什么索引能提高查询速度？

- 没有索引时的查询路径

（MySQL）首先遍历双向链表，定位到记录所在的页，然后遍历该页中的链表找到对应的值。

- 有索引时的查询路径（B+ 索引）

索引就相当于目录，也就是 B+ 树的搜索来定位元素，可以快速定位所在的页。通过使用 B+ 树时间复杂度从 O(n) 到了 O(logn)

### 索引数据结构
#### 哈希索引



### 为什么不对表中每一列都创建一个索引？

- 索引需要占用物理空间
- 创建索引和维护索引会耗费时间
- 对表中数据进行增删改时，索引也需要动态维护

### 索引如何提高查询速度？

将无序的数据变成相对有序的数据，就像查书的目录一样

### 使用索引的注意事项

- 对于经常需要搜索的列使用索引，可以加快搜索速度
- 在经常使用 WHERE 字句的列使用索引，加快条件判断速度
- 在经常需要排序的列上使用索引，加快排序查询时间
- 经常与其他表进行连接的表，在连接字段上应该建立索引
- 避免 where 字句中对字段施加函数，会造成无法命中索引
- 使用 InnoDB 时使用与业务无关的自增主键作为主键，而不要使用业务主键
- 将打算加索引的列设置为 NOT NULL，否则将导致引擎进行全表扫描而不是使用索引
- 在使用 limit offset 查询缓慢时，使用索引提高性能

### 什么是最左前缀原则？

如果查询的时候查询条件**精确匹配索引的左边连续一列或几列**，则此列就可以被用到

### Mysql 如何为表字段添加索引？

```mysql
ALTER TABLE `table_name` ADD PRIMARY KEY ( `column` )  # 主键索引
ALTER TABLE `table_name` ADD UNIQUE ( `column` ) # 唯一索引
ALTER TABLE `table_name` ADD INDEX index_name ( `column` ) # 普通索引
ALTER TABLE `table_name` ADD FULLTEXT ( `column`) # 全文索引
ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3` ) # 多列索引
```

### MyISAM 与 InnoDB 的区别？

- InnoDB 支持事务，MyISAM 不支持事务
- InnoDB 支持外键，而 MyISAM 不支持
- InnoDB 最小的锁粒度是行级锁，MyISAM 最小的锁粒度是表级锁（一个更新语句会锁住整张表，导致其他查询和更新都会被阻塞）
- InnoDB 不保存表的具体行数，MyISAM 用一个变量保存了整个表的行数
- InnoDB 是聚集索引，MyISAM 是非聚集索引

### 乐观锁与悲观锁的区别

- 悲观锁

总是假设最坏情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁。共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程。

传统关系型数据库：行锁，表锁等，读锁，写锁等

Java：synchronized 和 ReentrantLock

- 乐观锁

总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁。在更新的时候会判断一下在此期间别人**有没有去更新这个数据**，可以使用 `版本号机制` 和 `CAS 算法` 实现。

乐观锁适用于读操作频繁的场景，这样可以提高吞吐量

数据库：write_condition机制

Java：Atomic 包下的原子变量类使用 CAS 实现

### 乐观锁常见的两种实现方式

1. 版本号机制

一般是在数据表中加上一个数据版本号 version 字段，表示数据被修改的次数，当数据被修改时，version 值会加一。

2. CAS 算法

当且仅当 V 的值等于 A 时，CAS 通过原子方式用新值 B 来更新 V 的值，否则不会执行任何操作（比较和替换是一个原子操作）。

### 乐观锁的缺点

- ABA 问题

一个变量 V 初次读取的时候是 A 值，准备赋值的时候检查到它仍然是 A 值，但是这段时间它的值可能被改为其他值。

JDK 1.5 以后的 `AtomicStampedReference 类`就提供了此种能力，其中的 `compareAndSet 方法`就是首先检查当前引用是否等于**预期引用**，并且当前标志是否等于**预期标志**，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值。

- 循环时间长开销大

自旋 CAS（也就是不成功就一直循环执行直到成功）如果长时间不成功，会给 CPU 带来非常大的执行开销。

- 只能保证一个共享变量的原子操作

CAS 只对单个共享变量有效，当操作涉及跨多个共享变量时 CAS 无效。我们可以使用锁或者利用 `AtomicReference类`把多个共享变量合并成一个共享变量来操作。

### 锁机制与 InnoDB 锁算法

**MyISAM 和 InnoDB 存储引擎使用的锁：**

- MyISAM 采用表级锁 (table-level locking)。
- InnoDB 支持行级锁 (row-level locking) 和表级锁，默认为行级锁

**表级锁和行级锁对比：**

- 表级锁：粒度大，对整张表加锁，实现简单，加锁快，不会出现死锁。并发效率低。
- 行级锁：粒度小，对当前操作行加锁，加锁慢，会出现死锁。并发效率高。

**InnoDB 存储引擎的锁的算法有三种：**

- Record lock：单个行记录上的锁
- Gap lock：间隙锁，锁定一个范围，不包括记录本身
- Next-key lock：record + gap 锁定一个范围，包含记录本身

### 大表优化

当 MySQL 单表记录数过大时，数据库的 CRUD 性能会明显下降，一些常见的优化措施如下

- 限定数据的范围
- 读写分离
- 垂直分区（列的拆分）
- 水平分区（行的拆分）

### 一条 SQL 语句在 MySQL 中如何执行的

- 查询语句

权限校验 ---》查询缓存 ---》分析器 ---》优化器 ---》权限校验 ---》执行器 ---》引擎

- 更新语句

分析器 ----》权限校验 ----》执行器 ---》引擎 ---redo log prepare---》binlog---》redo log commit

## 日志

[必须了解的 mysql 三大日志 - binlog、redo log 和 undo log](https://segmentfault.com/a/1190000023827696)

[浅析 MySQL 事务中的 redo 与 undo](https://segmentfault.com/a/1190000017888478)