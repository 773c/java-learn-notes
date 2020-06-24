#### tomcat部署项目有三种方式

##### 一、直接将war包放入tomcat的webapps文件夹下

> ----访问路径：localhost:端口号/项目名
>
> ----改变路径为 / ：在tomcat的server.xml中添加
> <Context docBase="/项目名" path="/" reloadable="true" />

##### 二、将war包放在任意盘符下

> ----设置路径：在tomcat的server.xml中添加
> <Context docBase="盘符位置/项目名.war" path="/任意（不能为 / ）" reloadable="true" />
>
> ----启动tomcat后，会在webapps下生成一个path的名称的项目文件夹

##### 三、将war包放在任意盘符下

> ----添加xml文件:在tomcat的conf/Catalina/localhost 下创建一个任意名称的xml文件（名称即为访问虚拟路径）
>
> ----设置路径：在tomcat的server.xml中添加
> <Context docBase="盘符位置/项目名.war"  reloadable="true" />

