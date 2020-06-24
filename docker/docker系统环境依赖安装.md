# Docker系统环境依赖安装

## 安装步骤

### 1. yum包更新到最新
````bash
sudo yum update
````

### 2. 安装需要的软件包，yum-util提供yum-config-manager功能，另外两个是devicemapper驱动依赖
````bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
````

### 3. 设置yum源为阿里云
````bash
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
````

### 4. 更新并安装docker
````bash
sudo yum makecache fast
sudo yum -y install docker-ce
````

### 5. 安装后查看docker版本
````bash
docker -v
````

**注意：**

> 官方软件源默认启用了最新的软件，您可以通过编辑软件源的方式获取各个版本的软件包。例如官方并没有将测试版本的软件源置为可用，您可以通过以下方式开启。同理可以开启各种测试版本等。

````bash
vim /etc/yum.repos.d/docker-ee.repo

将[docker-ce-test]下方的enabled=0修改为enabled=1

安装指定版本的Docker-CE:

Step 1: 查找Docker-CE的版本:

yum list docker-ce.x86_64 --showduplicates | sort -r

Loading mirror speeds from cached hostfile

Loaded plugins: branch, fastestmirror, langpacks

docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable

docker-ce.x86_64            17.03.1.ce-1.el7.centos            @docker-ce-stable

docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable

Available Packages

Step2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.0.ce.1-1.el7.centos)

sudo yum -y install docker-ce-[VERSION]
````

### 6. 启动docker
````bash
systemctl start docker（restart重启）或 service docker start
````

**开机自动启动**

````bash
systemctl enable docker
````

### 7. 设置docker的ustc镜像
````bash
# 没有就创建一个touch daemon.json
vim docker安装目录下的/daemon.json	
# 添加
{
    "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
# 查询是否有Registry Mirrors
docker info	
````

