# Nginx���ؾ���

## ����

### ʵ��Ч��

> ����192.168.68.131/8080/a.html�ֱ����������������ܹ����з���

### 1. ��װ����tomcat8080��tomcat8081
````bash
mkdir -p /usr/local/tomcat8080
mkdir -p /usr/local/tomcat8081
````

### 2. �ֱ���webapps�´���/8080/a.html

### 3. ��nginx�������ļ�nginx.conf

````bash
http {
	.....
	upstream myserver(��������) {
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

## nginx�������
### 1. һ����ѯ��Ĭ�ϣ�
> ���������down�������Զ��޳�

### 2. weight

````bash
# ����Ȩ�أ�Ĭ��Ϊ1��Ȩ��Խ�󱻷���Ŀͻ��˾�Խ��
server 127.0.0.1:8080 weight=10��
````

### 3. ip_hash

````bash
# ��һ��������ʵ�ip�̶��Ժ󶼷����Ǹ�������
ip_hash;
````

### 4. fair

````bash
# ����˵���Ӧʱ��������������Ӧ�̵����ȷ���
fair��
````



