# Docker�������

## ��������

### 1. �鿴�����ؾ���
````bash
docker images
````

### 2. ��������
````bash
docker search ����
````

### 3. ��ȡ����
````bash
docker pull ����
````

### 4. ɾ������
````bash
docker rmi ���ƻ�ID
docker rmi `docker images -q`��ɾ�����о���
````

## ��������
### 1. �鿴����
````bash
docker ps �������У�
docker ps -a���鿴���У�
````

### 2. ��������
````bash
docker run -it --name=mycentos centos:7 /bin/bash������ʽ��
#����ʽ���Զ���������������
#exit�˳����������Զ�ֹͣ����

docker run -id --name=mycentos2 centos:7���ػ�ʽ��
#�ػ�ʽ����ʱ�����һ���ַ����������Զ���������������
#���뷽ʽΪ docker exec -it  mycentos2 /bin/bash
#exit�˳������������Զ�ֹͣ����

#-i��ʾ��������
#-t��ʾ��������������
#-d��ʾ�����ػ�ʽ�����������Զ����������У� �˳������Զ�ֹͣ����
#--name��ʾΪ��������������
#-v��ʾĿ¼ӳ���ϵ
#-p��ʾ�˿�ӳ��
````

### 3. ��������
````bash
docker start �������ƻ�ID
````

### 4. ֹͣ����
````bash
docker stop �������ƻ�ID
````

### 5. �ļ�����
````bash
docker cp ��Ҫ�������ļ���Ŀ¼ �������ƣ�����Ŀ¼�����������У�
docker cp �������ƣ�����Ŀ¼ ��Ҫ�������ļ���Ŀ¼���������п�������
````

### 6. Ŀ¼���أ�Ŀ¼ӳ�䣩
````bash
docker run -id --name=mycentos3 -v ������Ŀ¼:����Ŀ¼ �������ƣ�TAG�������latest�Ͳ���д��
#�������Ȩ�޲������ʾ��Ҫ��Ӳ��� --privileged=true
````

### 7. �鿴������Ϣ
````bash
docker inspect �������ƻ�ID���鿴���������Ϣ��
docker inspect --format='{{.NetworkSettings.IPAddress}}' �������ƻ�ID���鿴IP��
````

### 8. ɾ������
````bash
docker rm �������ƻ�ID������������ֹͣ���е�״̬�£�
````




