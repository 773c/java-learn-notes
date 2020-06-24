1.首先是环境变量的配置（不用配置CLASSPATH变量）

   ① 新建系统变量  变量名：JAVA_HOME  

   ② 寻找 Path 变量  

在cmd下输入【java -version】后如果不成功:

   ① 找到控制面板，查看方式选择大图标或者小图标，找到java选项的用户栏并点击进入。启用你想用的JDK版本，取消其他的。

   ② 如果还是不行：将对应版本JDK安装目录bin里面的java.exe,javaw.exe,javaws.exe复制到C:\Windows\System32和C:\ProgramData\Oracle\Java\javapath目录下（这是一个隐藏路径）

   ③ 如果依然不行：修改注册表（针对安装版）

运行regedit

找到HKEY_LOCAL_MACHINE\SOFTWARE\JavaSoft

修改Java Development Kit的CurrentVersion默认值为1.7

修改Java Runtime Environment的CurrentVersion默认版本为1.7
