# ��ѹ��MySQL

## ��װ����

### 1. ��mysqlѹ������ѹ�� /usr/local �ļ����£��������Ϊmysql
**��ѹ��**

````bash
tar -zxvf mysql-5.7.27-el7-x86_64.tar.gz -C /usr/local/
````

**��������**

````bash
cd /usr/local/
mv mysql-5.7.27-el7-x86_64/ mysql
````

### 2. ����mysql������5.7û��dataĿ¼���Լ�����һ��

````bash
cd mysql/
mkdir -p data
````

### 3. ����mysql�û����û���

````bash
groupadd mysql
useradd -r -s /sbin/nologin -g mysql mysql -d /usr/local/mysql/
#useradd -r������ʾmysql�û���ϵͳ�û����������ڵ�¼ϵͳ
````

**�ο���ַ��**https://www.cnblogs.com/xiongnanbin/p/11834979.html