##### 一、引入依赖

````java
  <!--集成druid连接池-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.21</version>
            </dependency>
            <!--MyBatis分页插件starter-->（这个依赖包含了spring-jdbc，mybatis-spring-boot-start,pagehelper）
            <dependency>
                <groupId>com.github.pagehelper</groupId>
                <artifactId>pagehelper-spring-boot-starter</artifactId>
                <version>${pagehelper-spring-boot-starter.version}</version>
            </dependency>
            <!--mysql驱动-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector-java.version}</version>
            </dependency>     
````

##### 二、在springboot的启动类上增加一个注解：
> ---- @EnableAutoConfiguration(exclude={DruidDataSourceAutoConfigure.class})