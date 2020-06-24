# Linux����ǽ

## ��װ����

### 1. Centos7��װ����ǽ

````bash
yum install firewalld systemd -y
````

### 2. ��Linux�ϰ�װtomcat���뿪������ǽ

#### 2.1 Centos7

````bash
firewall-cmd --zone=public --add-port=80/tcp --permanent�����80�˿ڣ�
��
sudo firewall-cmd --add-port=80/tcp --permanent�����80�˿ڣ�
systemctl reload firewalld�����¼��أ�
````

**�鿴����ǽ���ŵĶ˿ں�**

````bash
firewall-cmd --list-all 
````

**ɾ���˿ں�**

````bash
firewall-cmd --zone=public --remove-port=80/tcp --permanent
````

**�鿴����ǽ״̬**

````bash
systemctl status firewalld.service
````

**�رշ���ǽ**

````bash
systemctl stop firewalld.service
````

#### 2.2 Centos6

````bash
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
/etc/rc.d/init.d/iptables save
````

**�鿴����ǽ���ŵĶ˿ں�**

````bash
netstat -tunpl
````
