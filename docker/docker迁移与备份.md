# Docker迁移与备份

## 操作步骤

### 1. 容器保存为镜像

````bash
docker commit 容器名称或ID 镜像名称 
````

### 2. 镜像备份

````bash
docker save -o 文件名.tar 镜像名称
````

### 3. 镜像恢复与迁移

````bash
docker load -i 文件名.tar
````

