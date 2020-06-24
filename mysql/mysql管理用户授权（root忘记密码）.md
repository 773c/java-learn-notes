# 管理用户授权

## 无验证操作

### 1. 先将mysql服务停止
````bash
net stop mysql
````

### 2. 使用无验证方式启动mysql服务

````bash
mysqld --skip-grant-tables
````

### 3. 登录mysql

````bash
mysql> use mysql;
       Database changed
mysql> update user set password = password('root') where user='root';
````

### 4. 四、打开任务管理器

````bash
手动关闭mysqld服务
````

## 用户管理
### 1. 添加用户：
````bash
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
````

### 2. 删除用户：

````bash
DROP USER '用户名'@'主机名';
````

### 3. 修改用户密码：

````bash
UPDATE USER SET PASSWORD=PASSWORD('新密码') WHERE USER = '用户名';
SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');
````

## 权限
### 1. 查询权限：
````bash
SHOW GRANTS FOR '用户名'@'主机名';
````

### 2. 授予权限：

````bash
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
````

### 3. 撤销权限：

````bash
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
````

