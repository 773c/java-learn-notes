# ��ѹ��JDK

## ��װ����

### 1. ����Linuxϵͳ���Ƿ����jdk��
````bash
rpm -qa | grep -i java
````

### 2. ж��jdk

````bash
rpm -e --nodeps JDK����
````

### 3. ��tar.gz��ͨ��secureCRT�ϴ���Linuxϵͳ�ĸ�Ŀ¼

````bash
ALT+P ��������SFTP�����put -r "�̷�λ��"���̷�λ�ò��������ĺͿո�
````

### 4. ����Ŀ¼

````bash
mkdir -p /usr/local/java
````

### 5.  ��ѹ

````bash
tar -zxvf JDK����.tar.gz -C /usr/local/java
````

### 6. ���û�������

**�༭**

````bash
vim /etc/profile
````

**i���� �����������**

````bash
#set java environment
JAVA_HOME=/usr/local/java/JDK����
CLASSPATH=.:$JAVA_HOME/lib.tools.jar
PATH=$JAVA_HOME/bin:$PATH
export JAVA_HOME CLASSPATH PATH

````

**����**

> :wq(�����治�ɹ��� ��ǿ�Ʊ���)

### 7. ���¼���

````bash
source /etc/profile
````

