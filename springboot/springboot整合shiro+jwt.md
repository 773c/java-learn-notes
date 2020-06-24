#### JWT介绍

##### 一、JWT的组成

> 三部分，可以进行客户端与服务端之间验证的一种技术，取代了之前使用Session来验证的不安全性
>
> 为什么不适用Session？
>
> 原理是，登录之后客户端和服务端各自保存一个相应的SessionId，每次客户端发起请求的时候就得携带这个SessionId来进行比对
>
> 1. Session在用户请求量大的时候服务器开销太大了
> 2. Session不利于搭建服务器的集群（也就是必须访问原本的那个服务器才能获取对应的SessionId）

* JWT token的格式：header.payload.signature
* header中用于存放签名的生成算法

> 1. 存储两个变量
>    1. 秘钥（可以用来比对）
>    2. 算法（也就是下面将Header和payload加密成Signature）

````java
{"alg": "HS512"}
````

* payload中用于存放用户名、token的生成时间和过期时间

> 存储很多东西，基础信息有如下几个
>
> 1. 签发人，也就是这个“令牌”归属于哪个用户。一般是`userId`
> 2. 创建时间，也就是这个令牌是什么时候创建的
> 3. 失效时间，也就是这个令牌什么时候失效
> 4. 唯一标识，一般可以使用算法生成一个唯一标识

````java
{"sub":"admin","created":1489079981393,"exp":1489684781}
````

* signature为以header和payload生成的签名，一旦header和payload被篡改，验证将失败

> 这个是上面两个经过Header中的算法加密生成的，用于比对信息，防止篡改Header和payload
>
> ​	然后将这三个部分的信息经过加密生成一个`JwtToken`的字符串，发送给客户端，客户端保存在本地。当客户端发起请求的时候携带这个到服务端(可以是在`cookie`，可以是在`header`，可以是在`localStorage`中)，在服务端进行验证

````java
//secret为加密算法的密钥
String signature = HMACSHA512(base64UrlEncode(header) + "." +base64UrlEncode(payload),secret)
````

##### 二、JWT实例

这是一个JWT的字符串

````java
eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbiIsImNyZWF0ZWQiOjE1NTY3NzkxMjUzMDksImV4cCI6MTU1NzM4MzkyNX0.d-iki0193X0bBOETf2UN3r3PotNIEAV7mzIxxeI5IxFyzzkOZxS0PGfF_SK6wxCv2K8S0cZjMkv6b5bCqc0VBw

````

可以在该网站上获得解析结果：[https://jwt.io/]()

##### 三、JWT实现认证和授权的原理

- 用户调用登录接口，登录成功后获取到JWT的token；
- 之后用户每次调用接口都在http的header中添加一个叫Authorization的头，值为JWT的token；
- 后台程序通过对Authorization头中信息的解码及数字签名校验来获取其中的用户信息，从而实现认证和授权。

##### 四、Hutool

> Hutool是一个丰富的Java开源工具包,它帮助我们简化每一行代码，减少每一个方法。

#### 实现Shiro+JWT

##### 相关依赖

````java
            <dependency>
                <groupId>org.apache.shiro</groupId>
                <artifactId>shiro-spring</artifactId>
                <version></version>
            </dependency>
            <dependency>
                <groupId>io.jsonwebtoken</groupId>
                <artifactId>jjwt</artifactId>
                <version></version>
            </dependency>
            <dependency>
                <groupId>com.auth0</groupId>
                <artifactId>java-jwt</artifactId>
                <version></version>
            </dependency>   
````

##### 校验流程

我们使用Shiro主要做3件事情

* 用户请求/login直接通过用户名密码验证生成token，或者通过UserRealm来进行验证生成token

* 用户登录成功后，其他请求都通过过滤器来验证用户是否有效

  （1）请求认证的流程其实和登录认证流程是比较相似的，因为我们的服务是无状态的，所以每次请求带来token，我们就是做了一次登录操作。

  （2）其实对于无状态服务来说，每次请求都相当于做了一次登录操作，我们用session的时候之所以不需要做，是因为容器代替我们把这件事干掉了。

* 用户权限的校验

##### 流程图

![image-20200429184617707](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20200429184617707.png)

##### 相关自定义

* 首先是初始化JwtShiroConfig
* 添加JwtUserRealm类
* 添加JwtTokenUtil工具类
* 添加JwtAuthticationTokenFilter过滤器
* 添加JwtToken（类似UsernamePasswordToken）
* 添加JwtDefaultSubjectFactory（可选）

##### 一、配置JwtShiroConfig

````java
import com.cjj.qlmusic.security.custom.ChainDefinitionList;
import com.cjj.qlmusic.security.custom.JwtDeafultSubjectFactory;
import com.cjj.qlmusic.security.custom.JwtUserRealm;
import com.cjj.qlmusic.security.custom.UserRealm;
import com.cjj.qlmusic.security.filter.JwtAuthticationTokenFilter;
import com.cjj.qlmusic.security.util.JwtTokenUtil;
import org.apache.shiro.authc.credential.HashedCredentialsMatcher;
import org.apache.shiro.cache.CacheManager;
import org.apache.shiro.mgt.DefaultSessionStorageEvaluator;
import org.apache.shiro.mgt.DefaultSubjectDAO;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.crazycake.shiro.RedisCacheManager;
import org.crazycake.shiro.RedisManager;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.servlet.Filter;
import java.util.Arrays;
import java.util.HashMap;
import java.util.LinkedHashMap;
import java.util.Map;

/**
 * JwtShiroConfig配置类
 */
@Configuration
public class JwtShiroConfig {
    @Value("${shiro.loginUrl}")
    private String LOGIN_URL;
    @Value("${shiro.unauthorizedUrl}")
    private String UNAUTHORIZEDURL;
    @Value("${shiro.credentialsMatcher.algorithm}")
    private String ALGORITHM;
    @Value("${shiro.credentialsMatcher.iteration}")
    private Integer ITERATION;
    @Value("${shiro.credentialsMatcher.hexEncoded}")
    private boolean HEXENCODED;
    @Value("${shiro.realm.cachingEnabled}")
    private boolean CACHINGENABLED;
    @Value("${shiro.realm.authenticationCachingEnabled}")
    private boolean AUTHENTICATION_CACHING_ENABLED;
    @Value("${shiro.realm.authorizationCachingEnabled}")
    private boolean AUTHORIZATION_CACHING_ENABLED;
    @Value("${spring.redis.host}")
    private String HOST;
    @Value("${spring.redis.port}")
    private Integer PORT;
//    @Value("${spring.redis.password}")
//    private String PASSWORD;
    @Value("${spring.redis.timeout}")
    private Integer TIMEOUT;
    
    /**
     * shiroFilter过滤器
     * @return
     */
    @Bean
    public ShiroFilterFactoryBean shiroFilter() {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ JwtShiroFilter过滤器执行了");
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        //注册securityManager
        shiroFilterFactoryBean.setSecurityManager(securityManager());
        //未认证跳转api
        shiroFilterFactoryBean.setLoginUrl(LOGIN_URL);
        //未授权跳转的api
        shiroFilterFactoryBean.setUnauthorizedUrl(UNAUTHORIZEDURL);
        //设置过滤器
        Map<String, Filter> filterMap = new HashMap<>();
        filterMap.put("jwtFilter", new JwtAuthticationTokenFilter());
        shiroFilterFactoryBean.setFilters(filterMap);
        //设置认证权限
        shiroFilterFactoryBean.setFilterChainDefinitionMap(getChainDefinitionMap());
        return shiroFilterFactoryBean;
    }

    /**
     * securityManager安全管理器
     * @return
     */
    @Bean
    public DefaultWebSecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //注入realm
        securityManager.setRealms(Arrays.asList(userRealm(), jwtUserRealm()));
        //注入缓存
        securityManager.setCacheManager(cacheManager());
        /*
         * jwt配置
         */
        // 关闭 ShiroDAO 功能
        DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
        DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
        // 不需要将 Shiro Session 中的东西存到任何地方（包括 Http Session 中）
        defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
        subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
        securityManager.setSubjectDAO(subjectDAO);
        //禁止Subject的getSession方法
//        securityManager.setSubjectFactory(subjectFactory());
        return securityManager;
    }

    /**
     * 获取动态权限
     * @return
     */
    @Bean
    public Map<String, String> getChainDefinitionMap() {
        //新建一个LinkedHashMap来存储数据
        Map<String, String> chainDefinitionMap = new LinkedHashMap<>();
        //遍历lists集合
        for (Map<String, String> map : getChainDefinitionList().getLists()) {
            chainDefinitionMap.put(map.get("url"), map.get("permission"));
        }
        System.out.println(chainDefinitionMap);
        return chainDefinitionMap;
    }

    /**
     * redis连接管理器
     */
    @Bean
    public RedisManager redisManager() {
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
    public CacheManager cacheManager() {
        RedisCacheManager redisCacheManager = new RedisCacheManager();
        //注入redis管理器
        redisCacheManager.setRedisManager(redisManager());
        //设置缓存过期时间
        redisCacheManager.setExpire(60);
        return redisCacheManager;
    }

    /**
     * 获取自定义ChainDefinitionList
     * @return
     */
    @Bean
    public ChainDefinitionList getChainDefinitionList() {
        return new ChainDefinitionList();
    }

    /**
     * 用于登录验证的身份验证器
     */
    @Bean
    public UserRealm userRealm() {
        UserRealm userRealm = new UserRealm();
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

    /**
     * JWT身份验证器
     */
    @Bean
    public JwtUserRealm jwtUserRealm() {
        return new JwtUserRealm();
    }

    /**
     * 注入JwtTokenUtil
     */
    @Bean
    public JwtTokenUtil jwtTokenUtil() {
        return new JwtTokenUtil();
    }

    /**
     * 关闭ShiroDAO功能
     */
    public DefaultSubjectDAO subjectDAO(){
        DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
        DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
        //关闭session存储功能（包括HttpSession中）
        defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
        subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
        return subjectDAO;
    }
}

````

##### 二、JwtUserRealm

````java
import com.cjj.qlmusic.security.util.JwtTokenUtil;
import org.apache.shiro.authc.*;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;

/**
 * 用于JwtToken的Realm
 */
public class JwtTokenRealm extends AuthorizingRealm {
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    
    /**
     * 标识这个Realm是专门用来处理JwtToken
     * 不处理其他token（UsernamePasswordToken）
     */
    @Override
    public boolean support(AuthticationToken token){
        //这个token是从过滤器中注入的JwtToken(subject.login(token))
        return token instanceof JwtToken;
    }
    
    /**
     * 授权
     */
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
         System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ JwtAuthenorization认证中");
        return null;
    }

    /**
     * 认证
     */
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ JwtAuthentication认证中");
        String jwtToken = (String) authenticationToken.getPrincipal();
        //验证token
        if(!jwtTokenUtil.validateToken(jwtToken)){
            throw new UnknownAccountException();
        }
        SimpleAuthenticationInfo authenticationInfo = new SimpleAuthenticationInfo(
                jwtToken,
                jwtToken,
                getName()
        );
        return authenticationInfo;
    }
}
````

##### 三、JwtToken的工具类

> 用于生成和解析JWT token的工具类

````java
import com.cjj.qlmusic.security.entity.UmsAdmin;
import com.cjj.qlmusic.security.service.UmsAdminService;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;

import java.util.Date;
import java.util.Map;

/**
 * JwtToken生成工具
 */
public class JwtTokenUtil {
    private static final Logger LOGGER = LoggerFactory.getLogger(JwtTokenUtil);
    private static final String CLAIM_ACCOUNT = "account";
    
    @Autowired
    private UmsAdminService umsAdminService;
    @Value("${jwt.secretKey}")
    private String secretKey;
    @Value("${jwt.expiration}")
    private long expiration;
    
    /**
     * 生成token
     */
    public String generateToken(Map<String,Object> claims){
        return Jwts.Builder()
            .setClaims(claims)
            .setIssuedAt(new Date())
            .setExpiration(generateExpirationDate())
            .signWith(SignatureAlgorithm.HS512,secretKey)
            .compact();
    }
    
    /**
     * 生成过期时间
     */
    public Date generateExpirationDate(){
        return new Date(System.currentTimeMillis() + expiration * 1000)
    }
    
    /**
     * 获取Claims，根据jwtToken的格式获取，如果token格式不正确则返回null
     */
    public Claims getClaimsFromToken(String token){
        Claims claims = null;
        try {
            claims = Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(token)
                .getBody();
        }catch(Exception e){
            LOGGER.info("JWT格式验证错误:{}", token)
        }
        return claims;
    }
    
    /**
     * 获取用户账号（名）
     */
    public String getAccountFromToken(String token){
        String account = null;
        try{
            Claims claims = getClaimsFromToken(token);
            account = (String)claims.get(CLAIM_ACCOUNT);
        }catch(Exception e){
            account = null;
        }
        return account;
    }
    
    /**
     * 验证token是否有效（用户名与数据库是否一致,一般用于Realm）
     */
    public boolean validateToken(String token){
        String account = getExpirationDate();
        UmsAdmin admin = umsAdminService.findByAccount(account);
        if(admin == null){
            return false;
        }
        return true;
    }
    
    /**
     * 判断token是否过期
     */
    public boolean isTokenExpiration(String token){
        Date expiration = getExpirationDateFromToken(token);
    }
    
    /**
     * 获取过期时间
     */
    public Date getExpirationDateFromToken(String token){
        Claims claims = getClaimsFromToken(token);
        return claims.getExpiration();
    }
}
````

##### 四、JwtAuthticationTokenFilter过滤器

> 用于过滤除了anon和logout的其他url
>
> 继承AuthticatingFilter，重写isAccessAllow()、createToken()、onAccessDenied()、onLoginSuccess()、onLoginFailure()五个方法
>
> 在isAccessAllow中调用父类的executeLogin()。父类首先会调用createToken()，如果AuthenticationToken不为null，则会执行subject.login(token)

父类AuthenticatingFilter的源码如下

````java
 protected boolean executeLogin(ServletRequest request, ServletResponse response) throws Exception {
        AuthenticationToken token = this.createToken(request, response);
        if (token == null) {
            String msg = "createToken method implementation returned null. A valid non-null AuthenticationToken must be created in order to execute a login attempt.";
            throw new IllegalStateException(msg);
        } else {
            try {
                Subject subject = this.getSubject(request, response);
                subject.login(token);
                return this.onLoginSuccess(token, subject, request, response);
            } catch (AuthenticationException var5) {
                return this.onLoginFailure(token, var5, request, response);
            }
        }
    }
````

JwtAuthticationTokenFilter

````java
import cn.hutool.http.HttpStatus;
import com.cjj.qlmusic.security.custom.JwtToken;
import com.cjj.qlmusic.security.util.JwtTokenUtil;
import com.cjj.qlmusic.security.util.SpringContextUtil;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.subject.Subject;
import org.apache.shiro.web.filter.authc.AuthenticatingFilter;
import org.apache.shiro.web.util.WebUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.util.StringUtils;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * JwtToken登录授权过滤器
 * isAccessAllowed()用于token的认证，返回false则执行onAccessDenied()（token是否有效）
 * onAccessDenied()用于token的认证失败后进行重新登录再进行访问，登录成功即可访问，反之（token为null或过期时）
 */
public class JwtAuthticationTokenFilter extends AuthticatingFilter {
     private static final Logger LOGGER = LoggerFactory.getLogger(JwtAuthticationTokenFilter.class);
    @Autowired
    private JwtTokenUtil jwtTokenUtil;
    
    /**
     * 使用我们自己定义的Token类，提交给shiro。
     * 这个方法返回null的话会直接抛出异常，进入isAccessAllowed()的异常处理逻辑。
     */
    @Override
    protected AuthenticationToken createToken(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ createToken（创建JwtToken）");
        //获取JwtTokenUtil
        if(jwtTokenUtil == null){
            jwtTokenUtil = (JwtTokenUtil) SpringContextUtil.getBean("jwtTokenUtil");
        }
        HttpServletRequest request = WebUtils.toHttp(servletRequest);
        //获取约定的tokenHeader
        String tokenHeader= SpringContextUtil.getApplicationContext()
                .getBean(Environment.class).getProperty("jwt.tokenHeader");
        //获取前端Header传来的token信息
        String token = request.getHeader("Authorization").trim();
        LOGGER.info("请求的Header中有token {}", token);
        //判断token不为空且
        try{
            if(!StringUtils.isEmpty(token)&&!jwtTokenUtil.isTokenExpiration(token))
                //返回自定义JwtToken
                return new JwtToken;
        }catch(NullPointerException e){
              LOGGER.info("Token is null");
        }
        return null;
    }
    
    /**
     * 1.返回true，则可以直接访问url
     * 2.返回false，则通过onAccessDenied()返回值来决定是否允许访问url
     */
    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object o) {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ isAccessAllowed（token认证中）");
        boolean allowed = false;
        try{
            //调用父类的executeLogin，AuthticationToken不为null则会执行subject.login(token)
            allowed = executeLogin(request,response);
            //createToken()相当于在这里面
        }catch (IllegalStateException e) {
            LOGGER.info("createToken return null");
        } catch (Exception e) {
            LOGGER.info("Error occurs when login", e);
        }
        //return allowed || super.isPermissive(mappedValue); 下面会说明permissive
        return allowed; 
    }
    
     /**
     * 1.返回true表示登录通过
     * 2.返回false则响应状态码给前端
     */
     @Override
    protected boolean onAccessDenied(ServletRequest servletRequest, ServletResponse servletResponse) throws Exception {
        System.out.println("❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥❥ onAccessDenied（token认证失败，重新登录）");
		//响应状态为403
        response.setStatus(HttpStatus.HTTP_FORBIDDEN);
        return false;
    }
    
    /**
     * 如果shiro的subject.login(token)认证成功，则会进入该方法。
     */
    @Override
    protected boolean onLoginSuccess(AuthenticationToken token, Subject subject, ServletRequest request, ServletResponse response) throws Exception {
        LOGGER.info("onLoginSuccess 被调用了");
        //可以做token的刷新
        .....
        return true;
    }
    
    /**
     * 如果shiro的subject.login(token)认证失败，则会进入该方法。
     */
     @Override
    protected boolean onLoginFailure(AuthenticationToken token, AuthenticationException e, ServletRequest request, ServletResponse response) {
        LOGGER.info("Validate token fail, token:{}, error:{}", token.toString(), e.getMessage());
        return false;
    }
}
````

ps：在过滤器中会发现在过滤器中无法初始化bean组件，均为NullPointerException。因为容器加载顺序的问题。在web.xml中各个元素的执行顺序是这样的，context-param-->listener-->filter-->servlet 可以看出在Spring MVC 的dispatcherservlet初始化之前过滤器就已经加载好了,所以注入的是null。

解决：创建一个获取上下文的工具类

````java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;
/**
 * Spring 上下文工具类
 *
 * @author nwgdk
 */
@Component
public class SpringContextUtil implements ApplicationContextAware {
    private static ApplicationContext applicationContext;
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        SpringContextUtil.applicationContext = applicationContext;
    }
    /**
     * 获取上下文
     */
    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }
    /**
     * 通过 bena 名称获取上下文中的 bean
     */
    public static Object getBean(String name) {
        return applicationContext.getBean(name);
    }
    /**
     * 通过类型获取上下文中的bean
     */
    public static Object getBean(Class<?> requiredType) {
        return applicationContext.getBean(requiredType);
    }
}
````

ps：过滤器使用@Value()获取为null

解决：

> Environment 用来表示整个应用运行时的环境，为了更形象地理解Environment，你可以把Spring应用的运行时简单地想象成两个部分：一个是Spring应用本身，一个是Spring应用所处的环境。

````java
ApplicationContext application = SpringContextUtil.getApplicationContext();
Environment environment = application.getBean(Environment.class);
String value = environment.getProperty("jwt.tokenHeader");
````



##### 关于permissive

> 如果filter的拦截配置那里配置了permissive参数，即使登录认证没通过，因为isPermissive返回true，还是会让请求继续下去的，如下：

````java
("/logout", "noSessionCreation,authcToken[permissive]")
````

ps：就是这么简单直接，字符串匹配。当然这里也可以重写这个方法插入更复杂的逻辑。
 这么做的目的是什么呢？因为有时候我们对待请求，并不都是非黑即白，比如登出操作，如果用户带的token是正确的，我们会将保存的用户信息清除；如果带的token是错的，也没关系，大不了不干啥，没必要返回错误给用户。还有一个典型的案例，比如我们阅读博客，匿名用户也是可以看的。只是如果是登录用户，我们会显示额外的东西，比如是不是点过赞等。所以认证这里的逻辑就是token是对的，我会给把人认出来；是错的，我也直接放过，留给controller来决定怎么区别对待。

##### 五、JwtToken

> 此类实现AuthticationToken或者实现HostAuthenticationToken（它的父接口是AuthticationToken），UsernamePasswordToken就是实现HostAuthenticationToken

````java
import org.apache.shiro.authc.AuthenticationToken;

/**
 * 这个类相当于UsernamePasswordToken
 */
public class JwtToken implements AuthenticationToken {
    private String token;

    public JwtToken(String token) {
        this.token = token;
    }

    @Override
    public Object getPrincipal() {
        return token;
    }

    @Override
    public Object getCredentials() {
        return token;
    }
}
````

##### 六、JwtDefaultSubjectFactory

> 关闭session
>
> 在测试时，抛出了一个异常，暂时还没有解决。所以暂时不用

````java
import org.apache.shiro.subject.Subject;
import org.apache.shiro.subject.SubjectContext;
import org.apache.shiro.web.mgt.DefaultWebSubjectFactory;

/**
 * 自定义DefaultSubject配置shiro不开启session
 */

public class JwtDeafultSubjectFactory extends DefaultWebSubjectFactory{
    @Override
    public Subject createSubject(SubjectContext context) {
        //关闭后，subject.getSession()会报错
        context.setSessionCreationEnabled(false);
        return super.createSubject(context);
    }
}

````

