## 驱动开发相关问题－临时文件

### [如何安装.sys驱动文件](http://bbs.csdn.net/topics/210022789)

1. OpenSCManager 打开服务控制管理器
2. CreateService 创建一个服务，服务类型为内核驱动
3. OpenService   取得服务句柄
4. StartService  启动服务
5. ControlService停止服务
6. DeleteService 删除服务
7. CloseServiceHandle 关闭服务句柄

### [DeviceIoControl的使用说明](http://blog.csdn.net/lujunql/article/details/2532152)

应用程序和驱动程序的通信过程是：应用程序使用CreateFile函数打开设备，然后用DeviceIoControl与驱动程序进行通信，包括读和写两种操作。还可以用ReadFile读数据用WriteFile写数据。操作完毕时用CloseHandle关闭设备。我们比较常用的就是用DeviceIoControl对设备进行读写操作。

###
