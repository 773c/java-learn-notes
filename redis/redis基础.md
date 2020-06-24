##### 一、基础命令

##### （1）切换数据库

> 默认有16个数据库，默认使用第0个，使用select进行切换

````bash
select 3	//切换第3个
````

##### （2）查看所有key

````bash
keys *
````

##### （3）清空当前数据库

````bash
flushdb
````

##### （4）清空所有数据库

````bash
flushall
````

##### （5）查看数据库大小

````ba
dbsize
````

##### 二、String操作命令

##### （1）添加数据

````bash
set key value
````

##### （2）获取数据

````bash
get key
````

##### （3）移除数据库的数据

````  bash
move key 1(db)	//移除数据库1的key
````

##### （4）设置存在key的过期时间

````bash
expire key 10	//设置key的过期时间为10秒
````

##### （5）是否存在key

````bash
exists key 
````

##### （6）查看当前key的过期时间

````bash
ttl key
````

##### （7）查看key的类型

````bash
type key
````

##### （8）拼接字符串

````bash
append key value
````

##### （9）自增1，自减1

````bash
incr key	//加1
decr key	//减1
````

##### （10）自定义增量

````bash
incrby key 10	//加10
decrby key 10	//减10
````

##### （11）字符串截取（不改变key）

````bash
getrange key 0 -1	//全部
````

##### （12）替换

````bash
setrange key 1 xx	//从下标为1开始替换长度为xx
````

##### （13）存入数据并设置key过期时间

````bash
setex key 10 "cjj"	//设置key的值为cjj，10秒后过期
````

##### （14）存入数据但不可重复存入

````bash
setnx key value	//如果key存在，则存入失败，返回0
````

##### （15）批量存入数据

````bash
mset key1 value1 key2 value2....
````

##### （16）批量获取数据

````bash
mget key1 key2...
````

##### （17）批量存入数据但不能重复存入

````bash
msetnx key1 value1 key2 value2....	//如果key1或key2存在，则都存入失败，因为是原子性的操作。
````

##### （18）先get后set

````bash
getset key value	//如果不存在则返回nil，存在返回原来的值，再get则获取到新的值。
````

##### 三、List列表操作命令

> 在redis里面，list可以是栈或队列，所有list命令都是以l开头的。list对象名就相当于key

##### （1）添加数据

````bash
lpush key value	//像栈一样，先进后出，往左添加，相当于头
rpush key value	//像栈一样，先进后出，往右添加，相当于尾
````

##### （2）获取数据

````bash
lrange key 0 -1	//像栈一样，获取全部
````

##### （3）移除数据

````bash
lpop key	//像栈一样，移除左边第一个元素
rpop key	//像栈一样，移除右边第一个元素
````

##### （4）移除指定个数数据

````bash
lrem key 2 value	//移除2个value
````

##### （5）截取指定长度（改变了key）

````bash
ltrim key 1 2	//截取下标为1到2的数据
````

##### （6）通过下标获取值

````bash
lindex key 0	//获取第一个元素
````

##### （7）获取list长度

````bash
llen key
````

##### （8）移除最后一个元素然后将它添加到新的list中

````bash
rpoplpush key1 key2	//key1=>key2
````

##### （9）将list中下标的值替换成另一个值	

````bash
lset key 0 value	//将0位置的value替换，如果key（list）不存在会报错
````

##### （10）将value值插入到list中value的前面或后面

````bash
linsert key before value1 value2	//将value2插入到value1的前面
linsert key after value1 value2		//将value2插入到value1的后面
````

##### 四、Set集合操作命令

##### （1）添加数据

````bash
sadd key value
````

##### （2）获取所有数据

````bash
smembers key
````

##### （3）判断某个value是否存在

````bash
sismember key value
````

##### （4）获取Set集合中的元素个数

````bash
scard key
````

##### （5）移除Set集合的某个value值

````bash
srem key value
````

##### （6）随机获取某个元素

````bash
srandmember key	2	//随机获取2个元素
````

##### （7）随机删除某个元素

````bash
spop key
````

##### （8）将一个指定的value值移动到另一个Set集合中

````bash
smove key1 key2 value	//将key1中的value移动到key2
````

##### （9）获取两个Set集合的交集

````bash
sinter key1 key2	//共同好友的实现
````

##### （10）获取两个Set集合的差集

````bash
sdiff key1 key2
````

##### （11）获取两个Set集合的并集

````bash
sunion key1 key2
````

##### 五、Hash哈希操作命令

##### （1）添加数据

````bash
hset key1 key11 value11
````

##### （2）获取数据

````bash
hget key1 key11
````

##### （3）批量添加数据

````bash
hmset key1 key11 value11 key12 value12
````

##### （4）批量获取数据

````bash
hmget key1 key11 key12
````

##### （5）获取全部value

````bash
hgetall key	 //显示所有value的key-valuue
````

##### （6）删除指定的key，value也就消失了

````bash
hdel key1 key11
````

##### （7）获取长度

````bash
hlen key
````

##### （8）判断hash中的字段是否存在

````bash
hexists key1 key11
````

##### （9）指定自增，自减

````bash
hincrby key1 key11 10	//加10
hincrby key1 key11 -10	//减10
````

##### 六、Zset有序集合操作命令

##### （1）添加数据

````bash
zadd key 1 one 2 two	//按顺序添加one，two
````

##### （2）获取所有数据

````bash
zrange key 0 -1
````

##### （3）获取指定key-value

````bash
zrangebyscore key -inf 50 withscores	//获取负无穷到50的序号的key和value值
````

##### （4）根据value移除元素

````bash
zrem key value
````

##### （5）获取个数

````bash
zcard key
````

##### （6）从大到小排序

````bash
zrevrange key 0 -1
````

##### 七、Geospatial地理位置

> 底层是用zset实现的，所以可以使用zset的命令

##### （1）添加地理位置

````bash
geoadd china:city 116.40 39.90 beijing	//纬度、经度、名称
````

##### （2）获取指定城市的纬度和经度

````bash
geopos china:city beijing
````

##### （3）获取两个地区的距离

````bash
geodist china:city beijing shanghai km
````

##### （4）获得附近的人

````bash
georadius china:city 纬度 经度 500 km 	//搜索500KM范围内的数据
withdist	//显示到我的距离
withcoord	//显示对方经纬度 
count 5		//只显示5个
````

##### （4）获得附近的城市

````bash
georadiusbymember china:city beijing 500 km	
````

##### 八、Hyperloglog

> 应用场景：网页的UV（访问的次数），一般统计一些不重复的数量

##### （1）创建一组元素

````bash
pfadd key value1 valuue2....
````

##### （2）统计元素的基数数量

基数就是不重复的元素	{1,3,5,7,9,7} => 1,3,5,7,9

````bash
pfcount key 
````

##### （3）合并两组

````bash
pfmerge key3 key1 key2
````

##### 九、Bitmap

> 应用场景：统计用户信息，登录、未登录，打卡

##### （1）设置值

````bash
setbit key 0 1	//0代表星期1 1代表打卡	0代表未打卡
````

##### （2）获取某一天的值

````bash
getbit key 0
````

##### （3）统计打卡多少次

````bash
bitcount key
````

##### 十、事务

> Redis单条命令是保证原子性的，但是事务不保证原子性。
>
> Redis事务没有隔离级别的概念

##### （1）执行步骤：

* 开启事务（`multi`）
* 命令
* 执行事务（`exec`）

放弃事务（`discard`）

##### （2）异常

**编译型异常：**

> 事务中的所有命令不会执行

**运行时异常：**

> 其他命令正常运行，所以也就是没有原子性

##### （3）监控（Watch）

> 相当于乐观锁

**悲观锁：**

* 很悲观，认为什么时候都会出问题，所以无论左什么都会加锁

**乐观锁：**

* 和乐观，认为什么时候都不会出问题，所以在更新数据的时候去判断一下，在此期间是否有人修改过这个数据
* 获取version
* 更新的时候比较 version

![image-20200621104949203](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200621104949203.png)

但另一个线程修改money后，当前线程会执行失败。需要`unwatch`重新监控。

##### 十一、持久化

**RDB（Redis DataBase）**
![image-20200621135118860](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200621135118860.png)

**AOF（Append Only File）**

![image-20200621144624746](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200621144624746.png)

##### 十二、主从复制

> 主从复制是指将一台Redis服务器的数据，复制到其他的Redis服务器上。数据的复制是单向的，只能由主节点到从节点。一个主节点可以有多个从节点，而一个从节点只能有一个主节点。
>
> 一般来说，要将Redis运用于工程项目中，只使用一台Redis服务器是不可能的，因为可能会宕机。所以一般至少3台以上。一般Redis的内存最大为20G。
>
> 主机可以读写，从机只能读。

##### （1）主从复制的作用：

* 数据冗余
* 故障恢复
* 负载均衡
* 高可用基石

##### （2）环境配置

##### （2-1）基本命令

查看信息

````bash
info replication
# Replication
role:master		//主机
connected_slaves:0		//从机为0
master_replid:5a139860ae4b5ab17ab2191bedf486b32a76247f
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0
````

##### （2-2）集群

**修改配置文件**

* port
* logfile	"xxx.log"（防止重名）
* dbfilename	dumpxxx.rdb（防止重名）

**一主二从**

> 默认情况下，每一台Redis服务器都是`主节点`，一般情况下只要配置从机即可。
>
> 真实的主从配置是在配置文件中配置的，这样才是永久的，而使用命令是暂时的。
>
> 在哨兵没有出来之前，主从都是手动配置的。

**命令：**

````bash
slaveof ip地址 端口号	//认该端口号为主节点
slaveof no one		//将从机变为主机
````

**配置文件：**

````bash
###############################REPLICATION###################################
replicaof <masterip> <masterport>	//主机的ip地址和端口号
masterauth <master-password>	//如果有密码则填入
````

**复制原理：**

`全量复制`（重新连接master主机）：

1. slave连接到master主机后悔发送一个sync同步命令
2. master主机接到命令后，会启动后台的存盘进程，同时手机所有接收到的用于修改数据集命令，在后台进程执行完毕之后，master主机将传送整个数据文件到slave，并完成一次完全同步。

`增量复制`（已连接，master主机新添加的数据进行同步）

**哨兵模式**

> 当主机挂掉时，会自动的去选择一从机作为主机。

![image-20200621170109584](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200621170109584.png)

**配置哨兵配置文件**

sentinel.conf

````bash
sentinel monitor 自定义名称 ip地址 端口号 1
````

启动哨兵

````bash
redis-sentinel myconfig/sentinel.conf
````

全部配置

````bash
#如果有哨兵集群，需要配端口号
port 26379

#哨兵sentinel的工作目录
dir/tmp

#如果Redis有密码，需配置密码
sentinel auth-pass 自定义名称 密码

#主节点没有应答哨兵时，多久认为主节点下线，默认30秒
sentinel down-after-milliseconds 自定义名称 30

````

##### 十三、缓存穿透

> 当用户想要查询一个数据时，发现redis内存中没有，也就是缓存没有命中，于是向持久层数据库查询，发现也没有，于是本次查询失败。当用户很多的时候，缓存都没有命中（秒杀），都去请求数据库就会造成缓存穿透。
>
> 简而言之就是，缓存中查询不到，数据库也没有。

##### （1）解决方案

**布隆过滤器**

**缓存空对象**

##### 十四、缓存击穿



##### 十五、缓存雪崩









**问题：**Redis为什么单线程还这么快？

1. 首先，多线程不一定就比单线程快。
2. redis是将所有数据存放到内存中的。

