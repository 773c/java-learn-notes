# ��װ��MySQL

## ��װ����

### 1. �鿴Linux�����Ƿ��Ѿ���װ�����ھ�ж�أ�

````bash
rpm -qa | grep -i mysql
````

### 2. �ϴ�client��server��rpm��Linux

### 3. ��װmysql�ķ����

````bash
rpm -ivh server��װ��
````

### 4. �����װ����libc.so.6(GLIBC_2.14)(64bit) is needed by....

````bash
cd /usr/local
# ������Ӧ��װ��
wget http://ftp.gnu.org/gnu/glibc/glibc-2.14.tar.gz 
��
# ����ǹ��ھ��񣬸���
wget http://mirror.bjtu.edu.cn/gnu/libc/glibc-2.14.tar.gz

tar xvf glibc-2.14.tar.gz

cd glibc-2.14

mkdir build

cd build

# ����glibc�����õ�ǰglibc-2.14��װĿ¼
../configure --prefix=/usr/local/glibc-2.14  

make

make install

cp /usr/local/glibc-2.14/lib/libc-2.14.so /lib64/libc-2.14.so 

mv /lib64/libc.so.6 /lib64/libc.so.6.bak

LD_PRELOAD=/lib64/libc-2.14.so ln -s /lib64/libc-2.14.so /lib64/libc.so.6
````

> ע��������һ������ִ�г�����ͨ��
> LD_PRELOAD=/lib64/libc-2.12.so ln -s /lib64/libc-2.12.so /lib64/libc.so.6 �ٸĻ�ȥ
> ���ִ��strings /lib64/libc.so.6 | grep GLIBC���鿴glibc�Ƿ����
>
> ���ϲ��� �ͻ�mysql5.6

### 5. ��װservice��װ��

````bash
rpm -ivh service��װ����
````

### 6. ��װclient��װ��

````bash
rpm -ivh client��װ����
````

### 7. ����mysql����

````bash
service mysql start
````

### 8. �е�mysql�汾û��Ĭ�����룬�еĻ��������һ�����룬��װservice��װ��ʱ����ʾ��

````bash
# �鿴��ʼ����
cat /root/.mysql_secret   
````

### 9. �޸����루���ַ�ʽ��

````bash
/usr/bin/mysqladmin -u root password 'new-password'��Ĭ����ʾ�޸ķ�ʽ��

set password for '�û���'@'������' = password('������');
````

### 10. ���ÿ����Զ�����mysql

````bash
/etc/init.d/mysql start���ֶ�������

# ���뵽ϵͳ����
chkconfig --add mysql   
# �Զ�����
chkconfig mysql on 
````

````bash
# ��鿪���Ƿ����������
ntsysv
````

````bash
# �޸������ļ��������޸��ַ�����

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

> ע���޸ĺ����֮ǰ�Ѿ����������ݿ�����Ч����Ҫ���´������ݿ�

### 11. ΪԶ����������Ȩ��

````bash
grant all privileges on *.* to 'root'@'%' identified by '����';
# Ȩ����Ҫˢ��һ��
flush privileges;  
````

### 12. ���÷���ǽ��linuxĬ�ϻ�����3306�˿ڣ�

````bash
/sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
/etc/rc.d/init.d/iptables save
````

### 13. ɾ������ļ���

````bash
find / -name mysql
rm -rf ...............
````

### 14. ��ʼ�����ݿ�

````bash
mysql_install_db
````

### 15. �鿴mysql��װλ��

````bash
ps -ef | grep mysql
````

| ·��              | ����                      | ��ע                             |
| ----------------- | ------------------------- | -------------------------------- |
| /var/lib/mysql/   | mysql���ݿ��ļ��Ĵ��·�� | /var/lib/mysql/atguigu.cloud.pid |
| /usr/share/mysql  | �����ļ�Ŀ¼              | mysql.server��������ļ�       |
| /usr/bin          | �������Ŀ¼              | mysqladmin mysqldump����         |
| /etc/init.d/mysql | ��ͣ��ؽű�              |                                  |

