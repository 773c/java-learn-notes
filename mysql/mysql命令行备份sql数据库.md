**备份：**

````bash
mysqldump -uroot -proot 数据库名 > 盘符位置\任意名称.sql
````

**还原：**

````bash
首先创建数据库 mysql > create database db1;
使用 mysql > use db1;
最后 mysql > source 盘符位置\任意名称.sql
````

