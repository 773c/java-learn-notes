##### һ��shiri����������

* securityManager����������Ǳ���ġ�
* loginUrl ��û�е�¼���û�������Ҫ��¼��ҳ��ʱ�Զ���ת����¼ҳ�棬���Ǳ�������ԣ��������ַ�Ļ����Զ�Ѱ����Ŀweb��Ŀ�ĸ�Ŀ¼�µġ�/login.jsp��ҳ�档
* successUrl ����¼�ɹ�Ĭ����תҳ�棬����������ת����/���������½ǰ�����һ����Ҫ��¼��ҳ�棬���ڵ�¼�Զ���ת���Ǹ���Ҫ��¼��ҳ�档����ת���ˡ�
* unauthorizedUrl ��û��Ȩ��Ĭ����ת��ҳ��

##### �������޹���������������

* anon:����/admins/**=anon û�в�������ʾ��������ʹ�á�
* authc:����/admins/user/**=authc��ʾ��Ҫ��֤(��¼)����ʹ�ã�û�в���
* roles(��ɫ)������/admins/user/** =roles[admin],��������д��������ʱ����������ţ����Ҳ���֮���ö��ŷָ���ж������ʱ������admins/user/**=roles["admin,guest"],ÿ������ͨ������ͨ�����൱��hasAllRoles()������
* perms��Ȩ�ޣ�������/admins/user/** =perms[user:add:*],��������д��������ʱ����������ţ����Ҳ���֮���ö��ŷָ����/admins/user/**=perms["user:add:*,user:modify:*"]�����ж������ʱ����ÿ��������ͨ����ͨ�����뵱��isPermitedAll()������
* rest������/admins/user/** =rest[user],��������ķ������൱��/admins/user/**=perms[user:method] ,����methodΪpost��get��delete�ȡ�
* port������/admins/user/**=port[8081],�������url�Ķ˿ڲ���8081����ת��schemal://serverName:8081?queryString,����schmal��Э��http��https�ȣ�serverName������ʵ�host,8081��url������port�Ķ˿ڣ�queryString������ʵ�url��ģ�����Ĳ�����
* authcBasic������/admins/user/**=authcBasicû�в�����ʾhttpBasic��֤
* ssl:����/admins/user/**=sslû�в�������ʾ��ȫ��url����Э��Ϊhttps
* user:����/admins/user/**=userû�в�����ʾ��������û������������ʱ�������