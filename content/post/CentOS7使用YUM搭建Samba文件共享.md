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

	# setenforce 0

永久关闭SELinux

	# sed -i "/SELINUX=/c SELINUX=disable" /etc/sysconfig/selinux

临时关闭防火墙

	# systemctl stop firewalld.service

永久关闭防火墙

	# systemctl disable firewalld.service
	Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
	Removed symlink /etc/systemd/system/basic.target.wants/firewalld.service.

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

重启smb和nmb服务

	# systemctl restart smb.service
	# systemctl restart nmb.service

查看smb和nmb服务状态

	# systemctl status smb.service
	# systemctl status nmb.service

设置smb和nmb服务器自启动

	# systemctl enable smb.service
	# systemctl enable nmb.service

### 4. 创建共享目录

假设目前有设计、开发、运维三个部门，各自创建一个部门共享目录供部门内部共享文件，和一个公共共享目录供部门间互相共享文件：

	# mkdir -p /filesrv/{design,develop,ops,share/{design,develop,ops}}

	# tree /filesrv/
	/filesrv/
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

配置文件解析：

	[global]																					# 定义全局策略
		workgroup = SAMBA																# 定义工作组									 
		interfaces = lo 192.168.80.101/24								# 指定samba监听哪些网络端口
		hosts allow = 127. 192.168.80. EXCEPT 192.168.80.250  			# 指定有权访问Samba服务器资源的主机，与之相反的是hosts deny，使用EXCEPT可以指定例外的IP地址
		log_file = /var/log/samba/log.%m    						# 指定日志文件，每个访问主机会独立产生日志
		max log size = 50											       		# 定义单个日志文件最大容量为50kb

		security = user																	# 设置客户端访问Samba的方式
		# user表示使用用户名和密码验证身份
		# share表示匿名访问
		# server表示验证身份的访问，但账户信息保存在另一台smb服务器上
		# domain也表示验证身份的访问，但账户信息保存在活动目录中

		passdb backend = tdbsam							 			      # 账户与密码的存储方式
		# smbpasswd表示老的明文格式存储账户及密码
		# tdbsam表示基于TDB的密文格式存储
		# ldapsam表示用LDAP存储账户资料

		guest account = nobody              						# 设置匿名账户为nobody
		deadtime = 10# 连接回话自动关闭的期限，在大量并发访问环境中可提高服务器性能
		max connections = 0                 						# 设置最大连接数，0表示无限制

		printing = cups																	# 设置打印工具为cups ?? http://yuanbin.blog.51cto.com/363003/115768/
		printcap name = cups               							# ？？？
		load printers = yes															# 是否共享打印机
		cups options = raw															# 打印属性

	[filesrv]																					# 共享名称
			comment = Home Directories          					# 共享描述信息
			path = /filesrv                      					# 共享路径
			browseable = no						          					# 是否对所有人可见（yes|no）
			public = no																		# 是否被所有人可读(yes|no)
			read only = No																# 是否只读（yes|no）
			writeable = no																# 是否可写（yes|no）
			write list = 	 																# 可写用户或组列表
			create mask = 0744														# 客户端上传文件的默认权限
			directory mask = 0755	             						# 客户端创建目录的默认权限
			valid users =																	# 白名单
			invalid users = 															# 黑名单
			inherit acls = Yes														# 继承访问控制列表（yes|no）
			guest ok = no     														# 是否允许匿名访问，仅当全局设置security=share时有效（yes|no）
			available = yes																# 是否启用该共享(yes|no)

假设每个Samba目录都对自己部门的共享目录可读可写，所有的Samba用户都对share目录可读可写

	# cat smb.conf

	[global]
		workgroup = SAMBA
		security = user
		passdb backend = tdbsam
		printing = cups
		printcap name = cups
		load printers = yes
		cups options = raw

	[homes]
		comment = Home Directories
		valid users = %S, %D%w%S
		browseable = No
		read only = No
		inherit acls = Yes
		available = no   							# 关闭用户家目录共享

	[printers]
		comment = All Printers
		path = /var/tmp
		printable = Yes
		create mask = 0600
		browseable = No

	[print$]
		comment = Printer Drivers
		path = /var/lib/samba/drivers
		write list = root
		create mask = 0664
		directory mask = 0775

	[design]
		comment = design share
		path = /filesrv/design
		write list = @design				

	[develop]
		comment = develop share
		path = /filesrv/develop
		write list = @develop

	[ops]
		comment = ops share
		path = /filesrv/ops
		write list = @ops

	[share]
		comment = public share
		path = /filesrv/share
		write list = @design @develop @ops
		create mask = 0775         # 创建文件的用户和所属组拥有可读写执行权限，其它用户可读可执行
		directory mask = 0775      # 创建目录的用户和所属组拥有可读写执行权限，其它用户可读可执行

### 7. 修改共享目录权限

除了要在Samba主配置文件中定义权限外，还需要为系统目录设置正确的权限

	# chmod 1770 /filesrv/{design,develop,ops,share}
	# chmod 1777 /filesrv/share -R
	# chown :design /filesrv/design
	# chown :develop /filesrv/develop
	# chown :ops /filesrv/ops

### 8. 重启Samba服务

	# systemctl restart smb.service

### 9. 访问Samba共享

* Windows客户端访问:`\\Samba-server-ip`

![](/images/170223_01_09_01.png)

使用用户ops1验证，可见四个共享目录，但只有ops和share可访问

![](/images/170223_01_09_03.png)

无权限访问design和develop目录

![](/images/170223_01_09_02.png)

* Linux客户端访问

查看Samba主机上的共享信息

	# smbclient -L \\192.168.80.102
	Enter root's password:          # 仅查看共享信息不需要密码，此处直接回车，以匿名用户查看
	Anonymous login successful
	Domain=[SAMBA] OS=[Windows 6.1] Server=[Samba 4.4.4]

		Sharename       Type      Comment
		---------       ----      -------
		print$          Disk      Printer Drivers
		design          Disk      design share
		develop         Disk      develop share
		ops             Disk      ops share
		share           Disk      public share
		IPC$            IPC       IPC Service (Samba 4.4.4)
	Anonymous login successful
	Domain=[SAMBA] OS=[Windows 6.1] Server=[Samba 4.4.4]

		Server               Comment
		---------            -------
		LOCALHOST            Samba 4.4.4

		Workgroup            Master
		---------            -------
		SAMBA                LOCALHOST
		WORKGROUP            IMKINDU-PC

访问Samba共享目录

	# smbclient -U develop2 //192.168.80.102/develop
	Enter develop2's password:
	Domain=[SAMBA] OS=[Windows 6.1] Server=[Samba 4.4.4]
	smb: \>
	smb: \> ls
	  .                                   D        0  Sat Feb 25 05:54:45 2017
	  ..                                  D        0  Fri Feb 24 18:36:11 2017
	  test                                D        0  Sat Feb 25 05:44:55 2017
	  test1.txt                           N        0  Sat Feb 25 05:54:40 2017
	  test2.md                            N        0  Sat Feb 25 05:54:40 2017
	  test3                               D        0  Sat Feb 25 05:54:45 2017

			39294212 blocks of size 1024. 38295904 blocks available

---

### 常见问题FQA

#### 1. 安装了samba-client，但使用`smbclient`命令时如下报错：

	# smbclient -L \\192.168.80.102
	smbclient: relocation error: /lib64/libsamba-credentials.so.0: symbol GSS_KRB5_CRED_NO_CI_FLAGS_X, version gssapi_krb5_2_MIT not defined in file libgssapi_krb5.so.2 with link time reference       # 缺失samba软件

解决方法：安装samba软件

	# yum install -y samba

#### 2. 查看Samba主机共享信息时，有报错：

	# smbclient -L \\192.168.80.102
	Enter root's password:
	session setup failed: NT_STATUS_LOGON_FAILURE     # 登陆失败，一般为账户或密码错误

解决方法：仅查看共享信息不需要密码，此处直接回车，以匿名用户查看

#### 3. 访问Samba共享目录时，有报错

	# smbclient -U develop2 //192.168.80.102
	\\192.168.80.102: Not enough '\' characters in service    # 共享路径输入有误，一般使用//ip格式访问时会报错

解决：按照正确的格式输入共享路径 `//Samba-server-ip/share-name`

#### 4. 访问Samba共享目录时，有报错：

	# smbclient -U develop2 //192.168.80.102/filesrv
	Enter develop2's password:
	Domain=[SAMBA] OS=[Windows 6.1] Server=[Samba 4.4.4]
	tree connect failed: NT_STATUS_BAD_NETWORK_NAME    # 输入性错误，一般为输入了错误的共享名称

解决方法：检查并修正共享名称，比如本案中虽然各部门共享目录存放在filesrv目录中，但Samba配置文件中并未将filesrv设置为共享目录

	# smbclient -U develop2 //192.168.80.102/develop

#### 5. 访问共享目录时，有报错：

	# smbclient -U develop2 //192.168.80.102/develop
	Enter develop2's password:
	Connection to 192.168.80.102 failed (Error NT_STATUS_HOST_UNREACHABLE)   	# 无法连接Samba服务器，一般为网络故障或防火墙策略造成

解决方法：排除网络连接问题，调整防火墙策略，或关闭防火墙

#### 6. 访问共享目录是，有报错：

	# smbclient -U develop2 //192.168.80.102/ops
	Enter develop2's password:
	Domain=[SAMBA] OS=[Windows 6.1] Server=[Samba 4.4.4]
	smb: \>
	smb: \> ls
	NT_STATUS_ACCESS_DENIED listing \*     # 访问被拒绝，权限不足
	smb: \> mkdir test
	NT_STATUS_MEDIA_WRITE_PROTECTED making remote directory \test  # 写保护，权限不足

解决方法： 检查Samba目录的共享权限，或服务器文件系统的访问权限，是否不允许客户端或用户访问

---
*参考文档*

* 《Linux运维之道（第二版）》 - Samba文件共享 P176~189
