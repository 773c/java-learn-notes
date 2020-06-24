# Nginx动静分离

## 步骤

### 1. 在根目录创建文件夹
````bash
mkdir -p /data/www
mkdir -p /data/images
````

### 2. 配置nginx的配置文件nginx.conf

````bash
location /www/ {
	root   /data/;
	index  index.html index.htm;
}

location /image/ {
	root   /data/;
	autoindex on;  //能够显示目录结构
}

expires=3d（缓存3天）
````

