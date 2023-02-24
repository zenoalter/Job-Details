# 1. Hive 基本概念

## 1.1  什么是Hive

 	Hive：由Facebook开源用于解决<font color="red">海量结构化日志</font>的数据统计。（计算框架）

​	Hive是基于Hadoop的一个<font color="red">数据仓库工具</font>，可以将<font color="red">结构化的数据文件映射为一张表</font>，并提供<font color="red">类SQL</font>查询功能。

 	<font color=red>本质是：将HQL转化成MapReduce程序</font>

![img](file:///C:/Users/OUYANG~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

​	1）Hive处理的数据存储在HDFS

​	2）Hive分析数据底层的实现是MapReduce

​	3）执行程序运行在Yarn上

## 1.3 Hive架构原理

![img](file:///C:/Users/OUYANG~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)

## 1.4 Hive和数据库比较

### 1.4.1 查询语言

由于SQL被广泛的应用在数据仓库中，因此，专门针对Hive的特性设计了类SQL的查询语言HQL。熟悉SQL开发的开发者可以很方便的使用Hive进行开发。

### 1.4.2 数据存储位置

Hive 是建立在 Hadoop 之上的，所有 Hive 的数据都是存储在 HDFS 中的。而数据库则可以将数据保存在块设备或者本地文件系统中。

### 1.4.3 数据更新

由于Hive是针对数据仓库应用设计的，而<font color="red">数据仓库的内容是读多写少的。因此，Hive中不建议对数据的改写，所有的数据都是在加载的时候确定好的。</font>而数据库中的数据通常是需要经常进行修改的，因此可以使用 INSERT INTO …  VALUES 添加数据，使用 UPDATE … SET修改数据。

### 1.4.4 索引

Hive在加载数据的过程中不会对数据进行任何处理，甚至不会对数据进行扫描，因此也没有对数据中的某些Key建立索引。Hive要访问数据中满足条件的特定值时，需要暴力扫描整个数据，因此访问延迟较高。由于 MapReduce 的引入， Hive 可以并行访问数据，因此即使没有索引，对于[大数据](http://lib.csdn.net/base/hadoop)量的访问，Hive 仍然可以体现出优势。数据库中，通常会针对一个或者几个列建立索引，因此对于少量的特定条件的数据的访问，数据库可以有很高的效率，较低的延迟。由于数据的访问延迟较高，决定了 Hive 不适合在线数据查询。

### 1.4.5 执行

Hive中大多数查询的执行是通过 Hadoop 提供的 MapReduce 来实现的。而数据库通常有自己的执行引擎。



# 2.

load data local inpath '/stu.txt' into table stu   将本地的stu.txt放入表stu中



# 3.Hive数据类型

Hive基本数据类型：

| Hive数据类型                        | Java数据类型 | 长度                                                 | 例子                                 |
| ----------------------------------- | ------------ | ---------------------------------------------------- | ------------------------------------ |
| TINYINT                             | byte         | 1byte有符号整数                                      | 20                                   |
| SMALINT                             | short        | 2byte有符号整数                                      | 20                                   |
| <font color="red">INT   </font>     | int          | 4byte有符号整数                                      | 20                                   |
| <font color="red"> BIGINT</font>    | long         | 8byte有符号整数                                      | 20                                   |
| BOOLEAN                             | boolean      | 布尔类型，true或者false                              | TRUE  FALSE                          |
| FLOAT                               | float        | 单精度浮点数                                         | 3.14159                              |
| <font color="red">DOUBLE   </font>  | double       | 双精度浮点数                                         | 3.14159                              |
| <font color="red"> STRING   </font> | string       | 字符系列。可以指定字符集。可以使用单引号或者双引号。 | ‘now is the time’ “for all good men” |
| TIMESTAMP                           |              | 时间类型                                             |                                      |
| BINARY                              |              | 字节数组                                             |                                      |

集合数据类型：

| 数据类型 | 描述                                                         | 语法示例 |
| -------- | ------------------------------------------------------------ | -------- |
| STRUCT   | 和c语言中的struct类似，都可以通过“点”符号访问元素内容。例如，如果某个列的数据类型是STRUCT{first STRING, last STRING},那么第1个元素可以通过字段.first来引用。 | struct() |
| MAP      | MAP是一组键-值对元组集合，使用数组表示法可以访问数据。例如，如果某个列的数据类型是MAP，其中键->值对是’first’->’John’和’last’->’Doe’，那么可以通过字段名[‘last’]获取最后一个元素 | map()    |
| ARRAY    | 数组是一组具有相同类型和名称的变量的集合。这些变量称为数组的元素，每个数组元素都有一个编号，编号从零开始。例如，数组值为[‘John’,   ‘Doe’]，那么第2个元素可以通过数组名[1]进行引用。 | Array()  |

<font color="red">注意</font>：MAP，STRUCT和ARRAY里的元素间关系都可以用同一个字符表示，这里用“_”。

数组传的是角标，map传的是key

friends <font color="red">array</font><string>,

children <font color="red">map</font><string, int>,

hive (default)> select friends[1],children['xiao song'],address.city from test

where name="songsong";（语句）

# 4.DDL数据定义

### DDL语句用于对表，库进行处理

### 4.5创建表：

内部表和外部表。

​       当我们删除一个管理表（内部表）时，Hive也会删除这个表中数据。管理表不适合和其他工具共享数据

​	因为表是外部表，所以Hive并非认为其完全拥有这份数据。删除该表并不会删除掉这份数据，不过描述表的<font color="red">元数据信息</font>会被删除掉。

(元数据信息大概意思就是：对数据的一个大概总结，比如：有多少个表，多少条数据，但不是具体的数据信息)



内部表和外部表可以互相转换。



<font color="red">分区表</font>实际上就是对应一个HDFS文件系统上的独立的文件夹。

hive中的分区就是分目录。

<font color="red">分区表：同时添加多个分区，分区之间要用空格；而删除多个分区，分区之间却用逗号。</font>



desc 表名：desc +表名用来显示表的状态，包括列名（column name），各个列的类型（Type），各个列的值类型，主外键（Key），默认值，其他等。



增加和替换列

ALTER TABLE table_name <font color="red">ADD|REPLACE</font> COLUMNS (col_name data_type [COMMENT col_comment], ...) 

注：ADD是代表新增一字段，字段位置在所有列后面(partition列前)，REPLACE则是表示替换表中所有字段(replace只是修改元数据，实际数据不改变)



# 5.DML数据操作

- ### DML语句用于对表中的数据进行管理

load命令：向表中装载数据

insert into追加数据，insert overwrite覆盖数据（即先删后入）

创建表并加载数据（As Select）



# 6.查询

select from

where

join

笛卡尔积

join连接谓词中不支持or

hive (default)> select e.empno, e.ename, d.deptno from emp e join dept d on e.deptno

= d.deptno or e.ename=d.ename;   错误的



排序：全局排序（Order By）

Sort By：每个Reducer内部进行排序，对全局结果集来说不是排序。（区内排序）

分区排序：Distribute By：类似MR中partition，进行分区，结合sort by使用。

​       注意，Hive要求DISTRIBUTE BY语句要写在SORT BY语句之前。



当distribute by和sorts by字段相同时，可以使用cluster by方式。

cluster by除了具有distribute by的功能外还兼具sort by的功能。但是排序只能是升序排序，不能指定排序规则为ASC或者DESC。



<font color="red">分区针对的是数据的存储路径；分桶针对的是数据文件。</font>



查询表stu_buck中的数据。

   hive   (default)> select * from stu_buck tablesample(bucket 1 out of 4 on id);   

注：tablesample是抽样语句，语法：TABLESAMPLE(BUCKET x OUT OF y) 。

y必须是table总bucket数的倍数或者因子。hive根据y的大小，决定抽样的比例。例如，table总共分了4份，当y=2时，抽取(4/2=)2个bucket的数据，当y=8时，抽取(4/8=)1/2个bucket的数据。x表示从哪个bucket开始抽取，如果需要取多个分区，以后的分区号为当前分区号加上y。

### CASE WHEN

select 

  dept_id,

  sum(case sex when '男' then 1 else 0 end) male_count,

  sum(case sex when '女' then 1 else 0 end) female_count

<font color="red">//[ sum(if(sex='男',1,0)) male_count,</font>

<font color="red">sum(if(sex='女',1,0)) famale_count,]（这两个是等价的）</font>

from 

  emp_sex

group by

  dept_id;



### 行转列

CONCAT_WS(separator, str1, str2,...)：它是一个特殊形式的 CONCAT()。第一个参数剩余参数间的分隔符。分隔符可以是与剩余参数一样的字符串。如果分隔符是 NULL，返回值也将为 NULL。这个函数会跳过分隔符参数后的任何 NULL 和空字符串。分隔符将被加到被连接的字符串之间;

COLLECT_SET(col)：函数只接受基本数据类型，它的主要作用是将某字段的值进行去重汇总，产生array类型字段。



### 窗口函数

1．相关函数说明

OVER()：指定分析函数工作的数据窗口大小，这个数据窗口大小可能会随着行的变而变化

CURRENT ROW：当前行

n PRECEDING：往前n行数据

n FOLLOWING：往后n行数据

UNBOUNDED：起点，UNBOUNDED PRECEDING 表示从前面的起点， UNBOUNDED FOLLOWING表示到后面的终点

LAG(col,n)：往前第n行数据

LEAD(col,n)：往后第n行数据

NTILE(n)：把有序分区中的行分发到指定数据的组中，各个组有编号，编号从1开始，对于每一行，NTILE返回此行所属的组的编号。注意：n必须为int类型。



例子：数据集：

tony,2017-01-02,15

jack,2017-02-03,23

tony,2017-01-04,29

jack,2017-01-05,46

jack,2017-04-06,42

tony,2017-01-07,50

jack,2017-01-08,55

mart,2017-04-08,62

mart,2017-04-09,68

neil,2017-05-10,12

mart,2017-04-11,75

neil,2017-06-12,80

mart,2017-04-13,94

（1）查询在2017年4月份购买过的顾客及总人数

   select name,count(*) over ()    from business    where substring(orderdate,1,7) = '2017-04'    group by name;   

<font color="red">//over()（开窗函数）仅仅对其前面的count(*)使用，开窗函数是针对每一条数据的。如果上述命令去掉group by name，那他就会统计展示成：  </font>

<font color="red">jack   5</font>

<font color="red">mart  5</font>

<font color="red">mart  5</font>

<font color="red">mart  5</font>

<font color="red">mart  5</font>

<font color="red">所以可以看出over()他是针对每一条数据进行的，而加上group by name，即会变成：</font>

<font color="red">jack 2</font>

<font color="red">mart 2</font>

<font color="red">（group by就是可以理解为去重）</font>



#### <font color="green">from  ->  join on ->  where ->  group by  ->  select|having  -> order by  ->  limit</font>

##### 

LAG(col,n)：往前第n行数据

LEAD(col,n)：往后第n行数据

NTILE(n)：把有序分区中的行分发到指定数据的组中，各个组有编号，编号从1开始，对于每一行，NTILE返回此行所属的组的编号。注意：n必须为int类型。



### Rank

1．函数说明

RANK() 排序相同时会重复，总数不会变  1   1    3   4

DENSE_RANK() 排序相同时会重复，总数会减少  1    1   2   3

ROW_NUMBER() 会根据顺序计算  1   2   3   4 



# 7.函数

根据用户自定义函数类别分为以下三种：

​    （1）UDF（User-Defined-Function）

​           一进一出

​    （2）UDAF（User-Defined Aggregation Function）

​           聚集函数，多进一出

​           类似于：count/max/min

​    （3）UDTF（User-Defined Table-Generating Functions）

​           一进多出

​           如lateral view explore()



#### UDF：自定义UDF函数不能跨库

临时函数是一次性的，重新进入hive就会消失。



#### UDTF：一进多出。

例子：line："hello,world,hadoop,hive"将其输出为

hello

world

hadoop

hive

自定义一个UDTF实现将一个任意分割符的字符串切割成独立的单词。



//定义输出数据的列名

List<String> fieldNames = new ArrayList<>();

filedNames.add("word");



//定义输出数据的类型

List<ObjectInspector> fieldOIs = new ArrayList<>();

fieldOIs.add(PrimitiveObjectInspectorFactory.java<font color="red">String</font>ObjectInspector);

 //这个String决定函数输出将以String类型输出，如果输出为 1  2这种数字，则可以用int ,double亦可



# 8.压缩和存储

#### 列式存储和行式存储

Hive支持的存储数的格式主要有：TEXTFILE 、SEQUENCEFILE、ORC、PARQUET。

![行存列存](C:\Users\ouyangyuxin\Desktop\行存列存.png)

如图所示左边为逻辑表，右边第一个为行式存储，第二个为列式存储。

1．行存储的特点

查询满足条件的一整行数据的时候，列存储则需要去每个聚集的字段找到对应的每个列的值，行存储只需要找到其中一个值，其余的值都在相邻地方，所以此时行存储查询的速度更快。

2．列存储的特点

因为每个字段的数据聚集存储，在查询只需要少数几个字段的时候，能大大减少读取的数据量；每个字段的数据类型一定是相同的，列式存储可以针对性的设计更好的设计压缩算法。

TEXTFILE和SEQUENCEFILE的存储格式都是基于行存储的；

ORC和PARQUET是基于列式存储的。



orc存储文件默认采用<font color="red">ZLIB压缩。比snappy压缩的小。</font>



stored as orc tblproperties ("orc.compress"="SNAPPY");

存储方式和压缩总结:

<font color="red">在实际的项目开发当中，hive表的数据存储格式一般选择：orc或parquet。压缩方式一般选择snappy，lzo。</font>



# 9.调优

## Fetch抓取

Fetch抓取是指，<font color="red">Hive中对某些情况的查询可以不必使用MapReduce计算。</font>例如：SELECT * FROM employees;在这种情况下，Hive可以简单地读取employee对应的存储目录下的文件，然后输出查询结果到控制台。

### 本地模式

对于大多数这种情况，<font color="red">Hive可以通过本地模式在单台机器上处理所有的任务。对于小数据集，执行时间可以明显被缩短。用户可以通过设置hive.exec.mode.local.auto的值为true，来让Hive在适当的时候自动启动这个优化。</font>

### 

6.MAP JOIN

![img](https://pic1.zhimg.com/80/v2-8e984a7a8fd0f9e905c0683086597c30_720w.webp)

**MapJoin简单说就是在Map阶段将小表读入内存，顺序扫描大表完成Join。**

上图是Hive MapJoin的原理图，出自Facebook工程师Liyin Tang的一篇介绍Join优化的slice，从图中可以看出MapJoin分为两个阶段：

（1）通过MapReduce Local Task，将小表读入内存，生成HashTableFiles上传至Distributed Cache中，这里会对HashTableFiles进行压缩。

（2）MapReduce Job在Map阶段，每个Mapper从Distributed Cache读取HashTableFiles到内存中，顺序扫描大表，在Map阶段直接进行Join，将数据传递给下一个MapReduce任务。

也就是在map端进行join避免了shuffle。





