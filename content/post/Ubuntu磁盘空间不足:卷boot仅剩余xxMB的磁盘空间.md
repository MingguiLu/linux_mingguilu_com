+++
banner = ""
categories = ["Ubuntu"]
date = "2017-02-27T08:47:45+08:00"
images = []
menu = ""
tags = ["Ubuntu16.04","Boot分区"]
title = "Ubuntu磁盘空间不足:卷boot仅剩余xxMB的磁盘空间"

+++

最近启动Ubuntu16.04进入桌面之后，总弹出"磁盘空间不足:卷boot仅剩余5.1MB的硬盘空间"的提示

![](/images/170227_01_00_01.png)

点击"分析..."之后提示"无法扫描"/boot"所包含的某些文件夹　Error opening directory '/boot/lost+found':权限不够"

![](/images/170227_01_00_02.png)

安装Ubuntu16.04系统时，分配给boot分区200MB的磁盘空间，升级系统之后，之前的内核版本依然会存在boot分区中，造成分区的空间不足。需要删除旧的Linux内核版本，保留当前使用的版本即可。

#### 1.  查看已存在的Linux-image版本

	$ sudo dpkg --get-selections |grep linux-image  
	linux-image-4.4.0-21-generic			install  
	linux-image-4.4.0-59-generic			install  
	linux-image-4.4.0-62-generic			install  
	linux-image-4.4.0-64-generic			install  
	linux-image-extra-4.4.0-21-generic		install  
	linux-image-extra-4.4.0-59-generic		install  
	linux-image-extra-4.4.0-62-generic		install  
	linux-image-extra-4.4.0-64-generic		install  
	linux-image-generic				install		

#### 2. 查看当前正在使用的Linux内核版本

	$ uname -r  
	4.4.0-62-generic
	
或

	$ cat /proc/version  
	Linux version 4.4.0-62-generic (buildd@lcy01-30) (gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.4) ) #83-Ubuntu SMP Wed Jan 18 14:10:15 UTC 2017

#### 3. 卸载其它不用的内核版本

	$ sudo apt purge linux-image-4.4.0-21-generic 　
	
	$ sudo apt purge linux-image-4.4.0-59-generic 
	
#### 4. 再次查看剩余的版本

	$ sudo dpkg --get-selections | grep linux-image  
	linux-image-4.4.0-62-generic			install  
	linux-image-4.4.0-64-generic			install  
	linux-image-extra-4.4.0-62-generic		install  
	linux-image-extra-4.4.0-64-generic		install  
	linux-image-generic				install  
	
#### 5. 查看/boot分区的空间使用情况

	$ df -lh | grep boot   
	文件系统        1K-块     已用    可用   已用%  挂载点  
	/dev/sda1      180M 	104M   64M     62%　　/boot  

---

*参考文档*

*  [Ubuntu提示卷boot仅剩0字节的硬盘空间，解决办法](http://www.linuxdiyf.com/linux/23774.html)

	
	
	
	
	
