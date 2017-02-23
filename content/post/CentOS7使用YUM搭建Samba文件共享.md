+++
date = "2017-02-23T21:14:26+08:00"
title = "CentOS7使用YUM搭建Samba文件共享"
menu = ""
categories = ["CentOS"]
tags = ["CentOS7","Samba","文件共享"]
images = []
banner = ""

+++

### 1. 关于Samba

Samba用于在Linux、UNIX和Windows之间提供安全、稳定、快速的文件和打印服务。

Samba所需的软件包括：

* Samba  服务器端软件包
* Samba-client  客户端软件包
* Samba-common  Samba公共文件软件包

Samba有smbd和nmbd两个守护进程组成，两个进程的而启动脚本是独立的：

* smbd服务进程  为客户端提供文件共享和打印机服务，还负责用户权限验证以及锁功能，默认监听端口TCP协议的139和445

* nmbd服务进程  提供NetBIOS名称服务，以满足基于Common Internet File System (CIFS)协议的共享访问，默认使用UDP协议的137端口

### 2. 关闭防火墙和SELinux

CentOS7防火墙与SELinux默认是开启状态，并拒绝远程用户对Samba的访问，需要暂时关闭。

查看防火墙状态

	# systemctl status firewalld.service
	● firewalld.service - firewalld - dynamic firewall daemon
	   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
	   Active: active (running) since 二 2016-10-18 00:23:15 CST; 38min ago
	 Main PID: 843 (firewalld)
	   CGroup: /system.slice/firewalld.service
	           └─843 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

	10月 18 00:23:15 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall daemon.

查看SELinux状态

	# getenforce
	Enforcing

临时关闭SELinux

### 3. 配置Samba服务器

安装Samba软件

	# yum install -y samba

	# rpm -qa | grep samba
	samba-common-4.4.4-12.el7_3.noarch
	samba-client-libs-4.4.4-12.el7_3.x86_64
	samba-common-tools-4.4.4-12.el7_3.x86_64
	samba-4.4.4-12.el7_3.x86_64
	samba-common-libs-4.4.4-12.el7_3.x86_64
	samba-libs-4.4.4-12.el7_3.x86_64

启动smb和nmb服务

	# systemctl start smb.service
	# systemctl start nmb.service

停止smb和nmb服务

	# systemctl stop smb.service
	# systemctl stop nmb.service

查看smb和nmb服务状态

	# systemctl status smb.service
	# systemctl status nmb.service

设置smb和nmb服务器自启动

	# systemctl enable smb.service
	# systemctl enable nmb.service

### 4. 创建共享目录

假设目前有设计、开发和运维三个部门，各自创建一个部门共享目录供部门内部共享文件，和一个公共共享目录供部门间互相共享文件：

	# mkdir -p /share/{design,develop,ops,share/{desing,develop,ops}}

	# tree /share/
	/share/
	├── design
	├── develop
	├── ops
	└── share
	    ├── desing
	    ├── develop
	    └── ops
	7 directories, 0 files

### 5. 创建Samba账户

在使用useradd创建系统账户时使用参数 -s /sbin/nologin 指定账户无法登陆系统，提高账户安全性

Smaba共享使用的账户就是服务器端操作系统中真实存在的系统账户，但必须使用smbpasswd命令添加为Samba账户并设置独立的密码，而不可以使用系统密码

Samba默认将账户与密码文件存放在/var/lib/samba/private目录下

假设每个部门创建2个Samba账户，并加入对应部门的组中：

	# groupadd design
	# useradd design1 -g design -s /sbin/nologin -M
	# useradd design2 -g design -s /sbin/nologin -M
	# smbpasswd -a design1
	# smbpasswd -a design2

	# groupadd develop
	# useradd develop1 -g develop -s /sbin/nologin -M
	# useradd develop2 -g develop -s /sbin/nologin -M
	# smbpasswd -a develop1
	# smbpasswd -a develop2

	# groupadd ops
	# useradd ops1 -g ops -s /sbin/nologin -M
	# useradd ops2 -g ops -s /sbin/nologin -M
	# smbpasswd -a ops1
	# smbpasswd -a ops2

### 6. 修改配置文件

Samba默认配置文件：/etc/samba/smb.conf

好习惯说三遍：备份配置文件！备份配置文件！备份配置文件！

配置解析：

[global]  																	 # 定义全局策略
        workgroup = SAMBA										 # 定义工作组
        security = user											 
				interfaces = lo 192.168.80.101/24		 # 指定samba监听哪些网络端口
				hosts allow = 127. 192.168.80. EXCEPT 192.168.80.250  # 指定有权访问Samba服务器资源的主机，与之相反的是hosts deny，使用EXCEPT可以指定例外的IP地址
				log_file = /var/log/samba/log.%m     # 指定日志文件，每个访问主机会独立产生日志
				max log size = 50										 # 定义单个日志文件最大容量为50kb

        passdb backend = tdbsam							 # 定义客户端访问Samba的方式
				# user表示使用用户名和密码验证身份
				# share表示匿名访问
				# server表示验证身份的访问，但账户信息保存在另一台smb服务器上
				# domain也表示验证身份的访问，但账户信息保存在活动目录中
				guest account = nobody              # 设置匿名账户为nobody

				deadtime = 10                       # 连接回话自动关闭的期限，在大量并发访问环境中可提高服务器性能
				max connections = 0                 # 设置最大连接数，0表示无限制

        printing = cups											# ？？？
        printcap name = cups                # ？？？
        load printers = yes									# 是否共享打印机
        cups options = raw									# 打印属性

[share]																			# 共享名称
        comment = Home Directories          # 共享描述信息
				path = /share                       # 共享路径
        valid users = design1 @develop 			# 有效账户列表
        browseable = No						          # 共享目录是否对所有人可见（yes|no）
				read only = No											# 只读权限（yes|no）
				writeable = no											# 是否可写（yes|no）
				write list = design1 @develop 			# 写权限账户或组列表
				create mask = 0744									# 客户端上传文件的默认权限
				directory mask = 0755	              # 客户端创建目录的默认权限
				invalid users = root bin						# 禁止root与bin访问share共享
        inherit acls = Yes									# 继承访问控制列表（yes|no） ？？？
				guest ok = no     									# 是否允许匿名访问，仅当全局设置security=share时有效（yes|no）
				？？？																# 是否启用共享
