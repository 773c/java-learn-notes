# �����û���Ȩ

## ����֤����

### 1. �Ƚ�mysql����ֹͣ
````bash
net stop mysql
````

### 2. ʹ������֤��ʽ����mysql����

````bash
mysqld --skip-grant-tables
````

### 3. ��¼mysql

````bash
mysql> use mysql;
       Database changed
mysql> update user set password = password('root') where user='root';
````

### 4. �ġ������������

````bash
�ֶ��ر�mysqld����
````

## �û�����
### 1. ����û���
````bash
CREATE USER '�û���'@'������' IDENTIFIED BY '����';
````

### 2. ɾ���û���

````bash
DROP USER '�û���'@'������';
````

### 3. �޸��û����룺

````bash
UPDATE USER SET PASSWORD=PASSWORD('������') WHERE USER = '�û���';
SET PASSWORD FOR '�û���'@'������' = PASSWORD('������');
````

## Ȩ��
### 1. ��ѯȨ�ޣ�
````bash
SHOW GRANTS FOR '�û���'@'������';
````

### 2. ����Ȩ�ޣ�

````bash
GRANT Ȩ���б� ON ���ݿ���.���� TO '�û���'@'������';
````

### 3. ����Ȩ�ޣ�

````bash
REVOKE Ȩ���б� ON ���ݿ���.���� FROM '�û���'@'������';
````

