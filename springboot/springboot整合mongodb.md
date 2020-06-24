#### 框架介绍

> Mongodb是为快速开发互联网Web应用而构建的数据库系统，其数据模型和持久化策略就是为了构建高读/写吞吐量和高自动灾备伸缩性的系统。

##### 一、安装

![img](http://www.macrozheng.com/images/arch_screen_37.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181109153130575.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NTQ2ODkx,size_16,color_FFFFFF,t_70)

![img](http://www.macrozheng.com/images/arch_screen_38.png)

在安装路径下创建data\db和data\log两个文件夹

![img](http://www.macrozheng.com/images/arch_screen_39.png)

在安装路径下创建mongod.cfg配置文件

````bash
systemLog:
 destination: file
 path: D:\developer\env\MongoDB\data\log\mongod.log
storage:
 dbPath: D:\developer\env\MongoDB\data\db
````

安装为服务（运行命令需要用管理员权限）

````bash
D:\developer\env\MongoDB\bin\mongod.exe --config "D:\developer\env\MongoDB\mongod.cfg" --install
````

服务相关命令

````bash
启动服务：net start MongoDB
关闭服务：net stop MongoDB
移除服务：D:\developer\env\MongoDB\bin\mongod.exe --remove
````

下载客户端程序：https://download.robomongo.org/1.2.1/windows/robo3t-1.2.1-windows-x86_64-3e50a65.zip

##### 二、Spring Data Mongodb

> 和Spring Data Elasticsearch类似，Spring Data Mongodb是Spring提供的一种以Spring Data风格来操作数据存储的方式，它可以避免编写大量的样板代码。

##### （1）常用注解

* @Document:标示映射到Mongodb文档上的领域对象
* @Id:标示某个域为ID域
* @Indexed:标示某个字段为Mongodb的索引字段

##### （2）Spring Data方式的数据操作

继承MongoRepository接口可以获得常用的数据操作方法

![img](http://www.macrozheng.com/images/arch_screen_42.png)

##### 三、整合Mongodb实现文档操作

##### （1）在pom.xml中添加相关依赖

````java
<!---mongodb相关依赖-->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
````

