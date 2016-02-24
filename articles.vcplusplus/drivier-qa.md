## 驱动开发相关问题－临时文件

### [如何安装.sys驱动文件](http://bbs.csdn.net/topics/210022789)

1. OpenSCManager 打开服务控制管理器
2. CreateService 创建一个服务，服务类型为内核驱动
3. OpenService   取得服务句柄
4. StartService  启动服务
5. ControlService停止服务
6. DeleteService 删除服务
7. CloseServiceHandle 关闭服务句柄
