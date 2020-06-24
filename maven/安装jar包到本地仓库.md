# ��װ������jar�������زֿ�

## ��װ����

### 1. ����jar������Ŀ¼����

````bash
mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dfile=fastjson-1.1.37.jar -Dpackaging=jar
````

**���ߴ�cmdֱ������**

````bash
mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=jar������·��
````

### 2. ��װ������jar����˽��

#### 2.1 ��settings�����ļ�����ӵ�¼˽����������¼��Ϣ��

````xml
<server>
	<id>thirdparty</id>
	<username>admin</username>
	<password>admin123</password>
</server>
````

#### 2.2 ����jar������Ŀ¼����

````bash
mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=fastjson-1.1.37.jar -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty
````

**���ߴ�cmdֱ������**

````bash
mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=jar������·�� -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty
````