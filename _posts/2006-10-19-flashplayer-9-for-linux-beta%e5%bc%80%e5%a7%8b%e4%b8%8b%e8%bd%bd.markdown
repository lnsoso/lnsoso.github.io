---
layout: post
title: FlashPlayer 9 for linux Beta开始下载
author: Alvin
date: !binary |-
  MjAwNi0xMC0xOSAxMToxNjo0NSArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0xOSAwMzoxNjo0NSArMDgwMA==
---
TNND，被骗了，FlashPlayer 9 for linux Beta已经可以下载了，不过也好，更多信息访问这里 (en)
Ubuntu安装方法如下：

```bash
wget http://www.adobe.com/go/fp9_update_b1_installer_linuxplugin
tar xvzf FP9_plugin_beta_101806.tar.gz
cd flash-player-plugin-9.0.21.55
sudo rm /usr/lib/firefox/plugins/*flash*
sudo cp libflashplayer.so /usr/lib/firefox/plugins/
```

安装完成后，到这里 (en)测试是否成功，如果正常的话，应该会显示FlashPlayer版本：<a http://www.adobe.com/products/flash/about/>

PS:中文环境下，关闭Flash页面会出现段错误，英文环境不会，所以可以使用firefox前先切换到英文环境：

```bash
export LANG=en_GB.UTF-8
firefox

```