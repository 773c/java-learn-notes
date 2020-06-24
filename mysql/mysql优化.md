# MySQL优化

## 优化操作

**优化口诀**

> 全值匹配我最爱，最左前缀要遵守；
>
> 带头大哥不能死，中间兄弟不能断；
>
> 索引列上少计算，范围之后全失效；
>
> LIKE百分写最右，覆盖索引不写星；
>
> 不等空值还有or，索引失效要少用；
>
> 字符串引号不可丢，SQL高级也不难；

### 1. mysql的逻辑架构

#### （1）连接层

#### （2）服务层

#### （3）引擎层

#### （4）存储层

### 2. MyISAM和InnoDB的对比

| 对比项   | MyISAM                                           | InnoDB                                                       |
| -------- | ------------------------------------------------ | ------------------------------------------------------------ |
| 主外键   | 不支持                                           | 支持                                                         |
| 事务     | 不支持                                           | 支持                                                         |
| 行表锁   | 表锁，操作一条数据也会将整个表锁住，不适合高并发 | 行锁，操作时只锁某一行，适合高并发                           |
| 缓存     | 只缓存索引，不缓存真实数据                       | 不仅缓存索引还缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 表空间   | 小                                               | 大                                                           |
| 关注点   | 性能                                             | 事务                                                         |
| 默认安装 | Y                                                | Y                                                            |

### 3. join七种理论

#### （1）内连接

![image-20200605163748176](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200605163748176.png)

#### SQL：

````sql
SELECT *
FROM TABLE A
INNER JOIN TABLE B
ON A.Key = B.Key
````

#### （2）左连接

![image-20200605163431909](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200605163431909.png)

#### SQL：

````sql
SELECT *
FROM TABLE A
LEFT JOIN TABLE B
ON A.Key = B.Key
````

#### （3）右连接

![image-20200605163701998](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200605163701998.png)

#### SQL：

````sql
SELECT *
FROM TABLE A
RIGHT JOIN TABLE B
ON A.Key = B.Key
````

#### （4）左连接右为NULL

![image-20200605165217358](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200605165217358.png)

#### SQL：

````sql
SELECT *
FROM TABLE A
LEFT JOIN TABLE B
ON A.Key = B.Key
WHERE B.Key IS NULL
````

#### （5）右连接左为NULL

![image-20200605165329875](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200605165329875.png)

#### SQL：

````sql
SELECT *
FROM TABLE A
LEFT JOIN TABLE B
ON A.Key = B.Key
WHERE B.Key IS NULL
````

## 索引

### 1. 索引分类

#### 1.1 单值索引

> 即一个索引只包含单个列，一个表可以有多个单列索引

#### 1.2 唯一索引

> 索引列的值必须唯一，但允许有空值

#### 1.3 复合索引（联合索引）

> 即一个索引包含多个列。
>
> 例：复合索引 `test_col1_col2_col3 `实际建立了`(col1)、(col1,col2)、(col,col2,col3) `三个索引。     

### 2. 基本语法

#### 2.1 创建

````sql
CREATE INDEX 索引名 ON table(columnname(length)，...);//字符串必须给长度
````

````sql
ALTER table ADD INDEX 索引名 ON(columnname(length),...);
````

#### 2.2 删除

````sql
DROP INDEX 索引名 ON table;
````

#### 2.3 查看

````sql
SHOW INDEX FROM table；
````

#### 2.4 聚集索引和非聚集索引的区别

> `聚集索引`就是索引和数据存放在一块，辅助键只存储辅助键和主键，找到索引就找到数据了。
>
> `非聚集索引`就是主键只存储了主键，辅助键只存储了辅助键。数据存储到独立的地方，每个叶子节点都使用一个地址指向了数据。
>
> 一张表只能有一个聚集索引，一般是主键，`InnoDB`就是聚集索引，所以它必须有主键。而`MyISAM`是非聚集索引。
>
> `InnoDB`若使用主键id（聚集索引）作为条件来查找，可查到对应的叶子节点，获取行数据，若使用name（非聚集）来查找，它会先找到叶子节点对应的主键id，然后再获取行数据。
>
> `MyISAM`的索引和数据是分开的，每个叶子节点存储了一个指向数据的地址，所以辅助键检索无需先找到主键。

### 3. 性能分析

#### Explain

|  id  | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
| :--: | :---------: | :---: | :--: | :-----------: | :--: | :-----: | :--: | :--: | :---: |
|      |             |       |      |               |      |         |      |      |       |

#### 3.1 语法：`Explain+SQL语句`

#### 3.2 能干嘛：

* 表的读取顺序

  ````sql
  mysql> explain select t2.* from(
  	-> select t3.id
      -> from t3
      -> where t3.other_column = '') s1,t2
      -> where s1.id = t2.id;
  ````

  |  id  |   select_type   |   table   |
  | :--: | :-------------: | :-------: |
  |  1   |     PRIMARY     | <derived> |
  |  1   |     PRIMARY     |    t2     |
  |  2   | DERIVED（衍生） |    t3     |

  > id相同，table（表）按上往下执行，id不同，id值越大，优先权越高。

* 数据读取操作的操作类型（select_type）

  | 序号 | select_type  |
  | :--: | :----------: |
  |  1   |    SIMPLE    |
  |  2   |   PRIMARY    |
  |  3   |   SUBQUERY   |
  |  4   |   DERIVED    |
  |  5   |    UNION     |
  |  6   | UNION RESULT |

  

  1. SIMPLE：简单的`select`查询，查询中不包括子查询或UNION
  2. PRIMARY：查询中包含子查询，最外层执行的则为PRIMARY
  3. SUBQUERY：在`select`或`where`列表中包含子查询
  4. DERIVED：在`from`列表中包含的子查询被标记为DERIVED(衍生)，MySQL会递归执行这些子查询，把结果放在临时表
  5. UNION：若第二个`select`出现再UNION之后，则被标记为UNION，若UNION包含在`from`子句的子查询中，外层`select`将被标记为：DERIVED
  6. UNION RESULT：从UNION表获取结果的`select`

  `type`：system>const>eq_ref>ref>range（`范围扫描`）>index（`索引扫描`）>ALL（最好到最差，一般到ref或range就不错了，出现ALL就说明是`全盘扫描`，需要优化）

* 哪些索引可以使用（possible_keys）

  1. mysql理论上用到了哪些索引
  2. `覆盖索引`

* 哪些索引被实际使用（key）

  1. 实际使用的索引。如果为NULL，则没有使用索引
  2. 查询中若使用了覆盖索引，则该索引仅出现在key列表中

  `key_len`：索引用的越少，它就越小，则越好

  `ref`：最好是`const`，用到几个索引就有几个

* 表之间的引用

  `Extra`：出现Using filesort，Using temporary（常用于`order by`，`group by`）则需要优化

* 每张表有多少行被优化器查询（`越少越好`）

### 4. 索引失效原因分析

#### （1）全值匹配我最爱

#### （2）`最佳左前缀法则`（带头大哥不能死（第一个就像火车头，后面就是车厢，没有火车头还能走吗），中间兄弟不能断（就像楼梯，中间断了还能走吗）。）

> 如果是组合索引（复合索引），要遵守最左前缀法则，即查询从索引的最左边开始并且不能跳过索引的中间列

#### 例子：

1. 创建索引

   ````sql
   create index idx_name_age_sex on table(name,age,sex);
   ````

2. 查询条件

   ````sql
   select * from table where age=23 and sex='男'；
   select * from table where age=23；
   select * from table where sex='男'；
   select * from table where name='小陈' and sex='男'；
   ````

   必须加上name条件

   使用`show index 索引名 on table;`可以看到组合索引的顺序值

#### （3）不在索引列上做任何操作（计算、函数、（自动或手动）类型转换），会导索引失效而转向全表扫描ALL

#### （4）存储引擎不能使用索引中范围条件右边的列

> 范围（>，<），in()，between，like

			#### 例子：

1. ​	创建索引

   ````sql
   create index idx_name_age_sex on table(name,age,sex);
   ````

2. 查询条件

   ````sql
   select * from table where name='小陈' and age>11 sex='男';
   ````

   这个时候就不用创建age索引了

#### （5）尽量使用覆盖索引（只访问索引的查询（索引列和查询列一致）），减少select *

> 这样会在Extra多个Using index（优化效果好）

#### （6）mysql在使用不等于（！= 或 <>）的时候无法使用索引会导致全表扫描

#### （7）is null，is not null 也无法使用索引

#### （8）like以通配符开头（'%abc...'）mysql索引失效会变成全表扫描的操作

1. %写在右边 ，即like 'cjj%'时索引不会失效

2. 解决两边%%和左边%

   `覆盖索引`

#### （9）字符串不加单引号索引失效

#### （10）少用or，用它来连接时会索引失效

### 5. order by关键字优化

#### 5.1 order by满足两情况，会使用Index方式排序：

1. order by语句使用索引`最佳左前缀法则`
2. 使用where子句与order by子句的条件列满足索引`最佳左前缀法则`

#### 5.2 filesort两种算法

##### 5.2.1 双路排序

> 在mysql4.1之前，采用的是双路排序，需要进行两次扫描磁盘

##### 5.2.2 单路排序

> 从磁盘读取查询需要的所有列，按照order by列在buffer对它们进行排序，然后扫描排序后的列表进行输出，它的效率更快一些，避免了第二次读取数据。并把随机IO变成了顺序IO，但它会产生更多的空间，因为它把没一行都保存在内存中了。

使用单路遇到的问题

> 因为取出来的数据的总大小超过了sort_buffer的容量，导致每次只能取出sort_buffer容量大小的数据进行排序，导致进行了多次I/O操作

优化策略

1. 增大sort_buffer_size参数的大小
2. 增大max_length_for_sort_data参数的大小

#### 5.3 总结

1. mysql两种排序方式：文件排序（filesort）或扫描有序索引排序（index）

2. mysql能为排序和查询使用相同的索引

3. key a_b_c(a,b,c)

   **不会产生filesort**

   order by a

   order by a,b

   order by a,b,c

   order by a desc,b desc,c desc

   **如果where使用索引的最佳左前缀法则定义为常量，则order by能使用索引**

   where a = const order by b，c

   where a = const and b = const order by c

   where a = const and b > const order by b,c

   **会产生filesort**

   order by a asc,b desc,c desc      //排序不一致

   where g = const order by b,c      //丢失a（大哥）索引

   where a = const order by c         //丢失b（中间兄弟）索引

   where a = const order by a,d      //d不是索引的一部分

   where a in(...) order by b,c          //前面是范围，所以后面无效，必须是a开始

### 6. 慢查询日志

#### 6.1 命令

​	是否开启：`show variables like '%slow_query_log%';`

​	如何开启：`set global slow_query_log=1;`

#### 6.2 什么样的SQL才会记录到慢查询日志中

​	查看多少秒会被记录：`show variables like '%long_query_time%';`

​	设置阙值时间：`set global long_query_time=3;`

### 7. 批量插入数据脚本

#### 7.1 设置参数

​	是否开启：`show variables like 'log_bin_trust_function_creators';`

​	如何开启：`set global log_bin_trust_function_creators=1;`

​	**如果重启以上的配置都会消失，需要在配置文件上才能永久配置：**

​	windows下`my.ini`的[mysqld]加上`log_bin_trust_function_creators=1;`	

​	linux下 /etc/下的my.cnf的[mysqld]加上`log_bin_trust_function_creators=1;`	

#### 7.2 创建函数

生成字符串：

````sql
DELIMITER $$
CREATE FUNCTION rand_string(n int) returns VARCHAR(255)
BEGIN
DECLARE chars_str VARCHAR(100) DEFAULT 'abcdefghijklmnopqrsduvwxyzABCDEFGHIJKLMNOPQRSDUVWXYZ';
DECLARE return_str VARCHAR(255) DEFAULT '';
DECLARE i INT DEFAULT 0;
WHILE i < n DO
SET return_str=CONCAT(return_str,SUBSTRING(chars_str,FLOOR(1+RAND()*52),1));
SET i = i+1;
END WHILE;
RETURN return_str;
END $$
````

生成id：

````sql
DELIMITER $$
CREATE FUNCTION rand_id() RETURNS INT(5)
BEGIN
DECLARE i INT DEFAULT 0;
SET i = FLOOR(RAND()*10000-1000);
RETURN i;
end $$
````

删除：

````sql
drop function rand_id;
````

#### 7.3 创建存储过程

插入数据：

````sql
DELIMITER $$
CREATE PROCEDURE insert_emp(IN START INT(10),IN max_num INT(10),IN job VARCHAR(20),IN mgr INT(10))
BEGIN
DECLARE i INT DEFAULT 0;
#set autocommit = 0 把autocommit设置成0
SET autocommit = 0;
REPEAT
SET i = i + 1;
INSERT INTO emp(ename,job,mgr,hiredate,sal,comm,deptno)VALUES(rand_string(6),job,mgr,CURDATE(),2000,400,rand_id());
UNTIL i = max_num
END REPEAT;
COMMIT;
END $$
````

#### 7.4 调用

````sql
DELIMITER;
CALL insert_emp(10,100);
````

### 8. show-profile

#### 8.1 设置参数

​	是否开启：`show variables like 'profiling';`

​	如何开启：`set profiling = on;`

​	查看所有结果：`show profiles;`

​	诊断单个SQL：`show profile cpu,block io for query Query_ID;`

出现以下四个需要优化：

1. converting HEAP to MyISAM	查询结果太大，内存都不够用往磁盘上搬了
2. Creating tmp table   创建临时表
3. Copying to tmp table on disk   把内存中临时表复制到磁盘，危险
4. locked

### 9. 全局查询日志

> 永远不要在生产环境中开启该功能

#### 9.1 设置参数

​	开启：`set global general_log = 1;`

​			  	`set global log_output = 'TABLE';`

​	此后，你所编写的sql语句，将会记录到mysql库里的general_log表，可以使用下面命令查看

````sql
select * from mysql.general_log;
````

​	在mysql的my.cnf中，设置如下：

````mysql
#开启
general_log=1
#记录日志文件的路径
general_log_file=/path/logfile
#输出格式
log_output=FILE
````

## 锁机制

### 1. 表级锁（MyISAM）

> 开销小，加锁快；不会出现死锁；锁的粒度大，发生锁冲突概率高，并发度低。

#### 加锁的方式：

自动加锁。查询操作（`SELECT`），会自动给涉及的所有表加读锁，更新操作（`UPDATE`、`DELETE`、`INSERT`），会自动给涉及的表加写锁。也可以显示加锁：

​	查看哪些表被锁：`show open tables;`	

​	手动增加表锁：`lock table 表名 read(或write),表名2 read(或write).....;`

​	释放锁：`unlock tables;`

**加读锁（共享锁）案例：**

> 当前用户使表被锁时，当前用户只能对被锁表进行读，不能读写其他表。其他用户可以对任何表进行读操作，但对被锁表进行写操作时会进入阻塞状态，需要等当前用户释放锁，会立即执行写操作。即读操作时是共享的。

**加写锁（独占锁）案例：**

> 当前用户使表被锁时,当前用户只能对被锁表进行读和写，不能读写其他表。其他用户在进行读写被锁表时会进入阻塞状态，但能读写其他表。

`简而言之，就是读锁会阻塞写，但不会阻塞读，写锁会阻塞读和写。`

**优化分析：**

查看：`show status like 'table%';`

![image-20200609211656130](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200609211656130.png)

table_locks_waited越大越不好，需要优化。

### 2. 行级锁（InnoDB）

> 开销大，加锁慢；会出现死锁；锁的粒度小，发生锁冲突概率低，并发度高。
>
> InnoDB只有在通过索引条件检索数据时使用行级锁，否则使用表锁

#### 加锁的方式：

自动加锁。对于`UPDATE`、`DELETE`和`INSER`语句，InnoDB会自动给涉及数据集加排他锁；对于普通`SELECT`语句，InnoDB不会加任何锁；当然我们也可以显示的加锁：

共享锁：select * from 表名 where … + lock in share more

排他锁：select * from 表名 where … + for update

#### 2.1 事务四大特性（ACID）

1. `原子性`：是不可分割的最小操作单位，要么全都执行，要么全都不执行。
2. `一致性`：事务开始到完成时，数据都必须保持一致。
3. `隔离性`：并发访问数据库时，事务之间互不干扰，各并发事务之间数据库是独立的。
4. `持久性`：事务完成之后，数据就会被永久的保存到磁盘，即使出现系统故障也不会影响。

#### 2.2 并发事务带来的问题

1. `脏读`：事务A读取到了事务B已修改但未提交的数据。
2. `不可重复读`：同一个事务中，两次读取到的数据不一样。（感觉上是要这样，但是某些场景需要用到同一个事务读取到一样的数据）
3. `幻读`：事务A读取到了事务B提交的新增数据。

#### 2.3 事务的隔离级别

1. `read uncommitted`：读未提交	//产生的问题：脏读、不可重复读、幻读
2. `read committed`：读已提交   //产生的问题：不可重复读、幻读（`Oracle默认`）
3. `repeatable read`：可重复读   //产生的问题：幻读（`MySQL默认`）
4. `serializable`：串行化   //可以解决所有问题

查看事务隔离级别：`select @@tx_isolation;`或`show variables like 'tx_isolation';`

设置事务隔离级别：`set global transaction isolation level 级别字符串；`

**隔离级别从1到4安全性越来越高，但是效率越来越低**

#### 2.4 间隙锁

> 当用范围条件时，对于条件范围内不存在的记录，叫做间隙，例如：where id >1 and id<6;此时如果id为1,2,4,5,6。3就是这个间隙，当事务A已修改但未提交，事务B在此时插入一条id为2的数据时会被阻塞。

### 3. 行锁定分析

查看：`show status like 'innodb_row_lock';`

![image-20200610170038249](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200610170038249.png)

##  trace分析

开启：`set optimizer_trace="enabled=on",end_markers_in_json=on;`

​	 	 	`set optimizer_trace_max_mem_size=1000000;`

检查：`select * from information_schema.optimizer_trace;`