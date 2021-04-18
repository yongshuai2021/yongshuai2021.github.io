# SQL简明教程
## 环境准备
1. 搭建mysql并启动
2. 新建student与class表
```sql
create table student (id bigint not null primary key auto_increment, sname varchar(20) not null, class_id int not null, gender boolean not null, age int not null) auto_increment=1;

create table class (class_id int not null primary key auto_increment, cname varchar(20) not null, monitor varchar(20) not null) auto_increment=1;
```

## 理论知识
- 主键  

在关系数据库中，一张表中的每一行数据被称为**记录**，每一列被称为**字段**。一条记录通常由多个字段组成。  
对于关系表，能够通过某个字段唯一确定一条记录，这个字段被称为**主键**。

- 外键  

**外键**用来建立主表与从表的关联关系，为两个表的数据建立连接，约束两个表中数据的一致性和完整性。  
通过以下语句添加/删除外键：
```sql
alter table student add constraint fk_class_id foreign key (class_id) references class (class_id);

alter table student drop foreign key fk_class_id;
```

- 索引  

**索引**是关系数据库中对某一列或多个列的值进行预排序的数据结构。通过使用索引，可以让数据库系统不必扫描整个表，而是直接定位到符合条件的记录，这样就大大加快了查询速度。  

索引的优点如下：

    通过创建唯一索引可以保证数据库表中每一行数据的唯一性。
    可以给所有的 MySQL列类型设置索引。
    可以大大加快数据的查询速度，这是使用索引最主要的原因。
    在实现数据的参考完整性方面可以加速表与表之间的连接。
    在使用分组和排序子句进行数据查询时也可以显著减少查询中分组和排序的时间。

索引的缺点如下：

    创建和维护索引组要耗费时间，并且随着数据量的增加所耗费的时间也会增加。
    索引需要占磁盘空间，除了数据表占数据空间以外，每一个索引还要占一定的物理空间。如果有大量的索引，索引文件可能比数据文件更快达到最大文件尺寸。
    当对表中的数据进行增加、删除和修改的时候，索引也要动态维护，这样就降低了数据的维护速度。
 
通过以下语句添加唯一索引：
```sql
alter table student add unique index uni_name (name);
```

- 表分区  

- 事务  

**事务**是一种机制、一个操作序列，包含了一组数据库操作命令。事务将一组命令作为一个整体执行，即这一组数据库命令要么都执行，要么都不执行，因此事务是一个不可分割的工作逻辑单元。  
事务的ACID特性：   
1. 原子性Atomic  

事务的各元素是不可分的（原子的）。事务中的所有元素必须作为一个整体提交或回滚。

2. 一致性Consistent  

事务开始前后，数据库中存储的数据都处于一致状态。  

3. 隔离性Isolation

对数据进行修改的所有并发事务是彼此隔离的，这表明事务必须是独立的，它不应以任何方式依赖于或影响其他事务。

4. 持久性Duration  

一旦事务被提交，事务对数据所做的任何变动都会被永久地保留在数据库中。  

事务的隔离级别：  
隔离级别|脏读|不可重复读|幻读  
--|--|:--:|--:
Read Uncommitted|	Yes	|Yes	|Yes
Read Committed  |	-	|Yes	|Yes
Repeatable Read	|-	    |-	    |Yes
Serializable	|-	    |-	    |-

- 锁  
