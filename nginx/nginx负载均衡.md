# Nginx负载均衡

## 步骤

### 实现效果

> 访问192.168.68.131/8080/a.html分别在两个服务器上能够进行访问

### 1. 安装两个tomcat8080，tomcat8081
````bash
mkdir -p /usr/local/tomcat8080
mkdir -p /usr/local/tomcat8081
````

### 2. 分别在webapps下创建/8080/a.html

### 3. 在nginx的配置文件nginx.conf

````bash
http {
	.....
	upstream myserver(任意名称) {
		server 127.0.0.1:8080;
		server 127.0.0.1:8081;
	}
}
server {
	location / {
		proxy_pass http://myserver;
		root     html;
		index    index.html  index.htm;
	}
}
````

## nginx分配策略
### 1. 一、轮询（默认）
> 如果服务器down掉，会自动剔除

### 2. weight

````bash
# 代表权重，默认为1，权重越大被分配的客户端就越多
server 127.0.0.1:8080 weight=10；
````

### 3. ip_hash

````bash
# 第一次请求访问的ip固定以后都访问那个服务器
ip_hash;
````

### 4. fair

````bash
# 按后端的响应时间来分配请求，响应短的优先分配
fair；
````



