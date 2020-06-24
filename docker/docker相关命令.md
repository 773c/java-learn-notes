# Docker相关命令

## 镜像命令

### 1. 查看已下载镜像
````bash
docker images
````

### 2. 搜索镜像
````bash
docker search 名称
````

### 3. 拉取镜像
````bash
docker pull 名称
````

### 4. 删除镜像
````bash
docker rmi 名称或ID
docker rmi `docker images -q`（删除所有镜像）
````

## 容器命令
### 1. 查看容器
````bash
docker ps （运行中）
docker ps -a（查看所有）
````

### 2. 创建容器
````bash
docker run -it --name=mycentos centos:7 /bin/bash（交互式）
#交互式会自动进入容器命令中
#exit退出后，容器将自动停止运行

docker run -id --name=mycentos2 centos:7（守护式）
#守护式创建时会出现一串字符串，不会自动进入容器命令中
#进入方式为 docker exec -it  mycentos2 /bin/bash
#exit退出后，容器不会自动停止运行

#-i表示运行容器
#-t表示进入容器命令中
#-d表示创建守护式容器，不会自动进入命令中， 退出不会自动停止运行
#--name表示为创建的容器命名
#-v表示目录映射关系
#-p表示端口映射
````

### 3. 启动容器
````bash
docker start 容器名称或ID
````

### 4. 停止容器
````bash
docker stop 容器名称或ID
````

### 5. 文件拷贝
````bash
docker cp 需要拷贝的文件或目录 容器名称：容器目录（拷进容器中）
docker cp 容器名称：容器目录 需要拷贝的文件或目录（从容器中拷出来）
````

### 6. 目录挂载（目录映射）
````bash
docker run -id --name=mycentos3 -v 宿主机目录:容器目录 镜像名称：TAG（如果是latest就不用写）
#如果出现权限不足的提示需要添加参数 --privileged=true
````

### 7. 查看容器信息
````bash
docker inspect 容器名称或ID（查看所有相关信息）
docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器名称或ID（查看IP）
````

### 8. 删除容器
````bash
docker rm 容器名称或ID（必须在容器停止运行的状态下）
````




