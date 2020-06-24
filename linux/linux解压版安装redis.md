# 解压版Redis

## 安装步骤

### 1. 解压

````bash
tar -zxvf 文件名
````

### 2. 在redis目录下安装基本环境

````bash
yum install gcc-c++
make
make install
````

redis的安装路径：`usr/local/bin`

### 3. 将redis配置文件redis.conf复制到/usr/local/bin/myconfig下

````bash
mkdir myconfig
cp /opt/redis-5.0.5/redis.conf myconfig
````

### 4. 设置为后台运行

````bash
#################GENERAL##################
daemonize yes
````

### 5. 启动服务

````bash
./redis-server myconfig/redis.conf
````

### 6. 查看进程

````bash
ps -ef | grep redis
````

### 7. 关闭redis服务

![image-20200620161412217](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200620161412217.png)

### 8. windows连接linux

![img](https://img2018.cnblogs.com/blog/1334215/201901/1334215-20190109152918345-1249653468.png)

添加一个`bind IP地址`

![img](https://img2018.cnblogs.com/blog/1334215/201901/1334215-20190109153017625-322781510.png)

protected-mode改为`no`

重启redis即可