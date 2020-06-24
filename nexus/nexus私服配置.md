# Nexus˽������

## ����

### 1. ��nexus��װ����ѹ������һ��nexus-2.12.0-01��װ����sonatype-work˽��

````bash
�򿪹���ԱDOS���cd��nexus��binĿ¼�¡�
���� nexus.bat -install
������ nexus.bat start
````

### 2. ����localhost:8081/nexus���ɽ������ҳ��(Ĭ���˺�admin������admin123)

### 3. �ϴ�jar��nexus˽��

**����maven�е�settings.xml�ļ�**

````xml
<server>
	<id>releases</id>
		<username>admin</username>    	
        <password>admin123</password>
</server>
<server>
	<id>snapshots</id>
	<username>admin</username>    	
	<password>admin123</password>
</server>
````

**����pom.xml**

````xml
<distributionManagement>
	<repository>
		<id>releases</id>
		<url>http://localhost:8081/nexus/content/repositories/releases/</url>
    </repository>
    <snapshotRepository>
		<id>snapshots</id>
		<url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
    </snapshotRepository>
</distributionManagement>
````

    # ע�⣺settings��id��pom��idҪһ����

### 4. ��˽������jar��

````xml
<profile>
    <id>dev</id><!--profile��id -->
    <repositories>
    	<repository><!--�ֿ�id��repositories�������ö���ֿ⣬��֤id���ظ� -->
	    	<id>nexus</id><!--�ֿ��ַ����nexus�ֿ���ĵ�ַ -->
	    	<url>http://localhost:8081/nexus/content/groups/public/</url>
	    	<releases>
	        	<enabled>true</enabled>
	    	</releases>
	    	<snapshots>
	        	<enabled>true</enabled>
	    	</snapshots>
    	</repository>
    </repositories>
    <pluginRepositories><!-- ����ֿ⣬maven���������������Ҳ��Ҫ��˽�����ز�� -->
	    <pluginRepository><!-- ����ֿ��id�������ظ�������ظ���������ûḲ��ǰ�� -->
	        <id>public</id>
	        <name>Public Repositories</name>
	        <url>http://localhost:8081/nexus/content/groups/public/</url>
	    </pluginRepository>
    </pluginRepositories>
</profile>
# Ȼ����м���
<activeProfiles>
    <activeProfile>dev</activeProfile><!--������idһ�� -->
</activeProfiles>
````

