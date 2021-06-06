## clickhouse
## 概述
[clickhouse官网](https://clickhouse.tech/)  

olap列式数据库管理系统，源于Yandex公司  
核心应用场景：大批量海量数据在线实时分析（ROLAP）

性能比较：[clickhouse.tech/benchmark/dbms](https://clickhouse.tech/benchmark/dbms/)

缺点：  
1. 没有完整的事务支持
2. 稀疏索引导致clickhouse不擅长细粒度或者key-value类型数据的查询需求
3. 缺少搞了，低延迟的修改或删除已存在数据的能力，仅用于批量删除或修改数据
4. 不擅长join操作

## 设计思路剖析
思考：  
**关于OLTP和OLAP系统的核心设计思想和技术路线有哪些？**  
**OLTP与OLAP的区别？**

数据存储系统关于查询的典型操作：
1. 第一种需求OLTP：根据key找value
   ```sql
   select name, age from student where id=1;
   ```
2. 第二种需求OLAP：根据department统计平均年龄
   ```sql
   select department, avg(age) from student group by department;
   ```

如何设计一个HBase存储系统，其核心设计思想：

海量数据中，高效查询的核心思想：设计一种架构，能够快速把待搜寻的数据范围降低到原来的1/n，然后借助内存、索引等思路，实现高效查询。

如何设计专门用于OLAP的存储引擎呢？常见的方案和手段如下：

## clickhouse表引擎详解

clickhouse底层引擎：数据库引擎+表引擎