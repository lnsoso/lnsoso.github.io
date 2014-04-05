---
layout: post
title: 在 leopard 上安装 nginx
author: Alvin
date: !binary |-
  MjAwOC0xMi0wMyAyMToyODoyMCArMDgwMA==
date_gmt: !binary |-
  MjAwOC0xMi0wMyAxMzoyODoyMCArMDgwMA==
---
下载 nginx 最新版
./configure && make && sudo make install
安装 nginx 时注意对 pcre, ssl , zlib, md5 和 sha1 的配置
