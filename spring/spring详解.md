#### IOC容器

>IOC又称之为`控制反转`，把创建对象的权利交给Spring。原本由我们自己控制的对象变成了由IOC容器来控制的对象，被称之为控制反转。

##### 一、依赖注入（DI）

> IOC为了动态的向某个对象提供它所需要的其他对象，其通过`依赖注入`DI（Dependency Injection）来实现的。
>
> 比如`对象A`需要操作数据库，以前我们总是要在`A`中自己编写代码来获得一个`Connection`对象，有了 `spring`我们就只需要告诉`spring`，`A`中需要一个`Connection`，至于这个`Connection`怎么构造，何时构造，`A`不需要知道。在系统运行时，`spring`会在适当的时候制造一个`Connection`，然后像打针一样，注射到`A`当中，这样就完成了对各个对象之间关系的控制。`A`需要依赖 `Connection`才能正常运行，而这个`Connection`是由`spring`注入到`A`中的，依赖注入的名字就这么来的。

##### （1）bean的三种创建方式

1. 默认构造函数

   > 在spring配置文件中使用bean标签，配置`id`和`class`属性后，没有配其他属性和标签时，采用的就是`默认无参构造函数`创建对象，如果没有默认构造函数，则无法创建。

   如下为spring配置文件配置好`UserServiceImpl`这个对象并创建

   ````xml
   <bean id="userService" class="com.cjj.web.service.impl.UserServiceImpl"></bean>
   ````

   如下为`UserServiceImpl`没有默认无参构造参数时

   ````java
   public class UserServiceImpl implements UserService {
       public UserServiceImpl(String s){
           System.out.println("对象被创建了");
       }
   }
   ````

   当没有默认无参构造函数时，会报以下错误

   ````java
   org.springframework.beans.factory.BeanCreationException: 
   Error creating bean with name 'userService' defined in class path resource [applicationContext.xml]
   ````

2. 普通工厂中的方法

   > 当要用到别人写的jar包中的类的方法的对象时，也不确定是否有默认构造函数，无法修改源码，所以通过类中的方法来进行创建对象

   如下为别人的类

   ````java
   public class InstanceFactory{
       public AccountService getAccountService(){
           return new AccountServiceImpl();
       }
   }
   ````

   如下为spring配置文件

   ````xml
   <bean id="instanceFactory" class="com.cjj....AccountServiceImpl"></bean>
   <bean id="accountService" factory-bean="instanceFactory" 
         factory-method="getAccountService" ></bean>
   ````

3. 普通工厂中的静态方法

   如下为spring配置文件

   ````xml
   <bean id="accountService" class="com.cjj....AccountServiceImpl" 
         factory-method="getAccountService"></bean>
   ````

##### （2）依赖注入

> 在当前类需要用到其他类的对象时，由spring为我们提供，我们只需要在配置文件中或注解说明

##### （2-1）能注入的三种数据

1. `基本类型`和`String`
2. 其他`bean`类型（在`配置文件`中或者`注解扫描`的bean）
3. 复杂类型/`集合`类型

##### （2-2）注入的三种方式

1. Setter方法注入

   如下为`UserServiceImpl`类

   ````java
   public class UserServiceImpl implements UserService {
       private UserDao userDao;
   
       public void setUserDao(UserDao userDao) {
           this.userDao = userDao;
       }
       public void print(){
           userDao.dao();
       }
   }
   ````

   如下为配置文件

   ````xml
   <bean id="userDao" class="com.cjj.web.dao.impl.UserDaoImpl"/>
   <bean id="userService" class="com.cjj.web.service.impl.UserServiceImpl">
       <property name="userDao" ref="userDao"/>
   </bean>
   ````

2. 构造函数注入

   如下为`UserServiceImpl`类

   ````java
   public class UserServiceImpl implements UserService {
       private String name;
       private int age;
   
       public UserServiceImpl(String name,int age){
           this.name = name;
           this.age = age;
       }
   
       public void print(){
           System.out.println("姓名："+name+"..年龄："+age);
       }
   }
   ````

   如下为配置文件

   ````xml
   <bean id="userService" class="com.cjj.web.service.impl.UserServiceImpl">
       <constructor-arg name="name" value="cjj"/>
       <constructor-arg name="age" value="23"/>
   </bean>
   ````

3. 自动装配（`配置文件`或注解`@Autowired`）

   3.1  自动装配的方式

   * **no**：不进行自动装配
   * **byName**：根据Bean的id名字进行自动装配（底层是用setter注入的）
   * **byType**：根据Bean的类型进行自动装配（底层是用setter注入的）
   * **contructor**：类似于byType，与构造函数中的形参的类型相同则进行自动装配

##### 以配置文件为例子：

如下为spring配置文件

````xml
<bean id="userDao" class="com.cjj.web.dao.impl.UserDaoImpl"></bean>
<bean id="userService" class="com.cjj.web.service.impl.UserServiceImpl" autowire="constructor"></bean>
````

如下为`UserServiceImpl`类

````java
public class UserServiceImpl implements UserService {
    private UserDao userDao;

    public UserServiceImpl(UserDao userDao){
        this.userDao = userDao;
    }
    public void print(){
        userDao.dao();
    }
}
````

##### 以注解`@Autowired`为例子：

> 默认是以类型（byType）的方式来进行自动装配的

如下为spring配置文件

````xml
<context:component-scan base-package="com.cjj.web"/>	<!-- 扫描所有 -->
<context:annotation-config />	<!-- 这个是用来开启@Autowired等自动装配的，但是用了
context:component-scan就不需要配了 -->
````

如下为`UserServiceImpl`类

````java
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserDao userDao;

    public void print(){
        System.out.println(userDao);
        userDao.dao();
    }
}
````

如下为`UserDaoImpl`类

````java
@Repository
public class UserDaoImpl implements UserDao {
    @Override
    public void dao() {
        System.out.println("我是dao");
    }
}
````

