插入中文乱码：
1、****查看客户端 编码字符 状态****
mysql>status;

需要注意：client characterset：gbk；一定要是gbk，不能是utf8；

2、****查看所有的 编码字符****
mysql>show variables like'%char%';

需要注意两个地方：character_set_client 和 character_set connection ，这两个地方一定要是gbk;

3、****修改mysql为正常的编码方法****
mysql>set character_set_client=gbk;

mysql>set character_set_connection=gbk;

mysql>set character_set_database=utf8;

mysql>set character_set_server=utf8;

查询中文乱码？？？：
因为DOS窗口默认是gbk，如果mysql默认为utf-8，则输入chcp 65001转为utf-8即可；

