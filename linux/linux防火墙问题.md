# Linux防火墙

## 安装步骤

### 1. Centos7安装防火墙

````bash
yum install firewalld systemd -y
````

### 2. 在Linux上安装tomcat必须开启防火墙

#### 2.1 Centos7

````bash
firewall-cmd --zone=public --add-port=80/tcp --permanent（添加80端口）
或
sudo firewall-cmd --add-port=80/tcp --permanent（添加80端口）
systemctl reload firewalld（重新加载）
````

**查看防火墙开放的端口号**

````bash
firewall-cmd --list-all 
````

**删除端口号**

````bash
firewall-cmd --zone=public --remove-port=80/tcp --permanent
````

**查看防火墙状态**

````bash
systemctl status firewalld.service
````

**关闭防火墙**

````bash
systemctl stop firewalld.service
````

#### 2.2 Centos6

````bash
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
/etc/rc.d/init.d/iptables save
````

**查看防火墙开放的端口号**

````bash
netstat -tunpl
````
