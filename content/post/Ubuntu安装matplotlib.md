+++
banner = ""
categories = ["ubuntu"]
date = "2017-03-16T15:52:59+08:00"
images = []
menu = ""
tags = ["ubuntu16.04","python","matplotlib","numpy","pyplot"]
title = "Ubuntu安装matplotlib"

+++

**[Matplotlib](http://matplotlib.org/)**是一个 Python 的 2D绘图库，它以各种硬拷贝格式和跨平台的交互式环境生成出版质量级别的图形。通过 Matplotlib，开发者可以仅需要几行代码，便可以生成绘图，直方图，功率谱，条形图，错误图，散点图等

### 在Ubuntu16.04安装Matplotlib

#### 1. 如果使用Ubuntu16.04系统自带Python3版本：

	$ pytho3 --version
	Python 3.5.1+
	
	$ sudo apt install python3-matplotlib
	正在读取软件包列表..	. 完成
	正在分析软件包的依赖关系树       
	正在读取状态信息... 完成       
	将会同时安装下列软件：
	  binutils blt cpp-5 fonts-lyx g++-5 gcc-5 gcc-5-base javascript-common libasan2 libatomic1 libblas-common libblas3 libcc1-0 libcilkrts5 libgcc-5-dev libgfortran3
	  libgomp1 libitm1 libjs-jquery libjs-jquery-ui liblapack3 liblsan0 libmpx0 libquadmath0 libstdc++-5-dev libstdc++6 libtsan0 libubsan0 python-matplotlib-data
	  python3-cycler python3-dateutil python3-numpy python3-tk python3-tz tk8.6-blt2.5 ttf-bitstream-vera
	建议安装：
	  binutils-doc blt-demo gcc-5-locales g++-5-multilib gcc-5-doc libstdc++6-5-dbg gcc-5-multilib libgcc1-dbg libgomp1-dbg libitm1-dbg libatomic1-dbg libasan2-dbg
	  liblsan0-dbg libtsan0-dbg libubsan0-dbg libcilkrts5-dbg libmpx0-dbg libquadmath0-dbg apache2 | lighttpd | httpd libjs-jquery-ui-docs libstdc++-5-doc dvipng ffmpeg
	  inkscape ipython3 python-matplotlib-doc python3-cairocffi python3-gobject python3-nose python3-pyqt4 python3-scipy python3-sip python3-tornado texlive-extra-utils
	  texlive-latex-extra ttf-staypuft gfortran python-numpy-doc python3-dev python3-numpy-dbg tix python3-tk-dbg
	下列【新】软件包将被安装：
	  blt fonts-lyx javascript-common libblas-common libblas3 libgfortran3 libjs-jquery libjs-jquery-ui liblapack3 python-matplotlib-data python3-cycler python3-dateutil
	  python3-matplotlib python3-numpy python3-tk python3-tz tk8.6-blt2.5 ttf-bitstream-vera
	下列软件包将被升级：
	  binutils cpp-5 g++-5 gcc-5 gcc-5-base libasan2 libatomic1 libcc1-0 libcilkrts5 libgcc-5-dev libgomp1 libitm1 liblsan0 libmpx0 libquadmath0 libstdc++-5-dev
	  libstdc++6 libtsan0 libubsan0
	升级了 19 个软件包，新安装了 18 个软件包，要卸载 0 个软件包，有 480 个软件包未被升级。
	
#### 2. 如果使用Python2.7

	$ python --version
	$ Python 2.7.11+
	
	sudo apt install python-matplotlib
	
#### 3. 如果安装了较新的python版本，就必须安装matplotlib依赖的一些库：

	$ python3 --version
	Python 3.5.2

	$ sudo apt install python3.5-dev python3.5-tk tk-dev
	$ sudo apt install libfreetype6-dev g++
	
	$ pip install --user matplotlib
	# 安装包时可能需要使用pip3，而不是pip。另外，如果这个命令不管用，可能需要删除标志`--user`	
	
	Collecting matplotlib
	  Downloading matplotlib-2.0.0-1-cp27-cp27mu-manylinux1_x86_64.whl (14.6MB)
	    100% |████████████████████████████████| 14.6MB 58kB/s 
	Collecting numpy>=1.7.1 (from matplotlib)
	  Downloading numpy-1.12.0-cp27-cp27mu-manylinux1_x86_64.whl (16.5MB)
	    100% |████████████████████████████████| 16.5MB 70kB/s 
	Collecting cycler>=0.10 (from matplotlib)
	  Downloading cycler-0.10.0-py2.py3-none-any.whl
	Collecting python-dateutil (from matplotlib)
	  Downloading python_dateutil-2.6.0-py2.py3-none-any.whl (194kB)
	    100% |████████████████████████████████| 194kB 135kB/s 
	Collecting functools32 (from matplotlib)
	  Downloading functools32-3.2.3-2.zip
	Requirement already satisfied: six>=1.10 in ./.local/lib/python2.7/site-packages (from matplotlib)
	Collecting pytz (from matplotlib)
	  Downloading pytz-2016.10-py2.py3-none-any.whl (483kB)
	    100% |████████████████████████████████| 491kB 252kB/s 
	Requirement already satisfied: pyparsing!=2.0.0,!=2.0.4,!=2.1.2,!=2.1.6,>=1.5.6 in ./.local/lib/python2.7/site-packages (from matplotlib)
	Collecting subprocess32 (from matplotlib)
	  Downloading subprocess32-3.2.7.tar.gz (54kB)
	    100% |████████████████████████████████| 61kB 477kB/s 
	Building wheels for collected packages: functools32, subprocess32
	  Running setup.py bdist_wheel for functools32 ... done
	  Stored in directory: /home/imkind/.cache/pip/wheels/3c/d0/09/cd78d0ff4d6cfecfbd730782a7815a4571cd2cd4d2ed6e69d9
	  Running setup.py bdist_wheel for subprocess32 ... done
	  Stored in directory: /home/imkind/.cache/pip/wheels/7d/4c/a4/ce9ceb463dae01f4b95e670abd9afc8d65a45f38012f8030cc
	Successfully built functools32 subprocess32
	Installing collected packages: numpy, cycler, python-dateutil, functools32, pytz, subprocess32, matplotlib
	Successfully installed cycler-0.10.0 functools32-3.2.3.post2 matplotlib-2.0.0 numpy-1.12.0 python-dateutil-2.6.0 pytz-2016.10 subprocess32-3.2.7


### 测试matplotlib

在python中导入matplotlib，如果没有出现任何错误消息，说明matplotlib安装成功

	$ python3
	Python 3.5.2 (default, Nov 17 2016, 17:05:23) 
	[GCC 5.4.0 20160609] on linux
	Type "help", "copyright", "credits" or "license" for more information.
	>>> 
	>>> import matplotlib
	>>> 


---
	
	*参考文档*
	
	* 《Python编程从入门到实践》-- 数据可视化 15.1 安装matplotlib --p285-287
