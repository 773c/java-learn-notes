# 安装版MySQL

## 安装步骤

### 1. 查看Linux本地是否已经安装（存在就卸载）

````bash
rpm -qa | grep -i mysql
````

### 2. 上传client和server的rpm到Linux

### 3. 安装mysql的服务端

````bash
rpm -ivh server安装包
````

### 4. 如果安装出现libc.so.6(GLIBC_2.14)(64bit) is needed by....

````bash
cd /usr/local
# 下载响应安装包
wget http://ftp.gnu.org/gnu/glibc/glibc-2.14.tar.gz 
或
# 这个是国内镜像，更快
wget http://mirror.bjtu.edu.cn/gnu/libc/glibc-2.14.tar.gz

tar xvf glibc-2.14.tar.gz

cd glibc-2.14

mkdir build

cd build

# 配置glibc并设置当前glibc-2.14安装目录
../configure --prefix=/usr/local/glibc-2.14  

make

make install

cp /usr/local/glibc-2.14/lib/libc-2.14.so /lib64/libc-2.14.so 

mv /lib64/libc.so.6 /lib64/libc.so.6.bak

LD_PRELOAD=/lib64/libc-2.14.so ln -s /lib64/libc-2.14.so /lib64/libc.so.6
````

> 注：如果最后一行命令执行出错，可通过
> LD_PRELOAD=/lib64/libc-2.12.so ln -s /lib64/libc-2.12.so /lib64/libc.so.6 再改回去
> 最后，执行strings /lib64/libc.so.6 | grep GLIBC，查看glibc是否更新
>
> 如上不行 就换mysql5.6

### 5. 安装service安装包

````bash
rpm -ivh service安装包名
````

### 6. 安装client安装包

````bash
rpm -ivh client安装包名
````

### 7. 启动mysql服务

````bash
service mysql start
````

### 8. 有的mysql版本没有默认密码，有的会随机生成一个密码，安装service安装包时有提示、

````bash
# 查看初始密码
cat /root/.mysql_secret   
````

### 9. 修改密码（两种方式）

````bash
/usr/bin/mysqladmin -u root password 'new-password'（默认提示修改方式）

set password for '用户名'@'主机名' = password('新密码');
````

### 10. 设置开机自动启动mysql

````bash
/etc/init.d/mysql start（手动启动）

# 加入到系统服务
chkconfig --add mysql   
# 自动启动
chkconfig mysql on 
````

````bash
# 检查开机是否启动的软件
ntsysv
````

````bash
# 修改配置文件拷贝（修改字符集）

cd /usr/share/mysql
cp my-huge.cnf /etc/my.cnf
vim my.cnf
````

`[client]`

````bash
default-character-set=utf8
````

`[mysqld]`

````bash
character_set_server=utf8
character_set_client=utf8
collation-server=utf8_general_ci
````

`[mysql]`

````bash
default-character-set=utf8
````

> 注：修改后如果之前已经创建了数据库则无效，需要重新创建数据库

### 11. 为远程连接授予权限

````bash
grant all privileges on *.* to 'root'@'%' identified by '密码';
# 权限需要刷新一下
flush privileges;  
````

### 12. 设置防火墙（linux默认会拦截3306端口）

````bash
/sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
/etc/rc.d/init.d/iptables save
````

### 13. 删除相关文件夹

````bash
find / -name mysql
rm -rf ...............
````

### 14. 初始化数据库

````bash
mysql_install_db
````

### 15. 查看mysql安装位置

````bash
ps -ef | grep mysql
````

| 路径              | 解释                      | 备注                             |
| ----------------- | ------------------------- | -------------------------------- |
| /var/lib/mysql/   | mysql数据库文件的存放路径 | /var/lib/mysql/atguigu.cloud.pid |
| /usr/share/mysql  | 配置文件目录              | mysql.server命令及配置文件       |
| /usr/bin          | 相关命令目录              | mysqladmin mysqldump命令         |
| /etc/init.d/mysql | 启停相关脚本              |                                  |

