# Git详解

## 使用操作

### 1. 连接远程方式一（HTTP）

````bash
第一步，选择Git Bash Here，以Git 命令行模式进行仓库创建。
第二步，输入git init命令，进行仓库初始化（会生成隐藏文件.git）
第三步，执行git add t1.txt，将文件txt提交的仓库中（只是添加到缓存区），git add . 代表添加文件夹下所有文件。
第四步，执行git commit -m "first commit" 把添加的文件提交到版本库，并填写提交备注（双引号为备注信息）

接下来链接GitHub：
git remote add origin url
git pull --rebase origin master（如果不加这一句会报错，因为本地项目没有README.md文件）
git push -u origin master
````

### 2. 连接远程方式二（SSH）

````bash
GitHub获取SSH秘钥:
-$ ssh-keygen -t rsa
````

### 3. 克隆方式（HTTP）

> 使用这种方式是先把远程仓库clone下来，然后就不用再去连接远程仓库，可以直接push。

````bash
一、在github创建仓库后，复制url，在终端输入
git clone url（就会将这个仓库下载到本地位置）

二、将需要的文件复制到这个文件夹中

三、将文件添加，提交
git add .（.代表该目录下的所有文件）
git commit -m '初始化项目'

四、直接push到github上
git push
````







