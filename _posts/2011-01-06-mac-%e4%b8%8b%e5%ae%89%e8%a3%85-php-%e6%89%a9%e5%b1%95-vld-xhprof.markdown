---
layout: post
title: Mac 下安装 PHP 扩展 vld & xhprof
author: Alvin
date: !binary |-
  MjAxMS0wMS0wNiAxMToyNjo1NyArMDgwMA==
date_gmt: !binary |-
  MjAxMS0wMS0wNiAwMzoyNjo1NyArMDgwMA==
---
最近因为经常离线调试，所以开始重新在本机搭环境，还好 Mac 原本就自带了 PHP 。

```bash
[515][MacBookPro: /tmp]$ which php
/usr/bin/php
[516][MacBookPro: /tmp]$ php --version
PHP 5.3.3 (cli) (built: Aug 22 2010 19:41:55)
Copyright (c) 1997-2010 The PHP Group
Zend Engine v2.3.0, Copyright (c) 1998-2010 Zend Technologies
```

既然有 php 那就先试试直接用 pecl 来安装。

```bash
sudo pecl install -f vld
sudo pecl install -f xhprof
```

不过安装 xhprof 时提示说要在扩展的目录里，查了一下 PECL Bug [#16438](http://pecl.php.net/bugs/bug.php?id=16438&edit=1) 里面说是 pecl 里 xhprof 包的问题。那么问题也就很好解决了，直接把源码包下载下来安装即可。

```bash
wget http://pecl.php.net/get/xhprof-0.9.2.tgz
tar zxf xhprof-0.9.2.tgz
cd xhprof-0.9.2/extension
phpize
./configure
make && make install
```

然后修改 /etc/php.ini 如果没有这个文件就

```bash
sudo cp /etc/php.ini.default /etc/php.ini。
```

在 php.ini 中增加 extension=vld.so 和 extension=xhprof.so 和针对这两个扩展的详细配置，这个可以在网上搜到。
然后在 ~/.bash_profile 里增加 alias phpo='php -dvld.active=1'
以后再打 phpo xxxx.php 就可以看到 vld 的效果了。