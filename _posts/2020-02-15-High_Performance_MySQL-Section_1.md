---

layout: post
title: 【读书笔记】《高性能MySQL》第一章:MySQL架构和历史
categories: Reading-notes
description: 读书笔记
keywords: 读书笔记,高性能MySQL

---

#### 1.MySQL逻辑架构

MySQL存储引擎架构：将查询处理以及其他任务系统和数据的存储处理分离开来，这样做的好处在于可以根据需要灵活选择存储引擎。


* 第一层架构 —— 连接处理、授权认证、安全等。
* 第二层架构 —— MySQL的核心服务功能：存储过程、触发器、视图、查询缓存、查询解析、查询优化、函数等等。
* 第三层架构 —— 实现了不同的存储引擎，存储引擎负责MySQL中数据的存取.

#### 2.并发控制

##### 2.1读写锁

读锁是共享的,互相不阻塞的,多个客户端在同一时刻可以同时读取同一个资源而不互相影响.
写锁是排他的,写锁会阻塞其他的写锁和读锁,这样可以保证在给定的时间里面只有一个客户端在写入.

##### 2.2锁粒度

系统不应该花费巨大的性能来管理锁,因此需要在安全性和锁开销之间寻找平衡点,两种锁策略:
* 表锁:锁定整张表.一个用户在对一张表写操作(增删改)时,获取到写锁,阻塞其他用户的所有读写操作.(开销小)
* 行级锁:简单理解为锁住写操作涉及的记录行.(最大程度的支持并发,开销高)

#### 3.事务

##### 3.1ACID
* 原子性:一个事务是一个不可分割的最小单位,整个事务的所有操作要么全部成功要么全部失败回滚.
* 一致性:数据库总是从一个一致的状态转移到另一个一致的状态,事务没有提交数据不会持久化到磁盘中.
* 隔离性:通常来说,一个事务在未提交之前对其他事务来说是不可见的.
* 持久性:一旦事务成功提交,操作完的数据就会北保存到数据库中.

##### 3.2事务的隔离级别
* read uncommitted:(未提交读)即便事务没有提交,对其他事务也是可见的,即事务可以读取到未提交的数据,脏读.
* read commit:(提交读)一个事务在提交之前,对其他事务而言是不可见的,即不可重复读.
* repeatable read:(可重复读)一个事务可以多次读取同一条记录,解决了脏读,会出现幻读.MySQL的默认隔离级别.
* serializable:(串行化)强制让事务串行化,隔离级别的最高级.

##### 3.3死锁
两个及以上的事务在同一资源上互相占用,并请求锁定对方占用的资源.死锁检测和死锁超市机制的出现在一定程度上避免了死锁的问题.

##### 3.4事务日志
使用事务日志,存储引擎只需要在修改表数据的时候修改内存拷贝,再把该修改行为记录到持久到硬盘上的事务日志中,而不用每次都把修改的数据本身持久化到硬盘上.

##### 3.5MySQL的事务
MySQL事务两种事务引擎:InnoDB和DBN Cluster.其中MySQL采用了自动提交事务的模式,

#### 4.多版本并发控制
MySQL的大多数事务性存储引擎都不是简单的行级锁,他们几乎都实现了多版本并发控制(MVCC)，MVCC是通过保存数据在某个时间点的快照来实现的.典型的有乐观并发控制和悲观并发控制。InnoDB的MVCC是通过在每个记录的后面保存两个隐藏列来实现的：一个保存行创建时间，一个保存行过期时间（删除时间），其中保存的不是实际时间，二十系统版本好，，每开始一个新的事务，版本号自动增加。事务开始时的系统版本号就作为事务版本号用来和查询到的每行记录进行比较。
以下是repeatable read隔离级别下，MVCC的操作：
> * SELECT
>       InnoDB会根据下面两个条件检查每行记录；
>        1. 只查找版本早于当前事务版本的数据行（小于等于事务的系统版本号）。确保事务读取的行，在事务开始之前就存在的，或者是事务自身插入或者修改过的。
>        2. 行的删除版本号要么未定义，要么大于当前事务版本号。确保事务查询到的行，在事务开始之前没被删除。
>        只要符合上述两个条件的记录，才能返回作为查询结果
>
> * insert操作：InnoDB为新插入的每一行保存当前系统版本号为行创建版本号。
>
> * delete操作：InnoDB为删除的行保存当前系统版本号作为行删除版本号。
>
> * update操作：InnoDB为插入一行新数据保存当前版本号作为该新行行创建版本号，同时保存当前系统版本号到原来的行作为行删除版本号。

MVCC只在REPEATABLE READ和READ COMMITTED两个隔离级别下工作。其他两个隔离级别都和MVCC不兼容，因为READ COMMITED总是读取最新的数据行，而不是符合当前事务版本的数据行。而SERIALIZABLE则会对所有读取的行都加锁。


#### 4.MySQL的存储引擎

##### 4.1InnoDB存储引擎

InnoDB是MySQL的默认存储引擎，InnoDB采用MVCC来支持高并发，并且实现了四个标准的隔离级别。其默认级别是REPEATABLE READ（可重复读），并且通过间隙锁（next-key locking）策略防止幻读的出现。间隙锁使得InnoDB不仅仅锁定查询涉及的行，还会对索引中的间隙进行锁定，以防止幻影行的插入。

##### 4.2MyISAM 存储引擎

MyISAM包括全文索引、压缩、空间函数（GIS）等，但MyISAM不支持事务和行级锁，缺陷：崩溃后无法安全恢复。如果表在创建并导入数据以后，不会再进行修改操作，那么这样的表比较适合采用MyISAM压缩表