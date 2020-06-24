# RabbitMQ详解

## 介绍

![image-20200623120158179](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120158179.png)

### 1. RabbitMQ

> 通过典型的`生产者`和`消费者`模型，`生产者`不断向消息队列中产生消息，`消费者`不断的从队列中获取消息。因为消息的生产和消费都是异步的，而且只关心消息的发送和接收，没有业务逻辑的侵入，轻松的实现系统间解耦。
>
> 它是用erlang语言开发的。

### 2. 下载地址

````bash
# RabbitMQ 和 erlang安装包
https://packagecloud.io/rabbitmq
# socat依赖包
http://www.dest-unreach.org/socat/download/
# socat的rpm安装包
http://www.rpmfind.net/linux/rpm2html/search.php?query=socat(x86-64)
````

### 3. 相关命令

````bash
# DOS命令 用来在不使用web管理界面的情况下操作rabbitmq
rabbitmqctl help
# 操作web管理界面的DOS命令
rabbitmq-plugins enable（开启服务）|list|disable（关闭服务）
````

### 4. 实现操作

#### 4.1 相关依赖

````xml
<dependency>
	<groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.7.2</version>
</dependency>
````

#### 4.2 创建虚拟主机

![image-20200623120326914](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120326914.png)

![image-20200623120343061](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120343061.png)

![image-20200623120352324](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120352324.png)

> Name必须以斜杆开头

![image-20200623120543522](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120543522.png)

> 创建完后，当前虚拟主机只能`来宾用户guest`可以访问，所以需要去设置其他用户可访问

##### 4.2.1 创建用户

![image-20200623120827950](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120827950.png)

![image-20200623120903547](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623120903547.png)

#### 4.3 第一种模型（直连）

![image-20200623121412333](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623121412333.png)

如上图模型中，有以下概念：

* P：生产者，也就是要发送消息的程序
* C：消费者：消息的接受者，会一直等待消息到来
* queue：消息队列，图中红色部分。类似一个邮箱，可以缓存消息，生产者向其投递消息，消费者从其中读取消息

##### 4.3.1 生产者代码

**Java代码：**

````java
//创建连接mq的连接工厂
ConnectionFactory connectionFactory = new ConnectionFactory();
//设置连接的主机
connectionFactory.setHost("192.168.68.137");
//设置连接的端口号
connectionFactory.setPort(5672);
//设置连接哪个虚拟主机
connectionFactory.setVirtualHost("/cjj");
//设置访问虚拟主机的用户名
connectionFactory.setUsername("cjj");
//设置访问虚拟主机的密码
connectionFactory.setPassword("cjj");
//获取连接对象
Connection connection = connectionFactory.newConnection();
//获取连接通道
Channel channel = connection.createChannel();
//通道绑定消息队列
//参数1：队列名
//参数2：用来定义队列是否要持久化
//参数3：是否独占队列
//参数4：是否在消费完成后自动删除队列
//参数5：额外参数
channel.queueDeclare("hello",false,false,false,null);
//发布消息
//参数1：交换机名称
//参数2：队列名称
//参数3：传递消息额外设置
//参数4：消息的具体内容
channel.basicPublish("","hello",null,"66666666666666".getBytes());
//关闭通道
channel.close();
//关闭连接
connection.close();
````

##### 4.3.2 消费者代码

**Java代码：**

````java
//创建连接mq的连接工厂
ConnectionFactory connectionFactory = new ConnectionFactory();
//设置连接的主机
connectionFactory.setHost("192.168.68.137");
//设置连接的端口号
connectionFactory.setPort(5672);
//设置连接哪个虚拟主机
connectionFactory.setVirtualHost("/cjj");
//设置访问虚拟主机的用户名
connectionFactory.setUsername("cjj");
//设置访问虚拟主机的密码
connectionFactory.setPassword("cjj");
//获取连接对象
Connection connection = connectionFactory.newConnection();
//创建通道
Channel channel = connection.createChannel();
//通道绑定消息队列
channel.queueDeclare("hello",false,false,false,null);
//消费消息
//参数1：队列名称
//参数2：开始消息的自动确认机制
//参数3：消费时的回调接口
channel.basicConsume("hello",true,new DefaultConsumer(channel){
	//最后一个参数：消息队列取出的消息
	@Override
	public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
		System.out.println("消费者取出消息为："+new String(body));
	}
});
````

##### 4.3.3 API参数详解

````java
// 通道绑定队列
channel.queueDeclare("hello",false,false,false,null);

参数1：队列名称
参数2：用来定义队列是否需要持久化 true 表示是 false 表示否	
      注：消息是没有被持久化的
参数3：是否独占队列 true 表示是 false 表示否
参数4：是否在消费完成后自动删除队列 true 表示是 false 表示否
参数5：额外附加参数

    
// 发布消息
channel.basicPublish("","hello",null,"66666666666666".getBytes());

参数1：交换机名称
参数2：队列名称
参数3：消息额外设置
      注：这里可以进行消息的持久化，传入MessageProperties.PERSISTENT_TEXT_PLAIN即可
参数4：需要发布的消息
````

> 注：生产者和消费者的通道绑定队列需一致

#### 4.4 第二种模式（work queue）

> 当消息处理比较耗时的时候，可能产生消息的速度远远大于消费消息的速度。长此以来，消息就会堆积的越来越多，无法及时处理。
>
> 此时使用work模型，让多个消费者绑定到一个队列，共同消费队列的消息。队列中的消息一旦消费，就会消失。

![image-20200623171929773](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623171929773.png)

角色：

* P：生产者：任务的发布者
* C1：消费者1，领取任务并且完成任务，假设完成的速度较慢
* C2：消费者2，领取任务并完成任务，假设完成的速度较快

##### 4.4.1 消费者代码

> channel.basicConsume("hello",`true`,new DefaultConsumer(channel){}）
>
> 按照上面的第二个参数`消息自动确认`为ture会导致消费者在消费时，两个消费者是轮流进行的
>
> `消费者1`：0，2，4，6。。。
>
> `消费者2`：1，3，5，7。。。
>
> 这样的话，当某个消费者完成的更慢时，就会造成性能的低下。

**所以以下方法就是解决的方案：**

**消费者1：**

````java
//创建连接mq的连接工厂
ConnectionFactory connectionFactory = new ConnectionFactory();
//设置连接的主机
connectionFactory.setHost("192.168.68.138");
//设置连接的端口号
connectionFactory.setPort(5672);
//设置连接哪个虚拟主机
connectionFactory.setVirtualHost("/cjj");
//设置访问虚拟主机的用户名
connectionFactory.setUsername("cjj");
//设置访问虚拟主机的密码
connectionFactory.setPassword("cjj");
//获取连接对象
Connection connection = connectionFactory.newConnection();
//创建通道
final Channel channel = connection.createChannel();
//每次消费1个
channel.basicQos(1);
//通道绑定对象
channel.queueDeclare("work",true,false,false,null);
//消费消息
//参数1：队列名称
//参数2：开始消息的自动确认机制
//参数3：消费时的回调接口
channel.basicConsume("work",false,new DefaultConsumer(channel){
	//最后一个参数：消息队列取出的消息
	@Override
	public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
		System.out.println("消费者-1取出消息为："+new String(body));
		//参数1：确认队列中的那个消息
		//参数2：是否开启多个消息同时确认
		channel.basicAck(envelope.getDeliveryTag(),false);
	}
});
````

**消费者2：**

````java
//创建连接mq的连接工厂
ConnectionFactory connectionFactory = new ConnectionFactory();
//设置连接的主机
connectionFactory.setHost("192.168.68.138");
//设置连接的端口号
connectionFactory.setPort(5672);
//设置连接哪个虚拟主机
connectionFactory.setVirtualHost("/cjj");
//设置访问虚拟主机的用户名
connectionFactory.setUsername("cjj");
//设置访问虚拟主机的密码
connectionFactory.setPassword("cjj");
//获取连接对象
Connection connection = connectionFactory.newConnection();
//创建通道
final Channel channel = connection.createChannel();
//每次消费1个
channel.basicQos(1);
//通道绑定对象
channel.queueDeclare("work",true,false,false,null);
//消费消息
//参数1：队列名称
//参数2：开始消息的自动确认机制
//参数3：消费时的回调接口
channel.basicConsume("work",false,new DefaultConsumer(channel){
	//最后一个参数：消息队列取出的消息
	@Override
	public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
		try {
			Thread.sleep(2000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
		System.out.println("消费者-2取出消息为："+new String(body));
        //参数1：确认队列中的那个消息
		//参数2：是否开启多个消息同时确认
		channel.basicAck(envelope.getDeliveryTag(),false);
	}
});
````

#### 4.5 第三种模型（fanout广播模型）

![image-20200623212805370](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623212805370.png)



























![image-20200623212829088](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623212829088.png)









![image-20200623212900762](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623212900762.png)























![image-20200623212915650](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623212915650.png)