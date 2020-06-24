# 解压版JDK

## 安装步骤

### 1. 检查该Linux系统上是否存在jdk：
````bash
rpm -qa | grep -i java
````

### 2. 卸载jdk

````bash
rpm -e --nodeps JDK包名
````

### 3. 将tar.gz包通过secureCRT上传到Linux系统的根目录

````bash
ALT+P 即可跳出SFTP拖入或put -r "盘符位置"（盘符位置不能有中文和空格）
````

### 4. 创建目录

````bash
mkdir -p /usr/local/java
````

### 5.  解压

````bash
tar -zxvf JDK包名.tar.gz -C /usr/local/java
````

### 6. 配置环境变量

**编辑**

````bash
vim /etc/profile
````

**i插入 输入相关配置**

````bash
#set java environment
JAVA_HOME=/usr/local/java/JDK包名
CLASSPATH=.:$JAVA_HOME/lib.tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH

````

**保存**

> :wq(若保存不成功加 ！强制保存)

### 7. 重新加载

````bash
source /etc/profile
````

