# Docker部署

## mysql

### 1. 拉取mysql镜像

````bash
docker pull mysql:5.7
````

### 2. 创建容器

````bash
docker run -id --name=容器名称 -p 宿主机端口号:容器端口号 -e MYSQL_ROOT_PASSWORD=root的登录密码 镜像名称:TAG

-e表示添加环境变量 MYSQL_ROOT_PASSWORD 是root用户的登录密码
````

## tomcat

### 1. 拉取tomcat镜像

````bash
docker pull tomcat:8.5-jre8
````

### 2. 创建容器

````bash
docker run -id --name=容器名称 -p 宿主机端口号:容器端口号 -v /usr/local/webapps:/usr/local/tomcat/webapps 镜像名称:TAG
````

## nginx

### 1. 拉取nginx镜像

````bash
docker pull nginx:1.14.2
````

### 2. 创建容器

````bash
docker run -id --name=容器名称 -p 80:80 镜像名称:TAG
````

`nginx.conf配置文件`
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

```bash
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';

access_log  /var/log/nginx/access.log  main;

sendfile        on;
#tcp_nopush     on;

keepalive_timeout  65;

#gzip  on;

include /etc/nginx/conf.d/*.conf;
```
}

`default.conf配置文件`
server {
    listen       80;
    server_name  localhost;

```bash
#charset koi8-r;
#access_log  /var/log/nginx/host.access.log  main;

location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
}

#error_page  404              /404.html;

# redirect server error pages to the static page /50x.html
#
error_page   500 502 503 504  /50x.html;
location = /50x.html {
    root   /usr/share/nginx/html;
}

# proxy the PHP scripts to Apache listening on 127.0.0.1:80
#
#location ~ \.php$ {
#    proxy_pass   http://127.0.0.1;
#}

# pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
#
#location ~ \.php$ {
#    root           html;
#    fastcgi_pass   127.0.0.1:9000;
#    fastcgi_index  index.php;
#    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
#    include        fastcgi_params;
#}

# deny access to .htaccess files, if Apache's document root
# concurs with nginx's one
#
#location ~ /\.ht {
#    deny  all;
#}
```
}

## redis

### 1. 拉取redis镜像

````bash
docker pull redis:3.2
````

### 2. 创建容器

````bash
docker run -id --name=容器名称 -p 6379:6379 镜像名称:TAG
````

### 3. 连接redis

````bash
dos命令到redis目录下
redis-cli -h ip地址 
````