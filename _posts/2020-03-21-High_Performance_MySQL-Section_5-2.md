---


layout: post

title: 【读书笔记】《高性能MySQL》第五章:创建高性能索引(2)

categories: Reading-notes

description: 读书笔记

keywords: [读书笔记,高性能MySQL]


---



索引是存储引擎用于快速找到记录的一种数据结构,这也是索引的基本功能.在MySQL中也叫"键key".良好的性能少不了索引.换句话说,索引优化能够将查询性能轻松提高几个数量级.

接上篇:[创建高性能索引(1)]({{ site.url }}/High_Performance_MySQL-Section_5.html)

#####  3.5. 聚簇索引

聚簇索引不是单独的索引类型,是一种存储方式.InnoDB的聚簇索引事实上是在一个结构中保存了B-Tree索引和数据行,具体依赖于存储引擎的实现.

聚簇表示数据行和相邻的键值紧密的保存在一起;无法把数据同时放在两个地方所以一个表只能有一个聚簇索引.

InnoDB通过主键聚集数据.如果没有定义主键,	InnoDB会选择一个唯一的非空索引替代.

优点:

* 把相关数据保存在一起
* 数据访问更快(数据和索引在一个B-Tree中)
* 使用覆盖索引扫描的查询可以直接使用页节点的主键值

缺点:

* 如果可以把数据都缓存到内存中,聚簇索引便没有了优势
* 插入速度严重依赖插入顺序
* 更新聚簇索引列的代价很高
* 聚簇索引在插入新行或者主键要求被更新移动的时候可能导致页分裂的问题
* 聚簇索引可能导致表扫描变慢
* 二级索引占用空间比较大
* 二级索引查找需要两次索引查找

聚簇索引每一个叶子节点都包含了主键值,事务ID,用于事务和MVCC的回滚指针,以及所有的剩余列.

使用InnoDB时没有什么数据需要聚集可以定义一个代理主键,比如自增主键,可以保证按顺序插入,利于用主键做关联操作.注意避免使用随机的聚簇索引,太导致插入变得随机,导致性下降.

##### 3.6 覆盖索引

索引包含了要查询的数据就称为覆盖索引:

* 索引条目数远小于数据行大小,扫描索引的速度将会远远高于扫描表
* 索引按顺序存储存储,读取速度将会很快
* InnoDB二级索引保存了主键列,如果二级索引覆盖查询那么可以避免对主键的二次查询
* 哈希索引,空间索引,全文索引都不存储索引列的值,只有B-Tree索引可以做覆盖索引

##### 3.7 使用索引扫描做排序

生成有序结果的方式:排序操作;按索引顺序扫描;

按索引顺序读取数据要比顺序扫描全表的速度慢,按索引顺序几乎是随机I/O

设计索引时应该考虑尽可能满足排序和查询的需求

##### 3.8 冗余和重复索引

MySQL允许在相同列上创建多个索引,不过这需要单独维护重复的索引,并且优化器查询时也需要逐个考虑,这回严重影响性能.

MySQL的唯一限制和主键限制都是通过索引来实现的.

创建了索引(A,B)再创建索引(A)就是冗余索引,因为(A)是(A,B)的前缀索引

创建了索引(A,B)再创建索引(B,A)便不是冗余索引,(B)也不是冗余索引

一般情况下都不需要冗余索引,推荐扩展已有的索引.同时也有出于性能的原因速妖考虑冗余索引

删除重复或者冗余索引即可解决冗余和重复的问题,推荐使用外部工具来排查

##### 3.9索引和锁

即使InnoDB的行锁效率很高,当锁定是仍然会带来额外开销;当InnoDB在存储引擎曾能够过滤掉不需要的行时,可以减少InnoDB访问的行,从而减少加锁的行.

InnoDB在二级索引上使用共享锁(读锁),但访问主键索引时需要排他锁(写锁)

#### 总结

三个原则

* 单行访问很慢.近乎随机IO,最好在读取时包含尽可能多的行,这时候可以创建位置索引提高速度
* 按顺序访问数据时很快的.顺序IO不需要多次扫描;按顺序时不需要额外排序操作
* 索引覆盖查询非常快.减少了回表访问,直接在索引中获取所有数据

