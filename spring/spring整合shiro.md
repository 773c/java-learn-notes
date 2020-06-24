#### 相关依赖：

````java
    <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-core</artifactId>
      <version>1.4.2</version>
    </dependency>
    <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-ehcache</artifactId>
      <version>1.4.2</version>
    </dependency>
    <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-spring</artifactId>
      <version>1.4.2</version>
    </dependency>
    <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-web</artifactId>
      <version>1.4.2</version>
    </dependency>
    <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-quartz</artifactId>
        <version>1.4.2</version>
    </dependency>
````

##### 一、Shiro三大核心

* Subject：用户主体（把操作交给SecurityManager）
* SecurityManager：安全管理器（关联Realm）
* Realm：身份验证器（连接数据的桥梁）

##### 二、创建applicationContext-shiro.xml

````java
  <!-- 会话Cookie模板 -->
    <bean id="sessionIdCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg value="sid"/>
        <property name="httpOnly" value="true"/>
        <property name="maxAge" value="-1"/>
    </bean>

    <bean id="rememberMeCookie" class="org.apache.shiro.web.servlet.SimpleCookie">
        <constructor-arg value="rememberMe"/>
        <property name="httpOnly" value="true"/>
        <property name="maxAge" value="60"/>
    </bean>

    <!-- 会话验证调度器 -->
    <bean id="sessionValidationScheduler" class="org.apache.shiro.session.mgt.quartz.QuartzSessionValidationScheduler">
        <property name="sessionValidationInterval" value="1800000"/>
        <property name="sessionManager" ref="sessionManager"/>
    </bean>

    <!-- 会话ID生成器 -->
    <bean id="sessionIdGenerator" class="org.apache.shiro.session.mgt.eis.JavaUuidSessionIdGenerator"/>

    <!-- 会话DAO -->
    <bean id="sessionDAO" class="org.apache.shiro.session.mgt.eis.EnterpriseCacheSessionDAO">
        <property name="activeSessionsCacheName" value="shiro-activeSessionCache"/>
        <property name="sessionIdGenerator" ref="sessionIdGenerator"/>

    </bean>

    <!-- 会话管理器 -->
    <bean id="sessionManager" class="org.apache.shiro.web.session.mgt.DefaultWebSessionManager">
        <property name="globalSessionTimeout" value="1800000"/>
        <property name="deleteInvalidSessions" value="true"/>
        <property name="sessionValidationSchedulerEnabled" value="true"/>
        <property name="sessionValidationScheduler" ref="sessionValidationScheduler"/>
        <property name="sessionDAO" ref="sessionDAO"/>
        <property name="sessionIdCookieEnabled" value="true"/>
        <property name="sessionIdCookie" ref="sessionIdCookie"/>
    </bean>

    <!-- 安全管理器 -->
    <bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
        <property name="realm" ref="userRealm"/>
        <property name="cacheManager" ref="cacheManager"/>
        <!--<property name="rememberMeManager" ref="rememberMeManager"/>-->
        <!--<property name="sessionManager" ref="sessionManager"/>-->
    </bean>

    <!-- rememberMe管理器 -->
    <bean id="rememberMeManager" class="org.apache.shiro.web.mgt.CookieRememberMeManager">
        <property name="cipherKey" value="#{T(org.apache.shiro.codec.Base64).decode('4AvVhmFLUs0KTA3Kprsdag==')}"/>
        <property name="cookie" ref="rememberMeCookie"/>
    </bean>

    <!-- Realm实现 -->
    <bean id="userRealm" class="com.cjj.shiro.UserRealm">
        <!--<property name="userService" ref="userService"/> set方式注入bean-->
        <property name="credentialsMatcher" ref="credentialsMatcher"/>
        <property name="cachingEnabled" value="true"/>
        <property name="authenticationCachingEnabled" value="true"/>
        <property name="authenticationCacheName" value="authenticationCache"/>
        <property name="authorizationCachingEnabled" value="true"/>
        <property name="authorizationCacheName" value="authorizationCache"/>
    </bean>

    <!-- 凭证匹配器 -->
    <bean id="credentialsMatcher" class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
        <!--<constructor-arg ref="cacheManager"/>-->
        <property name="hashAlgorithmName" value="md5"/>
        <property name="hashIterations" value="2"/>
        <property name="storedCredentialsHexEncoded" value="true"/>
    </bean>

    <!-- 缓存管理器 使用Ehcache实现 -->
    <bean id="cacheManager" class="org.apache.shiro.cache.ehcache.EhCacheManager">
        <property name="cacheManagerConfigFile" value="classpath:ehcache.xml"/>
    </bean>


    <!--SSL证书加密-->
    <bean id="sslFilter" class="org.apache.shiro.web.filter.authz.SslFilter">
        <property name="port" value="8443"/>
    </bean>

    <!--控制并发人数-->
    <bean id="kickoutSessionControlFilter" class="com.cjj.shiro.KickoutSessionControlFilter">
        <property name="cacheManager" ref="cacheManager"/>
        <property name="sessionManager" ref="sessionManager"/>
        <property name="kickoutAfter" value="false"/>
        <property name="maxSession" value="1"/>
        <property name="kickoutUrl" value="/login?kickout=1"/>
    </bean>

    <!--<bean class="org.apache.shiro.spring.LifecycleBeanPostProcessor" />-->


    <!-- 基于Form表单的身份验证过滤器 -->
    <!--<bean id="formAuthenticationFilter" class="org.apache.shiro.web.filter.authc.FormAuthenticationFilter">-->
    <!--<property name="rememberMeParam" value="rememberMe"/>-->
    <!--<property name="usernameParam" value="username"/>-->
    <!--<property name="passwordParam" value="password"/>-->
    <!--<property name="loginUrl" value="/"/>-->
    <!--</bean>-->
    <!-- Shiro的Web过滤器 -->
    <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
        <!--注册securityManager-->
        <property name="securityManager" ref="securityManager"/>
        <!-- loginUrl认证提交地址，如果没有认证将会请求该地址进行认证，请求该地址将由FormAuthenticationFilter进行表单认证 -->
        <property name="loginUrl" value="/index.jsp"/>
        <!--访问未授权的页面跳转的页面-->
        <!--<property name="unauthorizedUrl" value="/unauthorized"/>-->
        <property name="filters">
            <util:map>
                <!--<entry key="authc" value-ref="formAuthenticationFilter"/>-->
                <entry key="ssl" value-ref="sslFilter"/>
                <entry key="kickout" value-ref="kickoutSessionControlFilter"/>
            </util:map>
        </property>
        <property name="filterChainDefinitions">
            <value>
                /index.jsp = anon
                /unauthorized = anon
                /logout = logout
                /main2.jsp = ssl,authc
                /main3.jsp = anon
                /login = anon
                /register = anon
                /main = kickout,authc
                /update = anon
                /logout = anon
                /js/** = anon
                /** = kickout,user <!--其他设置为user -->
            </value>
        </property>
    </bean>
````

##### 三、在springMVC上添加权限的注解支持

````java
    <!--开启shiro权限注解-->
    <aop:config proxy-target-class="true"></aop:config>
    <bean class="org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
        <property name="securityManager" ref="securityManager"/>
    </bean>
````



注：spring的事务AOP注解整合shiro时，@Transactional不能在service层上使用

