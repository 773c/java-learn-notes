# 解压版Nginx

## 安装步骤

### 1. 需要的依赖
````bash
pcre
openssl
zlib
````

### 2. 安装pcre依赖

````bash
/usr/src 解压文件
./configure 在根目录下执行 
make
make install   //或make && make install
pcre-config --version 安装之后查看版本号
````

### 3. 一键安装以上依赖

````bash
yum -y install make  zlib zlib-devel gcc-c++ libtool openssl openssl-devel
````

### 4. 安装nginx

````bash
/usr/src 解压文件
./configure 在根目录下执行 
make
make install （或make && make install）
安装后再/usr/local下会自动生成一个nginx文件夹
进入 /usr/local/nginx/sbin下
./nginx 启动
````

> 注：centos6可能会出现error while loading shared libraries: libpcre.so.1: cannot open shared object file: No such file or directory，因为centos6的/bin,/sbin,/lib,/lib64在根目录，而centos7都在/usr下
> ---- 说明在 /lib64下没有该文件，系统默认去/lib64下找。所以需要建立软连接。
>
> 在系统中查找对应库文件
> ---- sudo updatedb (更新数据库，一般与locate一起使用，基本是固定搭配)
> ---- locate -b '\libpcre.so.1'（该语句十分好用！可以找到指定文件所在绝对路径，当然是要系统中有这个文件啦）
> ---- find / -name libpcre.so.1（该语句也可以）
>
> 建立软连接（相当于windows的快捷方式）
> ---- ln -s /usr/local/lib/libpcre.so.1 /lib64/




