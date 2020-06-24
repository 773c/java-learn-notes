##### 一、网络NETWORK

````bash
bind 127.0.0.1     #绑定的ip

protected-mode   yes     #保护模式

port   6379     #端口设置
````

##### 二、通用 GENERAL

````bash

daemonize   no     #以守护进程的方式运行，默认是 no ，需要将他设置为yes

pidfile /var/run/redis_6379.pid     #如果以后台的方式运行，我们就需要制定一个pid文件

loglevel notice     #默认为notice，生成环境

logfile   ""     #日志的文件位置名

databases   16     #数据库的数量，默认是16个

always-show-logo   yes     #是否开启redis-server中的图形
````

##### 三、快照SNAPSHOTTING

> 持久化，在规定的时间内，执行了多少次操作，则会以持久化的方式保存到文件 .rdb .aof中

````bash
save 900 1     #如果900s（15分钟）内，至少有 1 个 key 进行了修改，那么久进行持久化操作
save 300 10	   #如果300s（5分钟）内，至少有 10 个 key 进行了修改，那么久进行持久化操作
save 60 10000    #如果60s（1分钟）内，至少有 10000 个 key 进行了修改，那么久进行持久化操作
stop-writes-on-bgsave-error yes     #如果持久化操作出错，是否还需要继续工作
rdbcompression yes     #是否压缩rdb文件，需要消耗一些CPU的资源
rdbchecksum yes     #保存rdb文件的时候，进行错误的校验
dir ./     #rdb文件保存的目录
````

##### 四、复制REPLICATION

> 搭建多个redis的作用

##### 五、安全SECURITY

````bash
requirepass "密码"     #redis密码，默认为空
命令设置密码：
config set requirepass "123456"     #设置密码
config get requirepass     #获取密码
auth "密码"     #登录到redis
````

##### 六、客户端LIMITS或CLIENTS

````bash
maxclients 10000     #设置能连上redis的最大客户端的数量
maxmemory <bytes>     #redis配置最大的内存容量
maxmemory-policy noeviction     #内存到达上限之后的处理策略
#maxmemory-policy的六种机制
1、volatile-lru：只对设置了过期时间的key进行LRU（默认值） 
2、allkeys-lru ： 删除lru算法的key   
3、volatile-random：随机删除即将过期key   
4、allkeys-random：随机删除   
5、volatile-ttl ： 删除即将过期的   
6、noeviction ： 永不过期，返回错误
````

##### 七、AOF配置APPEND ONLY MODE

````bash
appendonly no     #默认不开启aof模式，默认是rdb方式持久化的，在大部分的情况下，rdb完全够用
appendfilename "appendonly.aof"     #持久化的文件名字

appendfsync always     #每次修改都是sync。消耗性能
appendfsync everysec     #每秒执行一次sync，可能会丢失者1s的数据
appendfsync no     #不执行sync，这个时候操作系统自己同步数据，速度最快
````