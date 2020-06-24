# 安装版RabbitMQ

## 步骤

### 1.安装

#### 1.1 按以下顺序安装

`（1）erlang`

````bash
rpm -ivh erlang-22.0.7-1.el7.x86_64.rpm
````

`（2）socat`

````bash
rpm -ivh socat-1.7.3.2-2.el7.x86_64.rpm
````

`（3）rabbitmq`

````bash
rpm -ivh rabbitmq-server-3.7.18-1.el7.noarch.rpm
````

> 在安装完后，rabbitmq会默认去读取/etc/rabbitmq/下的rabbitmq.config文件。
>
> 但是默认/etc/rabbitmq/下是没有这个配置文件的，所以需要从/usr/share/doc/rabbitmq-server-3.7.18/rabbitmq.config.example复制到/etc/rabbitmq/rabbitmq.config

#### 1.2 复制配置文件

````bash
cp /usr/share/doc/rabbitmq-server-3.7.18/rabbitmq.config.example /etc/rabbitmq/rabbitmq.config
````

#### 1.3 修改配置文件

````bash
vim /etc/rabbitmq/rabbitmq.config
# 开放来宾用户，去掉%%和逗号
{loopback_users,[]}
````

#### 1.4 开启插件管理

````bash
rabbitmq-plugins enable rabbitmq_management
````

![image-20200623111115398](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200623111115398.png)

> 如上说明启动成功

#### 1.5 启动

````bash
systemctl start rabbitmq-server
````

> 输入`ip地址:15672`登录管理界面
>
> 默认用户名密码都是`guest`

#### 1.6 启动慢，老是连接超时解决

##### 1、 查看当前主机名

````bash
vim /etc/sysconfig/network
````

![img](https://img-blog.csdnimg.cn/20191205155653926.png)

##### 2、修改hosts文件

````bash
vim /etc/hosts
````

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191205155952169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbmdGZWlfeQ==,size_16,color_FFFFFF,t_70)

##### 3、退出保存然后重启Rabbitmq就可以了

````bash
systemctl restart rabbitmq-server
````