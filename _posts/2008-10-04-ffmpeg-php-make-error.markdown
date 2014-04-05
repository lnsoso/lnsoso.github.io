---
layout: post
title: ffmpeg-php make error
author: Alvin
date: !binary |-
  MjAwOC0xMC0wNCAxNTowOToxOCArMDgwMA==
date_gmt: !binary |-
  MjAwOC0xMC0wNCAwNzowOToxOCArMDgwMA==
---
make 时一直报 'ImgReSampleContext' undeclared 这个错误，看了一下 log 好像是谁改了 avcodec.h 文件，只好用 ffmpeg r15261 来编译了，sigh。ffmpeg svn 的稳定性一直很愁人<a href="http://www.flickr.com/photos/27801040@N03/2835558500"><img src="http://farm4.static.flickr.com/3179/2835558500_5cef17c0ae.jpg" /></a>

