#### tomcat������Ŀ�����ַ�ʽ

##### һ��ֱ�ӽ�war������tomcat��webapps�ļ�����

> ----����·����localhost:�˿ں�/��Ŀ��
>
> ----�ı�·��Ϊ / ����tomcat��server.xml�����
> <Context docBase="/��Ŀ��" path="/" reloadable="true" />

##### ������war�����������̷���

> ----����·������tomcat��server.xml�����
> <Context docBase="�̷�λ��/��Ŀ��.war" path="/���⣨����Ϊ / ��" reloadable="true" />
>
> ----����tomcat�󣬻���webapps������һ��path�����Ƶ���Ŀ�ļ���

##### ������war�����������̷���

> ----���xml�ļ�:��tomcat��conf/Catalina/localhost �´���һ���������Ƶ�xml�ļ������Ƽ�Ϊ��������·����
>
> ----����·������tomcat��server.xml�����
> <Context docBase="�̷�λ��/��Ŀ��.war"  reloadable="true" />

