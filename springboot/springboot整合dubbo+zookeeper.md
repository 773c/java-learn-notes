##### 一、相关依赖

````java
springbot为2.2.5版本，其他未测试过。
<!--dubbo-->
<dependency>
	<groupId>org.apache.dubbo</groupId>
	<artifactId>dubbo-spring-boot-starter</artifactId>
	<version>2.7.3</version>
</dependency>
    
<!--zookeeperClient-->
<dependency>
	<groupId>com.github.sgroschupf</groupId>
	<artifactId>zkclient</artifactId>
	<version>0.1</version>
</dependency>
    
<!--zookeeper-->
<dependency>
	<groupId>org.apache.curator</groupId>
	<artifactId>curator-framework</artifactId>
	<version>2.12.0</version>
</dependency>
<dependency>
	<groupId>org.apache.curator</groupId>
	<artifactId>curator-recipes</artifactId>
	<version>2.12.0</version>
</dependency>
<dependency>
	<groupId>org.apache.zookeeper</groupId>
	<artifactId>zookeeper</artifactId>
	<version>3.4.14</version>
	<!--日志会冲突-->
	<exclusions>
		<exclusion>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
		</exclusion>
	</exclusions>
</dependency>
````

##### 二、服务器Provider-Server

##### （1）配置文件

````properties
server.port = 8001
# 服务应用名字 自定义
dubbo.application.name=provider-server
# 注册中心地址
dubbo.registry.address=zookeeper://127.0.0.1:2181
# 哪些服务要被注册
dubbo.scan.base-packages=com.cjj.service
````

##### （2）业务代码

````java
import com.cjj.service.TicketService;
import org.apache.dubbo.config.annotation.Service;
import org.springframework.stereotype.Component;

@Service	//dubbo的Service，不是Spring的
@Component
public class TicketServiceImpl implements TicketService {
    @Override
    public String getTicket() {
        return "666666666666666";
    }
}
````

##### （3）登录dubbo-admin看是否已被注册

![image-20200622144342299](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200622144342299.png)

##### 三、服务器Consumer-Server

##### （1）配置文件

````properties
server.port = 8002
# 服务器名称
dubbo.application.name=consumer-server
# 注册中心地址
dubbo.registry.address=zookeeper://127.0.0.1:2181
````

##### （2）获取远程注册中心的服务

**方法一：**

* 创建一个和提供方一样的一个接口（不用实现类）

````java
import com.cjj.service.TicketService;
import com.cjj.service.UserService;
import org.apache.dubbo.config.annotation.Reference;
import org.springframework.stereotype.Service;

@Service
public class UserServiceImpl implements UserService {
    @Reference
    TicketService ticketService;
    public void get() {
        String ticket = ticketService.getTicket();
        System.out.println(ticket);
    }
}
````

