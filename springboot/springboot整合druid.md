##### һ����������

````java
  <!--����druid���ӳ�-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.21</version>
            </dependency>
            <!--MyBatis��ҳ���starter-->���������������spring-jdbc��mybatis-spring-boot-start,pagehelper��
            <dependency>
                <groupId>com.github.pagehelper</groupId>
                <artifactId>pagehelper-spring-boot-starter</artifactId>
                <version>${pagehelper-spring-boot-starter.version}</version>
            </dependency>
            <!--mysql����-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector-java.version}</version>
            </dependency>     
````

##### ������springboot��������������һ��ע�⣺
> ---- @EnableAutoConfiguration(exclude={DruidDataSourceAutoConfigure.class})