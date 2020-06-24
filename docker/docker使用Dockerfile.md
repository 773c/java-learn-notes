# Docker使用Dockerfile

## 使用步骤 

### 1. 什么是Dockerfile

> 是由一系列命令和参数构成的脚本，通俗来说就是用来构建镜像

### 2.常用命令

````bash
#FROM 基础镜像名称：TAG（定义以哪个基础镜像启动构建流程）
#MAINTAINER 创建者名称（声明镜像的创建者）
#ENV key value（设置环境变量）
#RUN command（是Dockerfile的核心部分）
#ADD 文件名称 dest_dir/file（将宿主机的文件复制到容器内，如果是压缩文件会自动解压）
#COPY 文件名称 dest_dir/file（将宿主机的文件复制到容器内，如果是压缩文件不会自动解压）
#WORKDIR 地址目录（设置工作目录）
````



