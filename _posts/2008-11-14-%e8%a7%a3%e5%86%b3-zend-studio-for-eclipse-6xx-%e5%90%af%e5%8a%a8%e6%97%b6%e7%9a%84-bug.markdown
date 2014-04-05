---
layout: post
title: 解决 Zend Studio for Eclipse 6.x.x 启动时的 bug
author: Alvin
date: !binary |-
  MjAwOC0xMS0xNCAxMTo1NDoxNiArMDgwMA==
date_gmt: !binary |-
  MjAwOC0xMS0xNCAwMzo1NDoxNiArMDgwMA==
---
在 Zend Studio for Eclipse 时会有显示 Building PHP Project 的错误，解决的办法很简单，在 preference 里的 General 中设置 Startup and Shutdown ，把 Advanced Debugger UI Plug-in 和 PDT Debug Daemon Plug-in 的对号去掉既可。

