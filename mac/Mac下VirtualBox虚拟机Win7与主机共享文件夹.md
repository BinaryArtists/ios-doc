## Mac下VirtualBox虚拟机Win7与主机共享文件夹

[原文链接](http://blog.csdn.net/jlminghui/article/details/47193027)

1.启动virtualbox，选中虚拟机，点击设置，点击共享文件夹；

2.点击右侧按钮添加共享文件夹；

3.选择共享文件夹路径（mac路径），自动挂载，固定分配两个勾选。

4.VBoxGuestAdditions_5.0.0.iso下载地址

http://download.virtualbox.org/virtualbox/5.0.0/

5.启动VirtualBox，启动win7虚拟机，点击菜单Devices，点击Optical Devices 》Choose Disk image 找到刚下载的iso文件，运行安装。

6.在win7虚拟机里面我的电脑右键，选择“映射网络驱动器”，点击“浏览“选择共享文件夹，找到之前共享的文件夹即可实现文件共享。
