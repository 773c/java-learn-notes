#### 相关依赖：

````java
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
        </dependency>
            或
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring-boot-web-starter</artifactId>
        </dependency>
````

##### 一、Shiro三大核心

* Subject：用户主体（把操作交给SecurityManager）
* SecurityManager：安全管理器（关联Realm）
* Realm：身份验证器（连接数据的桥梁）

##### （1）UserRealm

````java
import com.cjj.qlmusic.security.entity.UmsAdmin;
import com.cjj.qlmusic.security.service.UmsAdminService;
import com.cjj.qlmusic.security.util.SimpleByteSourceUtil;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;

/**
 * 用于用户登录时的校验
 */
public class UserRealm extends AuthorizingRealm {
    @Autowired
    private UmsAdminService umsAdminService;

    /**
     * 授权
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ UserRealm（授权中）");
        //获取账号
        String account = (String) principalCollection.getPrimaryPrincipal();
        SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
        System.out.println(umsAdminService.getRoles(account));
        System.out.println(umsAdminService.getPermissions(account));
        authorizationInfo.setRoles(umsAdminService.getRoles(account));
        authorizationInfo.setStringPermissions(umsAdminService.getPermissions(account));
        return authorizationInfo;
    }

    /**
     * 认证
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ UserRealm（认证中）");
        String account = (String) authenticationToken.getPrincipal();
        //从数据库获取管理员用户信息
        UmsAdmin umsAdmin = umsAdminService.findByAccount(account);
        //判断是否存在
        if(umsAdmin == null){
            throw new UnknownAccountException();
        }
        System.out.println("用户："+umsAdmin.getName()+"，密码："+umsAdmin.getPassword());
        SimpleAuthenticationInfo authenticationInfo = new SimpleAuthenticationInfo(
                umsAdmin.getAccount(),
                umsAdmin.getPassword(),
                new SimpleByteSourceUtil(umsAdmin.getSalt().getBytes()),
                getName()
        );
        return authenticationInfo;
    }
}
````

doGetAuthenticationInfo获取身份验证相关信息：

> 首先根据传入的用户名获取User信息；
>
> 然后如果user为空，那么抛出没找到帐号异常UnknownAccountException；
>
> 如果user找到但锁定了抛出锁定异常LockedAccountException；
>
> 最后生成AuthenticationInfo信息，交给间接父类AuthenticatingRealm使用CredentialsMatcher进行判断密码是否匹配，如果不匹配将抛出密码错误异常IncorrectCredentialsException；
>
> 另外如果密码重试此处太多将抛出超出重试次数异常ExcessiveAttemptsException；
>
> 在组装SimpleAuthenticationInfo信息时，需要传入：身份信息（用户名）、凭据（密文密码）、盐（username+salt），CredentialsMatcher使用盐加密传入的明文密码和此处的密文密码进行匹配。

doGetAuthorizationInfo获取授权信息:

> PrincipalCollection是一个身份集合，因为我们现在就一个Realm，所以直接调用getPrimaryPrincipal得到之前传入的用户名即可；
>
> 然后根据用户名调用UserService接口获取角色及权限信息。

##### （2）身份认证流程

![img](http://dl2.iteye.com/upload/attachment/0094/0173/8d639160-cd3e-3b9c-8dd6-c7f9221827a5.png)

流程如下：

* 首先调用Subject.login(token)进行登录，其会自动委托给Security Manager，调用之前必须通过SecurityUtils. setSecurityManager()设置（JAVASE）；
* SecurityManager负责真正的身份验证逻辑；它会委托给Authenticator进行身份验证；
* `Authenticator`才是真正的身份验证者，Shiro API中核心的身份认证入口点，此处可以自定义插入自己的实现；
* Authenticator可能会委托给相应的AuthenticationStrategy进行多Realm身份验证，默认`ModularRealmAuthenticator`会调用AuthenticationStrategy进行多Realm身份验证；
* Authenticator会把相应的token传入Realm，从Realm获取身份验证信息，如果没有返回或者抛出异常表示身份验证失败了。此处可以配置多个Realm，将按照相应的顺序及策略进行访问。

##### （2-1）Authenticator及AuthenticationStrategy

`Authenticator`的职责是验证用户帐号，是Shiro API中身份验证核心的入口点;

如果验证成功，将返回AuthenticationInfo验证信息；此信息中包含了身份及凭证；如果验证失败将抛出相应的AuthenticationException。

SecurityManager接口继承了Authenticator，另外还有一个`ModularRealmAuthenticator`实现，其委托给多个Realm进行验证，验证规则通过AuthenticationStrategy接口指定，默认提供的实现：

* **FirstSuccessfulStrategy**：只要有一个Realm验证成功即可，只返回第一个Realm身份验证成功的认证信息，其他的忽略；

* **AtLeastOneSuccessfulStrategy**：只要有一个Realm验证成功即可，和FirstSuccessfulStrategy不同，返回所有Realm身份验证成功的认证信息；

* **AllSuccessfulStrategy**：所有Realm验证成功才算成功，且返回所有Realm身份验证成功的认证信息，如果有一个失败就失败了。

`ModularRealmAuthenticator默认使用AtLeastOneSuccessfulStrategy策略。`

PS：（`对于两个Realm认证相同的用户才需要使用以上的逻辑`）

##例子：

假设我们有三个realm：

myRealm1： 用户名/密码为zhang/123时成功，且返回身份/凭据为zhang/123；

myRealm2： 用户名/密码为wang/123时成功，且返回身份/凭据为wang/123；

myRealm3： 用户名/密码为zhang/123时成功，且返回身份/凭据为zhang@163.com/123，和myRealm1不同的是返回时的身份变了；

登录代码：

````java
private void login(String configFile) {  
    //得到Subject及创建用户名/密码身份验证Token（即用户身份/凭证）  
    Subject subject = SecurityUtils.getSubject();  
    UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");  
  
    subject.login(token);  
}  
````

测试：

````java
@Test  
public void testAllSuccessfulStrategyWithSuccess() {  
    Subject subject = SecurityUtils.getSubject();  
  
    //得到一个身份集合，其包含了Realm验证成功的身份信息  
    PrincipalCollection principalCollection = subject.getPrincipals();  
    System.out.println(principalCollection.getRealmNames())  
}   
````

结果包含了zhang和zhang@163.com身份信息。

##### （2-2）AuthticationToken

![img](http://dl2.iteye.com/upload/attachment/0094/1333/6c026012-2583-3a26-af70-bb1b0eae491b.png)

AuthenticationToken用于收集用户提交的身份（如用户名）及凭据（如密码）：

````java
public interface AuthenticationToken extends Serializable {  
    Object getPrincipal(); //身份  
    Object getCredentials(); //凭据  
}   
````

扩展接口RememberMeAuthenticationToken：提供了“boolean isRememberMe()”现“`记住我`”的功能；

扩展接口是HostAuthenticationToken：提供了“String getHost()”方法用于获取用户“`主机`”的功能。

Shiro提供了一个直接拿来用的UsernamePasswordToken，用于实现用户名/密码Token组，另外其实现了RememberMeAuthenticationToken和HostAuthenticationToken，可以实现记住我及主机验证的支持。

##### （2-3）AuthenticationInfo

![img](http://dl2.iteye.com/upload/attachment/0094/1337/0be597af-cd61-34ce-b1f0-8ebd15dbdeb9.png)

`AuthenticationInfo`有两个作用：

> 如果Realm是AuthenticatingRealm子类，则提供给AuthenticatingRealm内部使用的CredentialsMatcher进行凭据验证；（如果没有继承它需要在自己的Realm中自己实现验证）；
>
> 提供给SecurityManager来创建Subject（提供身份信息）；

`MergableAuthenticationInfo`用于提供在多Realm时合并AuthenticationInfo的功能，主要合并Principal、如果是其他的如credentialsSalt，会用后边的信息覆盖前边的。

比如`HashedCredentialsMatcher`，在验证时会判断AuthenticationInfo是否是`SaltedAuthenticationInfo`子类，来获取盐信息。

`Account`相当于我们之前的User，SimpleAccount是其一个实现；在IniRealm、PropertiesRealm这种静态创建帐号信息的场景中使用，这些Realm直接继承了SimpleAccountRealm，而SimpleAccountRealm提供了相关的API来动态维护SimpleAccount；即可以通过这些API来动态增删改查SimpleAccount；动态增删改查角色/权限信息。及如果您的帐号不是特别多，可以使用这种方式，具体请参考SimpleAccountRealm Javadoc。

##### （2-4）PrincipalCollection

![img](http://dl2.iteye.com/upload/attachment/0094/1341/772c988b-e930-31d1-ad14-c9ae8f476f66.png)

PrincipalCollection用于聚合这些身份信息：

````java
public interface PrincipalCollection extends Iterable, Serializable {  
    Object getPrimaryPrincipal(); //得到主要的身份  
    <T> T oneByType(Class<T> type); //根据身份类型获取第一个  
    <T> Collection<T> byType(Class<T> type); //根据身份类型获取一组  
    List asList(); //转换为List  
    Set asSet(); //转换为Set  
    Collection fromRealm(String realmName); //根据Realm名字获取  
    Set<String> getRealmNames(); //获取所有身份验证通过的Realm名字  
    boolean isEmpty(); //判断是否为空  
}   
````

##### （3）授权

![img](http://dl2.iteye.com/upload/attachment/0094/0549/541e4da3-d1a5-3d13-83a6-b65c3596ee4e.png)

流程：

* 首先调用Subject.isPermitted*/hasRole*接口，其会委托给SecurityManager，而SecurityManager接着会委托给Authorizer；
* `Authorizer`是真正的授权者，如果我们调用如isPermitted(“user:view”)，其首先会通过PermissionResolver把字符串转换成相应的Permission实例；
* 在进行授权之前，其会调用相应的Realm获取Subject相应的角色/权限用于匹配传入的角色/权限；
* Authorizer会判断Realm的角色/权限是否和传入的匹配，如果有多个Realm，会委托给`ModularRealmAuthorizer`进行循环判断，如果匹配如isPermitted*/hasRole*会返回true，否则返回false表示授权失败。

`ModularRealmAuthorizer`进行多Realm匹配流程：

* 首先检查相应的Realm是否实现了`Authorizer`；
* 如果实现了`Authorizer`，那么接着调用其相应的isPermitted*/hasRole*接口进行匹配；
* 如果有一个Realm匹配那么将返回true，否则返回false。

如果Realm进行授权的话，应该继承AuthorizingRealm。

* 总结

PS：（shiro默认使用的是servlet容器的会话管理 ServletContainerSessionManager）

PS：（subject.isAuthenticated() 使用的是session来判断客户端的Cookie和服务端的session，所以前后端分离会失效，如果使用vue可以加上axios.defaults.withCredentials=true来对Cookie的保存，如果不开启，在客户端中看不到自动生成的JSEESIONID）

##### 二、创建ShiroConfig配置类

> shiroFilter过滤器

````java
 /**
     * 创建shiroFilterFactoryBean过滤器
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilter(){
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ shiroFilter过滤器执行了");
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //注册securityManager
        shiroFilterFactoryBean.setSecurityManager(securityManager());
        //未认证跳转的页面
        shiroFilterFactoryBean.setLoginUrl();
        //自定义权限
         shiroFilterFactoryBean.setFilterChainDefinitionMap(getChainDefinitionMap());
        return shiroFilterFactoryBean;
    }
   
````

> securityManager安全管理器

````java
    /**
     * 创建DefaultWebSecurityManager安全管理器
     */
    @Bean
    public DefaultWebSecurityManager securityManager(){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //注入Realm
        securityManager.setRealm(realm());
        //注入缓存管理器并且realm开启缓存，根据设置的过期时间不用反复的访问数据库
        securityManager.setCacheManager(cacheManager());
        //注入session管理器
        //securityManager.setSessionManager(sessionManager());
        return securityManager;
    }
````

> 获取自定义realm数据源

````java
    @Autowired
    private UserRealm userRealm;

    /**
     * 身份验证器
     * @return
     */
    @Bean
    public UserRealm realm(){
        //注入凭证匹配器
        userRealm.setCredentialsMatcher(credentialsMatcher());
        //是否开启缓存
        userRealm.setCachingEnabled(CACHINGENABLED);
        //认证缓存
        userRealm.setAuthenticationCachingEnabled(AUTHENTICATION_CACHING_ENABLED);
        userRealm.setAuthenticationCacheName("authenticationCache");
        //授权缓存
        userRealm.setAuthorizationCachingEnabled(AUTHORIZATION_CACHING_ENABLED);
        userRealm.setAuthorizationCacheName("authorizationCache");
        return userRealm;
    }
````

> credentialsMatcher凭证匹配器

````java
    /**
     * 密码验证凭证器
     */
    @Bean
    public HashedCredentialsMatcher credentialsMatcher() {
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        //加密方式
        credentialsMatcher.setHashAlgorithmName(ALGORITHM);
        //叠加次数
        credentialsMatcher.setHashIterations(ITERATION);
        //存储散列后的密码是否为16进制
        credentialsMatcher.setStoredCredentialsHexEncoded(HEXENCODED);
        return credentialsMatcher;
    }
````

> cacheManager缓存

（1）ehcache缓存

````java
    /**
     * ehcache缓存管理器（不用再去数据库查询数据做认证和授权）
     * @return
     */
    @Bean
    public CacheManager cacheManager(){
        EhCacheManager cacheManager = new EhCacheManager();
        //缓存的配置
        cacheManager.setCacheManagerConfigFile("classpath:ehcache.xml");
        return cacheManager;
    }
````

（2）redis缓存

````java
    /**
     * redis连接管理器
     */
    @Bean
    public RedisManager redisManager(){
        System.out.println("连接redis中。。。。");
        RedisManager redisManager = new RedisManager();
        //设置主机端口号，新版本
        redisManager.setHost(HOST+":"+PORT);
        //旧版本
        //redisManager.setPort(PORT);
        //设置密码,如果没有密码不用设置，不然会报错
        //redisManager.setPassword("");
        //设置连接超时时间
        redisManager.setTimeout(TIMEOUT);
        return redisManager;
    }

    /**
     * Redis集群使用RedisClusterManager，单个Redis使用RedisManager
     * redis认证授权缓存管理器（不用再去数据库查询数据做认证和授权）
     */
    @Bean
    public CacheManager cacheManager(){
        RedisCacheManager redisCacheManager = new RedisCacheManager();
        //注入redis管理器
        redisCacheManager.setRedisManager(redisManager());
        //设置缓存过期时间
        redisCacheManager.setExpire(150);
        return redisCacheManager;
    }


````

> AOP权限注解开启

````java
    /**
     * 开启注解支持
     */
    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(){
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        //注入安全管理器
        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager());
        return authorizationAttributeSourceAdvisor;
    }
  
````

注：不需要配置aop，springboot自动配置proxy-traget-class=true（基于类的代理），false（基于接口）

````java
    //不加这个注解不生效?
    @Bean
    @ConditionalOnMissingBean
    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        defaultAdvisorAutoProxyCreator.setProxyTargetClass(true);
        return defaultAdvisorAutoProxyCreator;
    }
````

##### 三、实现认证资源权限yml配置

（1）配置application.uml

````java
#动态权限配置文件
shiro:
  loginUrl: /
  lists:
  - url: /admin/test
    permission: authc

````

（2）创建ChainDefinitionList类

````java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.LinkedHashMap;
import java.util.List;

@Component
@ConfigurationProperties(prefix = "shiro")
public class ChainDefinitionList {
    private List<LinkedHashMap<String,String>> lists;

    public List<LinkedHashMap<String, String>> getLists() {
        return lists;
    }

    public void setLists(List<LinkedHashMap<String, String>> lists) {
        this.lists = lists;
    }

    @Override
    public String toString() {
        return "ChainDefinitionList{" +
                "lists=" + lists +
                '}';
    }
}
````

（3）加载到ShiroConfig中

> 用@Autowired注入ChainDefinitionList，切记不能使用new ChainDefinitionList();

````java
    /**
     * 获取动态权限
     * @return
     */
    @Bean
    public Map<String,String> getChainDefinitionMap(){
        //新建一个LinkedHashMap来存储数据
        Map<String,String> chainDefinitionMap = new LinkedHashMap<>();
        //遍历lists集合
        for(Map<String,String> map:chainDefinitionList.getLists()){
            chainDefinitionMap.put(map.get("url"),map.get("permission"));
        }
        System.out.println(chainDefinitionMap);
        return chainDefinitionMap;
    }
````

##### 四、使用Session来管理客户端和服务端的验证（有状态）

（1）自定义MySessionManager

````java
import org.apache.shiro.web.servlet.ShiroHttpServletRequest;
import org.apache.shiro.web.session.mgt.DefaultWebSessionManager;
import org.apache.shiro.web.util.WebUtils;
import org.springframework.util.StringUtils;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.io.Serializable;

public class MySessionManager extends DefaultWebSessionManager {

    public MySessionManager() {
        super();
    }

    @Override
    protected Serializable getSessionId(ServletRequest request, ServletResponse response) {
        System.out.println("自定义Session管理器生效中....");
        //获取session中key为token的sessionId
        String id = WebUtils.toHttp(request).getHeader("token");
        System.out.println(id);
        if (!StringUtils.isEmpty(id) && !"null".equals(id)) {
            request.setAttribute(ShiroHttpServletRequest.REFERENCED_SESSION_ID_SOURCE, "Stateless request");
            request.setAttribute(ShiroHttpServletRequest.REFERENCED_SESSION_ID, id);
            request.setAttribute(ShiroHttpServletRequest.REFERENCED_SESSION_ID_IS_VALID, Boolean.TRUE);
            return id;
        } else {
            //否则按默认规则从cookie取sessionId
            return super.getSessionId(request, response);
        }
    }
}

````

（2）在ShiroConfig中添加SessionManager

````java
    /**
     * Session管理器
     * @return
     */
    @Bean
    public SessionManager sessionManager(){
        MySessionManager mySessionManager = new MySessionManager();
        //注入sessionDAO
        mySessionManager.setSessionDAO(sessionDAO());
        return mySessionManager;
    }
````

（3）在ShiroConfig中添加SessionDAO

````java
    /**
     * redisSessionDAO
     */
    @Bean
    public SessionDAO sessionDAO(){
        RedisSessionDAO redisSessionDAO = new RedisSessionDAO();
        //注入redis管理器
        redisSessionDAO.setRedisManager(redisManager());
        return redisSessionDAO;
    }
````

##### 五、使用JWT来管理客户端和服务端的验证（无状态）

详情见springboot整合shiro+jwt

##### 六、有状态和无状态的区别

百度一下

