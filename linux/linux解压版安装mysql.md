# 解压版MySQL

## 安装步骤

### 1. 将mysql压缩包解压至 /usr/local 文件夹下，将其改名为mysql
**解压：**

````bash
tar -zxvf mysql-5.7.27-el7-x86_64.tar.gz -C /usr/local/
````

**重命名：**

````bash
cd /usr/local/
mv mysql-5.7.27-el7-x86_64/ mysql
````

### 2. 进入mysql，由于5.7没有data目录，自己创建一个

````bash
cd mysql/
mkdir -p data
````

### 3. 创建mysql用户和用户组

````bash
groupadd mysql
useradd -r -s /sbin/nologin -g mysql mysql -d /usr/local/mysql/
#useradd -r参数表示mysql用户是系统用户，不可用于登录系统
````

**参考网址：**https://www.cnblogs.com/xiongnanbin/p/11834979.html