# Nginx�������

## ����

### 1. �޸�ӳ���������
````bash
windows�µ�hosts�ļ������� ����ip��ַ www.cjjtest.com
````

### 2. �޸�nginx�������ļ�nginx.conf

````bash
��server�е�server_name localhost�޸ĳ�server_name ����ip��ַ
��location�м���proxy_pass http://127.0.0.1:8080;
````

### 3. ������ʵ���192.168.68.131/8080/����ת��8080�˿ڵ�tomcat������
       ������ʵ���192.168.68.131/8081/����ת��8081�˿ڵ�tomcat������

````bash
# ��ѹ����tomcat���޸�����server.xml�����ж˿ں�
# ��nginx�������ļ�nginx.conf������������ݣ�
location ~ /8080/ {
	proxy_pass http://127.0.0.1:8080;
}
location ~ /8081/ {
	proxy_pass http://127.0.0.1:8081;
}
# ����nginx����
````

