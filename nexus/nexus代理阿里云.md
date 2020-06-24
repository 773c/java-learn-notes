# Nexus代理阿里云

## 步骤

### 1. 在nexus manager中添加Proxy Repository。

### 2. 填写ID、Name这些都随便，关键在这：

````bash
Download Remote Indexes 设置为False。
Auto Blocking Enabled 设置为False。
````

### 3. 地址：

http://maven.aliyun.com/nexus/content/groups/public/ 

或 

https://maven.aliyun.com/repository/public

### 4. 如果要查看远程阿里云repo目录报错，需要在maven的settings中设置：

````xml
<mirror>
	<id>nexus</id>
	<mirrorOf>central</mirrorOf>
	<name>my nexus</name
	<url>http://localhost:8081/nexus/content/groups/public/</url>
</mirror>
````

