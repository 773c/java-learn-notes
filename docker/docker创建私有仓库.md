# Docker����˽�вֿ�

## 1. ��������

### 1.1 ��ȡ����

````bash
docker pull registry
````

### 1.2 ��������

```bash
docker run -id --name=registry1 -p 5000:5000 ��������:TAG
```

### 1.3 ����˽�вֿ�

````bash
ip��ַ:�˿ں�/v2/_catalog	
# {"repositories":[]} ��ʾ��װ�ɹ�
````

### 1.4 �޸�daemon.json

````bash
vi /etc/docker/daemon.json
````

### 1.5 �����ϴ���˽�вֿ�
#### ��1����Ǵ˾���Ϊ˽�вֿ�ľ���
````bash
docker tag ��������:TAG ip��ַ:�˿ں� /��������
````

####  ��2�� �ϴ���ǵľ���
````bash
docker push ip��ַ:�˿ں�/��������
````

