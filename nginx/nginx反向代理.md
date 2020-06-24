# Nginx反向代理

## 步骤

### 1. 修改映射访问域名
````bash
windows下的hosts文件最后加上 访问ip地址 www.cjjtest.com
````

### 2. 修改nginx的配置文件nginx.conf

````bash
将server中的server_name localhost修改成server_name 访问ip地址
在location中加入proxy_pass http://127.0.0.1:8080;
````

### 3. 如果访问的是192.168.68.131/8080/则跳转到8080端口的tomcat服务器
       如果访问的是192.168.68.131/8081/则跳转到8081端口的tomcat服务器

````bash
# 解压两个tomcat，修改里面server.xml的所有端口号
# 在nginx的配置文件nginx.conf中添加以下内容：
location ~ /8080/ {
	proxy_pass http://127.0.0.1:8080;
}
location ~ /8081/ {
	proxy_pass http://127.0.0.1:8081;
}
# 重启nginx即可
````

