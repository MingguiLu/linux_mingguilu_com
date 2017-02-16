+++
banner = ""
categories = ["ubuntu"]
date = "2017-02-16T09:52:59+08:00"
images = []
menu = ""
tags = ["ubuntu16.04","LAMP","Apache","MariaDB","PHP"]
title = "Ubuntu搭建LAMP-基于Apt包管理器"

+++

###  １. Web服务Apache2

#### 安装Apache2

	$ sudo apt install -y apache2

#### 查看apache2版本

	$ sudo apache2  -v
	Server version: Apache/2.4.18 (Ubuntu)
	Server built:   2016-07-14T12:32:26

#### 启动apache2.service

	$ sudo systemctl start apache2.service

#### 停止apache2.service

	$ sudo systemctl stop apache2.service

#### 查看apache2.service状态

	$ sudo systemctl status apache2.service

#### 在浏览器测试访问

	http://server-ip

![](/images/170216_01_01_01.png)

###  2. 数据库服务MariaDB

#### 安装MariaDB

	$ sudo apt install -y mariadb-server mariadb-client

#### 设置MariaDB root用户密码

	$ sudo mysql_secure_installation

	Enter current password for root (enter for none):
	＃ 输入当前的root密码：没有就直接回车
	Set root password? [Y/n] y
	＃是否设置密码：是
	New password:
	＃设置密码
	Re-enter new password:
	＃再次确认密码
	Remove anonymous users? [Y/n] y
	＃ 是否移除匿名用户：是
	Disallow root login remotely? [Y/n] y
	＃ 禁止root用户远程登录：是
	Remove test database and access to it? [Y/n] y
	＃ 是否删除默认创建的test数据库，并清除所有对test数据的权限设置：是
	Reload privilege tables now? [Y/n] y
	＃ 是否重新加载权限表：是

	All done!  If you've completed all of the above steps, your MariaDB
	installation should now be secure.

	Thanks for using MariaDB!

#### 测试登录MariaDB

	$ sudo mysql -uroot -p  或  $ sudo mysql -u root -p

	Enter password:
	Welcome to the MariaDB monitor.  Commands end with ; or \g.
	Your MariaDB connection id is 59
	Server version: 10.0.29-MariaDB-0ubuntu0.16.04.1 Ubuntu 16.04

	Copyright (c) 2000, 2016, Oracle, MariaDB Corporation Ab and others.

	Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

	MariaDB [(none)]>

#### 启动MariaDB

	$ sudo systemctl start mysql-service

#### 停止MariaDB

	$ sudo systemctl stop mysql-service

#### 查看MariaDB

	$ sudo systemctl status mysql.service

### 3. 脚本语言PHP

#### 安装PHP7

	$ sudo apt install -y php7.0 php7.0-mysql php7.0-curl php7.0-json php7.0-cgi libapache2-mod-php7.0

#### 查看php版本

	$ sudo php -v

	PHP 7.0.13-0ubuntu0.16.04.1 (cli) ( NTS )
	Copyright (c) 1997-2016 The PHP Group
	Zend Engine v3.0.0, Copyright (c) 1998-2016 Zend Technologies
	    with Zend OPcache v7.0.13-0ubuntu0.16.04.1, Copyright (c) 1999-2016, by Zend Technologies

#### 创建php探针

	$ sudo vim /var/www/html/test.php

	$ sudo cat /var/www/html/test.php
	<?php
	    phpinfo();
	?>

#### 在浏览器测试php探针文件

	http://server-ip/test.php

![](/images/170216_01_03_01.png)

### 4. phpMyAdmin

#### 安装phpMyAdmin

	$ sudo apt install -y phpmyadmin

选择运行phpMyAdmin的web服务

![](/images/170216_01_04_01.png)

确认配置phpMyAdmin管理的数据库

![](/images/170216_01_04_02.png)

设置phpMyAdmin的密码

![](/images/170216_01_04_03.png)

#### 在浏览器测试访问phpMyAdmin

	http://server-ip/phpmyadmin

![](/images/170216_01_04_04.png)

#### 登录phpMyAdmin

用户名：phpmyadmin

密码：（上面安装时设置的phpmyadmin的密码）

![](/images/170216_01_04_05.png)

登录成功，进入phpMyAdmin的web界面

![](/images/170216_01_04_06.png)

<br />

#### 注意：如果使用root用户和密码登录phpMyAdmin,则会报错：

**待解决**：为什么root无法登录？因为禁止了root用户远程登录数据库？该怎么处理？

![](/images/170216_01_04_07.png)  

<br />

 至此，已成功在Ubuntu 16.04 LTS上搭建LAMP。

<br />

*参考链接*  
[在 Ubuntu Server 16.04 LTS 上安装 LAMP](https://linux.cn/article-7463-1.html)
