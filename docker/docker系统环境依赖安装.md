# Dockerϵͳ����������װ

## ��װ����

### 1. yum�����µ�����
````bash
sudo yum update
````

### 2. ��װ��Ҫ���������yum-util�ṩyum-config-manager���ܣ�����������devicemapper��������
````bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
````

### 3. ����yumԴΪ������
````bash
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
````

### 4. ���²���װdocker
````bash
sudo yum makecache fast
sudo yum -y install docker-ce
````

### 5. ��װ��鿴docker�汾
````bash
docker -v
````

**ע�⣺**

> �ٷ����ԴĬ�����������µ������������ͨ���༭���Դ�ķ�ʽ��ȡ�����汾�������������ٷ���û�н����԰汾�����Դ��Ϊ���ã�������ͨ�����·�ʽ������ͬ����Կ������ֲ��԰汾�ȡ�

````bash
vim /etc/yum.repos.d/docker-ee.repo

��[docker-ce-test]�·���enabled=0�޸�Ϊenabled=1

��װָ���汾��Docker-CE:

Step 1: ����Docker-CE�İ汾:

yum list docker-ce.x86_64 --showduplicates | sort -r

Loading mirror speeds from cached hostfile

Loaded plugins: branch, fastestmirror, langpacks

docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable

docker-ce.x86_64            17.03.1.ce-1.el7.centos            @docker-ce-stable

docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable

Available Packages

Step2: ��װָ���汾��Docker-CE: (VERSION���������17.03.0.ce.1-1.el7.centos)

sudo yum -y install docker-ce-[VERSION]
````

### 6. ����docker
````bash
systemctl start docker��restart�������� service docker start
````

**�����Զ�����**

````bash
systemctl enable docker
````

### 7. ����docker��ustc����
````bash
# û�оʹ���һ��touch daemon.json
vim docker��װĿ¼�µ�/daemon.json	
# ���
{
    "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
# ��ѯ�Ƿ���Registry Mirrors
docker info	
````

