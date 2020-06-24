# Dubbo详解

## 介绍

### 1. 什么是RPC

> RPC（Remote Procedure Call）是指远程过程调用，是一种进程间通信方式，是一种技术的思想，而不是规范。
>
> 例如电脑A中的方法A调用电脑B中的方法B。
>
> RPC两个核心模块：通讯，序列化。

### 2. 什么是dubbo

> 是一款高性能、轻量级的开源Java RPC框架。
>
> 它提供了三大核心能力“面向接口的远程方法调用、智能容错、负载均衡。
>
> dubbo本身并不是一个服务软件。它其实就是一个jar包，能够帮你的java程序连接到zookeeper。

#### 2.1 可视化监控程序dubbo-admin

1. 下载`dubbo-admin`

   **地址：**https://github.com/apache/dubbo-admin/tree/master

2. 解压

   dubbo-admin\src\main\resources\application.properties	可以指定zookeeper地址

3. 在项目目录下打包`dubbo-admin`或用IDEA打开

   ````java
   mvn clean package -Dmaven.test.skip=true
   ````

4. 输入用户名密码

   ````bash
   root
   root
   ````

#### 三、Zookeeper

> ZooKeeper 是一个开源的分布式协调服务，ZooKeeper 一个最常用的使用场景就是用于担任服务生产者和服务消费者的注册中心。服务生产者将自己提供的服务注册到 ZooKeeper 中心，服务的消费者在进行服务调用的时候先到 ZooKeeper 中查找服务，获取到服务生产者的详细信息之后，再去调用服务生产者的内容与数据。

#### （1）下载地址

http://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.4.14/

或

http://archive.apache.org/dist/zookeeper/

#### （2）使用步骤

1. 