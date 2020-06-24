##### 一、shiri过滤器属性

* securityManager：这个属性是必须的。
* loginUrl ：没有登录的用户请求需要登录的页面时自动跳转到登录页面，不是必须的属性，不输入地址的话会自动寻找项目web项目的根目录下的”/login.jsp”页面。
* successUrl ：登录成功默认跳转页面，不配置则跳转至”/”。如果登陆前点击的一个需要登录的页面，则在登录自动跳转到那个需要登录的页面。不跳转到此。
* unauthorizedUrl ：没有权限默认跳转的页面

##### 二、其限过滤器及配置释义

* anon:例子/admins/**=anon 没有参数，表示可以匿名使用。
* authc:例如/admins/user/**=authc表示需要认证(登录)才能使用，没有参数
* roles(角色)：例子/admins/user/** =roles[admin],参数可以写多个，多个时必须加上引号，并且参数之间用逗号分割，当有多个参数时，例如admins/user/**=roles["admin,guest"],每个参数通过才算通过，相当于hasAllRoles()方法。
* perms（权限）：例子/admins/user/** =perms[user:add:*],参数可以写多个，多个时必须加上引号，并且参数之间用逗号分割，例如/admins/user/**=perms["user:add:*,user:modify:*"]，当有多个参数时必须每个参数都通过才通过，想当于isPermitedAll()方法。
* rest：例子/admins/user/** =rest[user],根据请求的方法，相当于/admins/user/**=perms[user:method] ,其中method为post，get，delete等。
* port：例子/admins/user/**=port[8081],当请求的url的端口不是8081是跳转到schemal://serverName:8081?queryString,其中schmal是协议http或https等，serverName是你访问的host,8081是url配置里port的端口，queryString是你访问的url里的？后面的参数。
* authcBasic：例如/admins/user/**=authcBasic没有参数表示httpBasic认证
* ssl:例子/admins/user/**=ssl没有参数，表示安全的url请求，协议为https
* user:例如/admins/user/**=user没有参数表示必须存在用户，当登入操作时不做检查