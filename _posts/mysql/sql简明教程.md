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

1. 分区表分类

RANGE分区：基于属于一个给定连续区间的列值，把多行分配给分区。  
LIST分区：类似于按RANGE分区，区别在于LIST分区是基于列值匹配一个离散值集合中的某个值来进行选择。  
HASH分区：基于用户定义的表达式的返回值来选择分区，该表达式使用将要插入到表中的这些行的列值进行计算。这个函数可以包含MySQL中有效的、产生非负整数值的任何表达式。  
KEY分区：类似于按HASH分区，区别在于KEY分区只支持计算一列或多列，且MySQL服务器提供其自身的哈希函数。必须有一列或多列包含整数值。  
复合分区：在MySQL 5.6版本中，只支持RANGE和LIST的子分区，且子分区的类型只能为HASH和KEY。

2. 分区表限制

- 分区键必须包含在表的所有主键、唯一键中。
- MYSQL只能在使用分区函数的列本身进行比较时才能过滤分区，而不能根据表达式的值去过滤分区，即使这个表达式就是分区函数也不行。
- 最大分区数： 不使用NDB存储引擎的给定表的最大可能分区数为8192（包括子分区）。如果当分区数很大，但是未达到8192时提示 Got error … from storage engine: Out of resources when opening file,可以通过增加open_files_limit系统变量的值来解决问题，当然同时打开文件的数量也可能由操作系统限制。
- 不支持查询缓存：分区表不支持查询缓存，对于涉及分区表的查询，它自动禁用。查询缓存无法启用此类查询。
- 分区的innodb表不支持外键。
- 服务器SQL_mode影响分区表的同步复制。 主机和从机上的不同SQL_mode可能会导致sql语句; 这可能导致分区之间的数据分配给定主从位置不同，甚至可能导致插入主机上成功的分区表在从库上失败。 为了获得最佳效果，您应该始终在主机和从机上使用相同的服务器SQL模式。
- ALTER TABLE … ORDER BY：对分区表运行的ALTER TABLE … ORDER BY列语句只会导致每个分区中的行排序。
- 全文索引。分区表不支持全文索引，即使是使用InnoDB或MyISAM存储引擎的分区表。
- 分区表无法使用外键约束。
- Spatial columns：具有空间数据类型（如POINT或GEOMETRY）的列不能在分区表中使用。
- 临时表：临时表不能分区。
- subpartition问题：subpartition必须使用HASH或KEY分区。只有RANGE和LIST分区可能被分区；HASH和KEY分区不能被子分区。
- 分区表不支持mysqlcheck，myisamchk和myisampack。



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

## SQL应用   

1. 表设计
2. 窗口函数  
   MySQL8.0开始支持
3. 分组聚合函数应用  
   聚合查询的列中，只能放入分组的列。
   max min avg sum count
   ```sql
   select count(*) students from student;
   select count(*) boys from student where gender=true;
   select class_id, count(*) num from student group by class_id;
   select class_id, gender, COUNT(*) num from student group by class_id, gender;
   select class_id, gender, COUNT(*) num from student group by class_id, gender having class_id>1;
   ```
4. 游标
   
5. 更新游标
   
6. SQL优化手段及应用
   
7. 连接  
   内连接：只返回同时存在于两张表的行数据
   ```sql
   select * from student s inner join class c on s.class_id=c.class_id order by age desc;
   ```
   RIGHT OUTER JOIN：返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以NULL填充剩下的字段。
   ```sql
   select * from student s right join class c on s.class_id=c.class_id order by age desc;
   ```
   LEFT OUTER JOIN：返回左表都存在的行。
   ```sql
   select * from student s left join class c on s.class_id=c.class_id order by age desc;
   ```
   FULL OUTER JOIN：选出左右表都存在的记录。
   ```sql
   select * from student s full join class c on s.class_id=c.class_id order by age desc;
   ```

8. Null值的处理和归纳能力
   
9.  SQL递归查询
    
    
10. 多表关联
    
11. 自关联
    在查询时对自身连接。
    ```sql
    select a.sname from student as a, student as b where a.class_id=1 and a.age < b.age;
    ```
12. 排序
    ```sql
    select * from student order by age;
    select * from student order by age asc;
    select * from student order by age desc;
    select * from student order by age desc, class_id;
    ```
13. 模糊匹配
    
14. CASE语句应用
15. 关联子查询
16. Update的使用
17. 查询