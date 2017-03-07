+++
date = "2016-12-30T09:09:19+08:00"
title = "Linux软件安装与维护"
menu = ""
categories = ["Ubuntu","CentOS"]
tags = ["apt","apt-get","rpm","yum"]
images = []
banner = ""

+++

### 1. 使用软件源码包

### 2. Debian/Ubuntu
Debian系Linux发行版的软件包格式为deb

#### 2.1 dpkg
dpkg的是基于Debian系统的一个低级包管理器。它可以安装，删除，提供有关资料，以及建立*.deb包，但它不能自动下载并安装它们相应的依赖包。

查看帮助

	dpkg --help

查看指定软件包的信息

	dpkg -I 下载/Software/vivaldi-stable_1.6.689.46-1_amd64.deb

	#以Vivaldi浏览器deb安装包为例
	##必须使用软件包的完整名称

列出指定软件包里的文件目录

	dpkg -c 下载/Software/vivaldi-stable_1.6.689.46-1_amd64.deb

	#必须使用软件包的完整名称

安装deb软件包

	dpkg -i vivaldi-stable_1.6.689.46-1_amd64.deb

列出所有已安装的软件包

	dpkg -l

查询是否已安装指定软件包

	dpkg -l vivaldi-stable

	#即可看到软件名称、版本、架构、描述信息
	#注意软件名称必须正确，比如查询“vivaldi”就会提示“dpkg-query: 没有找到与 vivaldi 相匹配的软件包”
	#当不能确认完整的软件包名称，可以再输入“vivaldi”后，按“Tab”键，会列出所有相关包名称

查看已安装的软件包的详细信息

	dpkg -s vivaldi-stable

	#同上，必须指定软件名称“vivaldi-stable”，而不能用原始的软件包名称“vivaldi-stable_1.6.689.46-1_amd64.deb”

列出指定软件包安装后的所有文件

	dpkg -L vivaldi-stable

	#同上

卸载软件包（保留配置文件）

	dpkg -r vivaldi-stable

	#同上，必须指定软件名称“vivaldi-stable”，而不能用原始的软件包名称“vivaldi-stable_1.6.689.46-1_amd64.deb”

卸载软件包（删除配置文件）

	dpkg -p vivaldi-stable

	#有些软件包卸载后还会遗留一些配置文件，使用`-p`就像在windows上卸载软件时勾选上“同时清除用户配置文件”

安装指定目录及其子目录下所有的“.deb”包

	dpkg -R --install debpackages/

	# -R 参数表示递归，指定目录和子目录下所有的.deb包都将被安装

释放软件包，但不配置

	dpkg --unpack vivaldi-stable_1.6.689.46-1_amd64.deb

重新配置软件包

	dpkg --configure

替换软件包信息

	dpkg --update-avail

清除包的当前可用信息

	dpkg --clear-avail

丢弃所有不能安装和不可用软件包的信息

	dpkg --forget-old-unavail

显示版本

	dpkg --version


#### 2.2 apt-cache、apt-get
##### 2.2.1 apt-cache
apt-cache是Linux下的apt软件包管理工具，使用它能查询到apt的二进制软件包缓存文件,结合一些参数使用能查寻到软件包信息和软件包依赖关系等

列出所有软件包的名称

	apt-cache pkgnames

列出所有以指定字符开头的软件包

	apt-cache pkgnames apache2

	#以“apache2”为例

列出包含指定关键字的软件包和简介

	apt-cache search apache2

以便于阅读的格式介绍该软件包

	apt-cache show apache2

查询软件包的依赖关系

	apt-cache showpkg apache2

统计全部软件包的信息

	apt-cache stats

##### 2.2.2 apt-get
apt-get是Debian及其衍生版的高级包管理器，并提供命令行方式来从多个来源检索和安装软件包，其中包括解决依赖性。和dpkg不同的是，apt-get不是直接基于.deb文件工作，而是基于软件包的正确名称。

更新软件源（/etc/apt/source.list）

	sudo apt-get update

升级已安装的所有软件包（不管依赖性，不添加包，也不删除包）

	sudo apt-get upgrade

升级已安装的所有软件包（根据依赖关系的变化，添加包或删除包）

	sudo apt-get dist-upgrade

安装指定的软件包

	sudo apt-get install apache2

	#以“apache2”为例

安装多个软件包

	sudo apt-get install apache2 mysql-server php5

	#同时安装“apache2”、"mysql-server"、"php5"，并解决依赖性

安装包含指定字符串的软件包

	sudo apt-get install '*name*'

安装软件包但不升级

	sudo apt-get install packageName --no-upgrade

安装指定名称和版本的软件包

	sudo apt-get install apache2=2.4.18-2ubuntu3.1

卸载软件包但不清除配置文件

	sudo apt-get remove apache2

卸载软件包同时清除配置文件

	sudo apt-get purge apaceh2

或者

	sudo apt-get remove --purge apache2

清理本地软件仓库

	sudo apt-get clean

仅下载指定软件源码包

	sudo apt-get --download-only source apache2

下载指定软件包并解包

	sudo apt-get source apache2

同时下载、解包并编译软件包

	sudo apt-get --compile source apache2

仅下载不安装软件包

	sudo apt-get download apache2

查看软件包版本变更日志

	sudo apt-get changelog apache2

检查是否有损坏的依赖关系

	sudo apt-get check

建立指定软件包的编译环境

	sudo apt-get build-dep apache2

	#为手工编译软件apache2,提前把编译过程中需要用的软件包先安装配置好

将已经删除了的软件包的.deb安装文件从硬盘中删除掉

	sudo apt-get autoclean

将已经安装了的所有软件包的.deb包从硬盘中删除掉

	sudo apt-get clean

删除为了满足其他软件包的依赖而安装的，但现在不再需要的软件包

	sudo apt-get autoremove apache2
