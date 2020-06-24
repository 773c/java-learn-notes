第1步：删除Mysql的安装目录，默认为C:\Program Files目录下(如果你更改了安装路径，就到你所重新设置的路径下查找)。

第2步：删除Mysql的数据存放目录，一般在C:\ProgramData\MySQL目录下（需要注意这个文件夹默认是隐藏的，要通过查看->隐藏的项目，无论你的Mysql安装在哪一个盘下，C:\ProgramData\MySQL目录下都会有Mysql文件）。

第3步：删除注册表数据，通过cmd窗口，输入regedit，删除以下几个文件：
HKEY_LOCAL_MACHINE/SYSTEM/ControlSet001/Services/Eventlog/Applications/MySQL
HKEY_LOCAL_MACHINE/SYSTEM/ControlSet002/Services/Eventlog/Applications/MySQL
HKEY_LOCAL_MACHINE/SYSTEM/CurrentControlSet/Services/Eventlog/Applications/MySQL
