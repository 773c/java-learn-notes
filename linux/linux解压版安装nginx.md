# ��ѹ��Nginx

## ��װ����

### 1. ��Ҫ������
````bash
pcre
openssl
zlib
````

### 2. ��װpcre����

````bash
/usr/src ��ѹ�ļ�
./configure �ڸ�Ŀ¼��ִ�� 
make
make install   //��make && make install
pcre-config --version ��װ֮��鿴�汾��
````

### 3. һ����װ��������

````bash
yum -y install make  zlib zlib-devel gcc-c++ libtool openssl openssl-devel
````

### 4. ��װnginx

````bash
/usr/src ��ѹ�ļ�
./configure �ڸ�Ŀ¼��ִ�� 
make
make install ����make && make install��
��װ����/usr/local�»��Զ�����һ��nginx�ļ���
���� /usr/local/nginx/sbin��
./nginx ����
````

> ע��centos6���ܻ����error while loading shared libraries: libpcre.so.1: cannot open shared object file: No such file or directory����Ϊcentos6��/bin,/sbin,/lib,/lib64�ڸ�Ŀ¼����centos7����/usr��
> ---- ˵���� /lib64��û�и��ļ���ϵͳĬ��ȥ/lib64���ҡ�������Ҫ���������ӡ�
>
> ��ϵͳ�в��Ҷ�Ӧ���ļ�
> ---- sudo updatedb (�������ݿ⣬һ����locateһ��ʹ�ã������ǹ̶�����)
> ---- locate -b '\libpcre.so.1'�������ʮ�ֺ��ã������ҵ�ָ���ļ����ھ���·������Ȼ��Ҫϵͳ��������ļ�����
> ---- find / -name libpcre.so.1�������Ҳ���ԣ�
>
> ���������ӣ��൱��windows�Ŀ�ݷ�ʽ��
> ---- ln -s /usr/local/lib/libpcre.so.1 /lib64/




