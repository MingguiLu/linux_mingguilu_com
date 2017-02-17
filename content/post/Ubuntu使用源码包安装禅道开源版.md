+++
banner = ""
categories = ["Ubuntu"]
date = "2017-02-17T22:48:12+08:00"
images = []
menu = ""
tags = ["Ubuntu16.04","禅道","ZenTao"]
title = "Ubuntu使用源码包安装禅道开源版"

+++

### 1. 搭建LAMP环境
上一篇文档，已经[在Ubuntu server 16.04 上使用apt搭建了LAMP环境](http://linux.mingguilu.com/2017/02/16/ubuntu%E4%BD%BF%E7%94%A8apt%E6%90%AD%E5%BB%BAlamp/)，本文在此基础上使用源码包安装[禅道9.0.1开源版](http://www.zentao.net/download/80025.html)

### ２. 下载并解压[禅道源码包](http://dl.cnezsoft.com/zentao/9.0.1/ZenTaoPMS.9.0.1.zip)

####  安装zip、unzip软件

	sudo apt install -y zip unzip

####　下载禅道9.0.1开源版源码包

	wget http://dl.cnezsoft.com/zentao/9.0.1/ZenTaoPMS.9.0.1.zip

#### 解压缩源码包

	unzip ZenTaoPMS.9.0.1.zip

#### 将解压后的源码目录拷贝到apache网站根目录

	sudo cp -rvf zentaopms/ /var/www/html/

	$ ls /var/www/html/zentaopms/
	bin  config  db  doc  framework  lib  module  tmp  VERSION  www

###  ３. 安装禅道

#### 在浏览器访问 http://server-ip/zentaopms/www/index.php，进入安装页面

![](/images/170217_01_03_01.png)

#### 点击“开始安装”，阅读并同意授权协议

![](/images/170217_01_03_02.png)

#### 系统检查，两项未通过

![](/images/170217_01_03_03.png)

#### 根据图提示修复临时文件目录和上传文件目录的权限

	chmod o=rwx -R /var/www/html/zentaopms/tmp/

	chmod o=rwx -R /var/www/html/zentaopms/www/data

#### 刷新系统检查，全部通过
![](/images/170217_01_03_04.png)

#### 输入数据库root密码，生成配置文件

![](/images/170217_01_03_05.png)

#### 保存配置文件

![](/images/170217_01_03_06.png)

根据提示拷贝文本框中的配置内容，保存到" /var/www/html/zentaopms/config/my.php "中

	$ sudo vim /var/www/html/zentaopms/config/my.php

	$ cat /var/www/html/zentaopms/config/my.php
	<?php
	$config->installed       = true;
	$config->debug           = false;
	$config->requestType     = 'GET';
	$config->db->host        = '127.0.0.1';
	$config->db->port        = '3306';
	$config->db->name        = 'zentao';
	$config->db->user        = 'root';
	$config->db->password    = 'passwd123!';
	$config->db->prefix      = 'zt_';
	$config->webRoot         = getWebRoot();
	$config->default->lang   = 'zh-cn';

#### 设置帐号

![](/images/170217_01_03_07.png)

#### 安装成功

![](/images/170217_01_03_08.png)

#### 正式登录禅道管理系统之前，为了安全起见，必须删除install.php和upgrade.php

![](/images/170217_01_03_09.png)

	$ pwd
	/var/www/html/zentaopms/www/

	$ sudo rm -rvf install.php upgrade.php
	已删除'install.php'
	已删除'upgrade.php'

#### 登录禅道管理系统

![](/images/170217_01_03_10.png)

#### 登录成功

![](/images/170217_01_03_11.png)

<br />

### 常见问题FQA

#### １.  在“生成配置文件”时，出现如下报错：

![](/images/170217_01_FQA_01.png)

解决方法：

可能是由于禁止了MySql或ＭriaDB的root用户远程登录，需要允许root用户远程登录

* 改表法：更改“mysql”数据库里的“user”表里的“host”项，从“localhost”改为“%”，并刷新权限，让修改生效

	$ mysql -uroot -p  

	MariaDB [(none)]> use mysql;  
	Reading table information for completion of table and column names  
	You can turn off this feature to get a quicker startup with -A  
	Database changed  
	<br />
	MariaDB [mysql]> select user,host from user;  
	+------------+-----------+  
	| user       | host      |  
	+------------+-----------+  
	| phpmyadmin | localhost |  
	| root       | localhost |  
	+------------+-----------+  
	2 rows in set (0.00 sec)  
	<br />
	MariaDB [mysql]> update user set host = '%' where user = 'root' and host = 'localhost';  
	＃更改“mysql”数据库里的“user”表里的“host”项，从“localhost”改为“%”
　
	Query OK, 1 row affected (0.00 sec)  
	Rows matched: 1  Changed: 1  Warnings: 0  
	<br />
	MariaDB [mysql]> flush privileges;      　
	＃ 刷新权限，让修改生效  
	Query OK, 0 rows affected (0.00 sec)  
	<br />
	MariaDB [mysql]> select user,host from user;  
	+------------+-----------+  
	| user       | host      |  
	+------------+-----------+  
	| root       | %         |  
	| phpmyadmin | localhost |  
	+------------+-----------+  
	2 rows in set (0.00 sec)  

<br />

#### 2.  “保存配置文件”后，安装页面变成空白，无法设置帐号

解决方法：

可能是将配置内容保存到了/var/www/html/zentaopms/config/config.php，且保存时，没有去掉第一行`<?php`，导致配置文件出错。注意查看config.php文件第一行，就是`<?php`。

默认情况下/var/www/html/zentaopms/config/my.php是不存在的，可以先`sudo touch my.php`或直接`sudo vim my.php`，再将配置内容保存进去。

<br />

*参考文档*

* [使用源码包安装(各系统通用)禅道](http://www.zentao.net/book/zentaopmshelp/101.html)
