# Nexus��������

## ����

### 1. ��nexus manager�����Proxy Repository��

### 2. ��дID��Name��Щ����㣬�ؼ����⣺

````bash
Download Remote Indexes ����ΪFalse��
Auto Blocking Enabled ����ΪFalse��
````

### 3. ��ַ��

http://maven.aliyun.com/nexus/content/groups/public/ 

�� 

https://maven.aliyun.com/repository/public

### 4. ���Ҫ�鿴Զ�̰�����repoĿ¼������Ҫ��maven��settings�����ã�

````xml
<mirror>
	<id>nexus</id>
	<mirrorOf>central</mirrorOf>
	<name>my nexus</name
	<url>http://localhost:8081/nexus/content/groups/public/</url>
</mirror>
````

