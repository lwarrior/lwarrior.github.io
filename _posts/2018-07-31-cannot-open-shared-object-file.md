---
layout: post
title:  "error while loading shared libraries: libstdc++.so.6: cannot open shared object file: No such file or directory"
date:   2018-07-31 14:00:00 +0800
categories: Linux
---
环境：CentOS 6 x86_64

在64位系统中安装32位的安装包，经常会遇到类似的问题，以下是解决办法。
### 1.执行命令
yum whatprovides libstdc++.so.6

会提示这个库文件包含在哪个rpm包里；

有类似如下的输出

Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
libstdc++-4.4.7-11.el6.i686 : GNU Standard C++ Library
Repo        : base
Matched from:
Other       : libstdc++.so.6

### 2.安装rpm包
yum -y install libstdc++-4.4.7-11.el6.i686
