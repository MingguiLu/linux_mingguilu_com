+++
banner = ""
categories = ["Ubuntu"]
date = "2017-02-18T14:17:37+08:00"
images = []
menu = ""
tags = ["禅道","zentao","zbox"]
title = "Ubuntu使用一键安装包安装禅道开源版"

+++

<br />

>相比[搭建LAMP环境](http://linux.mingguilu.com/2017/02/16/ubuntu%E4%BD%BF%E7%94%A8apt%E6%90%AD%E5%BB%BAlamp/)[使用源码包安装禅道](http://linux.mingguilu.com/2017/02/17/ubuntu%E4%BD%BF%E7%94%A8%E6%BA%90%E7%A0%81%E5%8C%85%E5%AE%89%E8%A3%85%E7%A6%85%E9%81%93%E5%BC%80%E6%BA%90%E7%89%88/)，使用Linux一键安装包则简单很多

### 1. 下载并安装[Linux 64位一键安装包](http://www.zentao.net/download/80025.html)

下载[禅道9.0.1版本Linux 64位一键安装包](http://dl.cnezsoft.com/zentao/9.0.1/ZenTaoPMS.9.0.1.zbox_64.tar.gz)

	$ wget http://dl.cnezsoft.com/zentao/9.0.1/ZenTaoPMS.9.0.1.zbox_64.tar.gz
	
直接将安装包解压缩到/opt目录下，即安装完成

	$ sudo tar -zxvf ZenTaoPMS.9.0.1.zbox_64.tar.gz -C /opt/

### 2. 使用zbox命令管理Apache和Ｍysql服务

获取zbox命令的帮助

	$ sudo /opt/zbox/zbox -h
	Usage: zbox.php {start|stop|restart|status}

	Options:
	    -h --help Show help.
	    -ap --aport Apache port, default 80　　# 修改Apache服务端口号
	    -mp --mport Mysql port, default 3306　# 修改Mysql服务端口号

开启Apache和Ｍysql服务

	$ sudo /opt/zbox/zbox start 
	Start Apache success
	Start Mysql success
	
重启Apache和Ｍysql服务

	$ sudo /opt/zbox/zbox restart
	Retart Apache success
	Retart Mysql success
 
停止Apache和Ｍysql服务

	$ sudo /opt/zbox/zbox stop
	Stop Apache success
	Stop Mysql success

查看Apache和Ｍysql服务状态

	$ sudo /opt/zbox/zbox status
	Apache is not running
	Mysql is not running
	
开启Apache和Ｍysql服务后，在浏览器访问禅道管理系统

	http://server-ip
	
![](/images/170218_01_02_01.png)

进入“开源版”登录界面，默认帐号：admin，密码：123456

![](/images/170218_01_02_02.png)

登录成功

![](/images/170218_01_02_03.png)

### 3. 访问数据库

#### 网页登录数据库

数据库管理用的是adminer，为了安全，访问adminer的时候需要身份验证，需要运行/opt/zbox/auth/adduser.sh来添加用户

* 在普通用户下

	$ sudo ./adduser.sh  
	This tool is used to add user to access adminer  
	Account: adminer1  passwd123!　　# 设置用户名和密码  
	./adduser.sh: 3: read: Illegal option -s  
	Adding password for user adminer1  

* 在root用户下

	＃ cd /opt/zbox/auth/  
	＃ ./adduser.sh   
	This tool is used to add user to access adminer  
	Account: adminer2　　＃ 设置用户名  
	Password: 　　　　　＃ 设置密码(隐式)  
	Adding password for user adminer2

在访问禅道管理系统时，点击右下角的“数据库管理”

![](/images/170218_01_03_01.png)

adminer身份验证

![](/images/170218_01_03_02.png)

验证通过进入数据web管理台

![](/images/170218_01_03_03.png)

#### 命令行连接数据库

登录数据库，默认帐号:root，密码为空

	/opt/zbox/bin/mysql -u root -P mysql端口 -p 
	
	$ /opt/zbox/bin/mysql -uroot -p　　# 使用默认的3306端口，可省略`-P`参数
	Enter password: 　　　# 密码为空
	Welcome to the MySQL monitor.  Commands end with ; or \g.
	Your MySQL connection id is 58
	Server version: 5.5.45 Source distribution

	Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

	Oracle is a registered trademark of Oracle Corporation and/or its
	affiliates. Other names may be trademarks of their respective
	owners.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	mysql> 
	
<br />

___

### 常见问题FQA

#### 1.  如果服务器上已经搭建了LAMP环境，80端口和3306端口被占用了，无法启动

	$ sudo /opt/zbox/zbox start
	(98)Address already in use: AH00073: make_sock: unable to listen for connections on address 0.0.0.0:80
	no listening sockets available, shutting down
	AH00015: Unable to open logs
	Start Apache fail. You can see the log /opt/zbox/logs/apache_error.log
	Start Mysql fail. You can see the log /opt/zbox/logs/mysql_error.log

解决方法：

修改Apache端口

	$ sudo /opt/zbox/zbox -ap 8080

修改Mysql端口

	$ sudo /opt/zbox/zbox -mp 3308
	
在浏览器访问禅道管理系统时指定端口号

	http://server-ip:8080

<br />

___

*参考文档*

* [linux用一键安装包](http://www.zentao.net/book/zentaopmshelp/90.html)





	

