# Git���

## ʹ�ò���

### 1. ����Զ�̷�ʽһ��HTTP��

````bash
��һ����ѡ��Git Bash Here����Git ������ģʽ���вֿⴴ����
�ڶ���������git init������вֿ��ʼ���������������ļ�.git��
��������ִ��git add t1.txt�����ļ�txt�ύ�Ĳֿ��У�ֻ����ӵ�����������git add . ��������ļ����������ļ���
���Ĳ���ִ��git commit -m "first commit" ����ӵ��ļ��ύ���汾�⣬����д�ύ��ע��˫����Ϊ��ע��Ϣ��

����������GitHub��
git remote add origin url
git pull --rebase origin master�����������һ��ᱨ����Ϊ������Ŀû��README.md�ļ���
git push -u origin master
````

### 2. ����Զ�̷�ʽ����SSH��

````bash
GitHub��ȡSSH��Կ:
-$ ssh-keygen -t rsa
````

### 3. ��¡��ʽ��HTTP��

> ʹ�����ַ�ʽ���Ȱ�Զ�ֿ̲�clone������Ȼ��Ͳ�����ȥ����Զ�ֿ̲⣬����ֱ��push��

````bash
һ����github�����ֿ�󣬸���url�����ն�����
git clone url���ͻὫ����ֿ����ص�����λ�ã�

��������Ҫ���ļ����Ƶ�����ļ�����

�������ļ���ӣ��ύ
git add .��.�����Ŀ¼�µ������ļ���
git commit -m '��ʼ����Ŀ'

�ġ�ֱ��push��github��
git push
````







