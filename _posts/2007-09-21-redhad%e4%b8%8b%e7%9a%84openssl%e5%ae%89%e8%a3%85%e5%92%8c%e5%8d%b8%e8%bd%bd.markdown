---
layout: post
title: redhad下的openssl(安装和卸载)
author: Alvin
date: !binary |-
  MjAwNy0wOS0yMSAxODo0Nzo0NiArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wOS0yMSAxMDo0Nzo0NiArMDgwMA==
---
转至: http://blog.csdn.net/baitianhai/archive/2004/10/27/155461.aspx
最近在鼓捣redhat linux，想自己以源代码方式安装软件，不想用rpm方式安装。
首先从httpd开始，先卸载在安装倒是比较容易，不过后来像添加ssl功能，发现编译的时候需要用openssl的安装目录，本人比较愚笨，一顿好找也没有找到，于是就想把openssl也以源代码方式安装。先卸载，此时出现问题，系统好多东西依赖于openssl的库，我查了好多资料也没找到什么办法，于是我最后一狠心，用rpm -e --nodeps给卸载了，然后手动安装了openssl，然后重新启动，这下坏了，好多服务都起不来了，smb,ssh等等，图形模式也起不来了，我欲哭无泪。
因为我是在虚拟机上安装的，smb起不来了，我只能重新安装系统了。这次安装我大多数东西都没选择，一路安装完毕，结果在文本方式发现vi编辑没有颜色了，哎，也不知道是少装了那个东西弄得（各位谁知道麻烦告诉告诉我一下），只能按照猜测重新安装了又添加了一些东西。不过幸运的vi高亮显示功能又有了，遗憾的是具体是那个软件我还是不清楚。有了上次的教训我不敢轻易卸掉系统原来的openssl了，我从网上搜索到了一篇安装openssl的英文文章，地址在 http://www.devside.net/web/server/linux/openssl 我按照上面说的安装了zlib,openssl。步骤简介如下（怕以后忘了）
安装zlib
Home : http://www.gzip.org/zlib/
Package(linux source) : http://www.gzip.org/zlib/
Our Configuration
Install to : /usr/local
Module types : dynamically and staticly loaded modules, *.so and *.a
Build Instructions
zlib library files are placed into /usr/local/lib and zlib header files are placed into /usr/local/include, by default.
Build static libraries
.../zlib-1.2.1]# ./configure
.../zlib-1.2.1]# make test
.../zlib-1.2.1]# make install
Build shared libraries
.../zlib-1.2.1]# make clean
.../zlib-1.2.1]# ./configure --shared
.../zlib-1.2.1]# make test
.../zlib-1.2.1]# make install
.../zlib-1.2.1]# cp zutil.h /usr/local/include
.../zlib-1.2.1]# cp zutil.c /usr/local/include
/usr/local/lib should now contain...
libz.a
libz.so -> libz.so.1.2.1
libz.so.1 -> libz.so.1.2.1
libz.so.1.2.1
/usr/local/include should now contain...
zconf.h
zlib.h
zutil.h
[Optional] Instructions for non-standard placement of zlib
Create the directory that will contain zlib
.../zlib-1.2.1]# mkdir /usr/local/zlib
Follow the given procedure above, except
.../zlib-1.2.1]# ./configure --prefix=/usr/local/zlib
Update the Run-Time Linker
/etc/ld.so.cache will need to be updated with the new zlib shared lib: libz.so.1.2.1
For standard zlib installation...
Add /usr/local/lib to /etc/ld.so.conf, if specified path is not present
/etc]# ldconfig
If zlib was installed with a prefix...
Add /usr/local/zlib/lib to /etc/ld.so.conf
/etc]# ldconfig
安装openssl
Download
Home : http://www.openssl.org/
Package(source) : openssl-0.9.7d.tar.gz
Our Configuration
install to : /usr/local/ssl
module types : dynamically and staticly loaded modules, *.so *.a
Build Instructions
.../openssl-0.9.7d]# ./config
--prefix=/usr/local/ssl
[default location]
shared
[in addition to the usual static libraries, create shared libraries]
zlib-dynamic
[like "zlib", but has OpenSSL load the zlib library dynamically when needed]
.../openssl-0.9.7d]# ./config -t
[display guess on system made by ./config]
.../openssl-0.9.7d]# make
.../openssl-0.9.7d]# make test
.../openssl-0.9.7d]# make install
Update the Run-time Linker
ld.so.cache will need to be updated with the location of the new OpenSSL shared libs: libcrypto.so.0.9.7 and libssl.so.0.9.7
Sometimes it is sufficient to just add these two files to /lib, but we recommend you follow these instructions instead.
Edit /etc/ld.so.conf
Add /usr/local/ssl/lib to the bottom.
...]# ldconfig
Update the PATH
Edit /root/.bash_profile
Add /usr/local/ssl/bin to the PATH variable.
Re-login
Testing
...]# openssl version
Should display OpenSSL 0.9.7d 17 Mar 2004
If an older version is shown, your system contains a previously installed OpenSSL.
Repeate the steps in Update the PATH, except place the specified location at the start of the PATH variable.
[the older openssl, on most systems, is located under /usr/bin]
[the command 'which openssl' should display the path of the openssl that your system is using]
/usr/local/ssl/bin]# ./openssl version should display the correct version.
但是我最后没有得到想要的结果，系统原来的openssl还是没能卸载掉，我该怎么做那？我继续搜索资料，哈，幸运的我找了，在一个国内论坛上是这么说的
cd /usr/local/ssl/lib
ln -s libcrypto.so.0.9.7 libcrypto.so.2
ln -s libssl.so.0.9.7 libssl.so.2
//最后要刷新系统的动态连接库配置
echo /usr/local/ssl/lib >> /etc/ld.so.conf
ldconfig -v
这下子我豁然开朗，原来依赖的那2个文件是个软链接啊，我把它修改为我现在真正的openssl库文件不是就行了吗？于是一顿忙碌后，我终于执行了 rpm -e -nodeps ，然后重新启动系统，一路运行下去，全是绿灯。一时间感觉自己好幸福啊
为了这个问题我查了国内的几个比较大的unix/linux网站都没找到资料，不过从这里http://bbs.netbuddy.org/unix/737.html还是找到了（国外的E文大概意思能看懂，但是查找起来还是没找到，也不知道这方面好点的网站），
