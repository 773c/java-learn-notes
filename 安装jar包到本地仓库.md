##### ��װ������jar�������زֿ�

����jar������Ŀ¼����

>mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dfile=fastjson-1.1.37.jar -Dpackaging=jar

���ߴ�cmdֱ������

> mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=jar������·��

##### ��װ������jar����˽��

��settings�����ļ�����ӵ�¼˽����������¼��Ϣ

````
     <server>
          <id>thirdparty</id>
          <username>admin</username>
          <password>admin123</password>
     </server>
````

����jar������Ŀ¼����

>mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=fastjson-1.1.37.jar -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty

���ߴ�cmdֱ������

> mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dpackaging=jar -Dfile=jar������·�� -Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ -DrepositoryId=thirdparty

