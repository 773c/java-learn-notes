# Nginx��������

## ����

### 1. �ڸ�Ŀ¼�����ļ���
````bash
mkdir -p /data/www
mkdir -p /data/images
````

### 2. ����nginx�������ļ�nginx.conf

````bash
location /www/ {
	root   /data/;
	index  index.html index.htm;
}

location /image/ {
	root   /data/;
	autoindex on;  //�ܹ���ʾĿ¼�ṹ
}

expires=3d������3�죩
````

