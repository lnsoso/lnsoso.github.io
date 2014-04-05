---
layout: post
title: snow leopard 下通过 py 脚本下载 google music
author: Alvin
date: !binary |-
  MjAwOS0wOS0xNyAxMDo0OToyNiArMDgwMA==
date_gmt: !binary |-
  MjAwOS0wOS0xNyAwMjo0OToyNiArMDgwMA==
---
最近在国内没办法再用 spotify 听歌了，迫于无奈只好转向 Google Music 。

同时在考虑把歌同步到 iPhone 上来听的情况，开始着手找下载 Google Music 的小工具，发现骨头做的 [gmbox](http://li2z.cn/2009/09/13/gmbox-0-2/) 不错，可惜现在是只支持 windows 和 linux ，只好用 [gmusic.py](http://forum.ubuntu.com.cn/viewtopic.php?f=73&p=1205852) 这个脚本。

运行 gmusic.py 首先要安装 python3 环境，去 python 官方网站载下 python3.1 然后执行如下脚本

```bash
configure --enable-shared && make && sudo make install && ln -s /usr/local/bin/python3 /usr/bin/python3

```

用 gmusic.py 下载速度在我的 istat 里显示大概是 800K 左右，非常不错。
