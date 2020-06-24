# Docker创建私有仓库

## 1. 操作步骤

### 1.1 拉取镜像

````bash
docker pull registry
````

### 1.2 创建容器

```bash
docker run -id --name=registry1 -p 5000:5000 镜像名称:TAG
```

### 1.3 访问私有仓库

````bash
ip地址:端口号/v2/_catalog	
# {"repositories":[]} 表示安装成功
````

### 1.4 修改daemon.json

````bash
vi /etc/docker/daemon.json
````

### 1.5 镜像上传到私有仓库
#### （1）标记此镜像为私有仓库的镜像
````bash
docker tag 镜像名称:TAG ip地址:端口号 /镜像名称
````

####  （2） 上传标记的镜像
````bash
docker push ip地址:端口号/镜像名称
````

