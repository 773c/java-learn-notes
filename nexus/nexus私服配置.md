# Nexus私服配置

## 步骤

### 1. 将nexus安装包解压后会出现一个nexus-2.12.0-01安装包和sonatype-work私服

````bash
打开管理员DOS命令，cd到nexus的bin目录下。
输入 nexus.bat -install
再输入 nexus.bat start
````

### 2. 输入localhost:8081/nexus即可进入操作页面(默认账号admin，密码admin123)

### 3. 上传jar包nexus私服

**配置maven中的settings.xml文件**

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

**配置pom.xml**

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

    # 注意：settings的id和pom的id要一样。

### 4. 从私服下载jar包

````xml
<profile>
    <id>dev</id><!--profile的id -->
    <repositories>
    	<repository><!--仓库id，repositories可以配置多个仓库，保证id不重复 -->
	    	<id>nexus</id><!--仓库地址，即nexus仓库组的地址 -->
	    	<url>http://localhost:8081/nexus/content/groups/public/</url>
	    	<releases>
	        	<enabled>true</enabled>
	    	</releases>
	    	<snapshots>
	        	<enabled>true</enabled>
	    	</snapshots>
    	</repository>
    </repositories>
    <pluginRepositories><!-- 插件仓库，maven的运行依赖插件，也需要从私服下载插件 -->
	    <pluginRepository><!-- 插件仓库的id不允许重复，如果重复后面的配置会覆盖前面 -->
	        <id>public</id>
	        <name>Public Repositories</name>
	        <url>http://localhost:8081/nexus/content/groups/public/</url>
	    </pluginRepository>
    </pluginRepositories>
</profile>
# 然后进行激活
<activeProfiles>
    <activeProfile>dev</activeProfile><!--与上面id一致 -->
</activeProfiles>
````

