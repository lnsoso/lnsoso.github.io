---
layout: post
title: ! '[转] J2me流媒体技术实现讨论[2] '
author: gavinkwoe
date: !binary |-
  MjAwOC0wOC0zMSAxOToxNToxMiArMDgwMA==
date_gmt: !binary |-
  MjAwOC0wOC0zMSAxMToxNToxMiArMDgwMA==
---
cleverpig said“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">之所以有些格式的媒体文件不支持分段播放，是因为它们文件中不含有索引信息。
就像在以顺序方式读取文件时无法seek一样。。
这个问题可以通过人工（或者用程序）将文件分割后部署放到服务器上来解决。</td>
</tr>
</tbody></table>
</div>
”
以及“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">随着iTunes4.9版的发布，podcaster（pod播客们）能够建立自己的podcast，并可以通过增加幻灯片式的图片使其更加吸引人。而且 在附加信息中的URL还可使用户门自由的找到其他的podcast资源。这成为了podcast世界的“大地震”。目前这一特性移植到手机上是通过划分“ 章节”来完成的，即将podcast资源文件划分为多个章节，这样做才能让没有“重播/定位”能力的手机进行播放。
但是另一个挑战将摆在移动用户面前，例如：移动收听必须对中断事件进行管理。当我们正开始播放20-40分钟的podcast时，一个电话或者短信突然到 来，这些情况将使播放被迫中断。此时我们只能选择重新打开podcast从头再听或者是没有心情从头听。另外媒体文件格式问题也是对移动用户的“噩梦”， 大多数手机都不支持mp3或者AAC这种podcast的文件格式，但它们都支持.3gp的标准AMR格式文件。而且能够保存几兆mp3或者AAC文件） 的手机目前也不是很普及。
但是Tea Vui Huang制作的javacast改变了这一切。这个软件就是将mp3音乐转换为手机可以播放的.3gp 标准amr（audio recording format）格式。大家可以到http://www.ringtone4me.com/看看，上面有一些具有此类功能软件链接。
javacast的作者&mdash;&mdash;Tea Vui Huang也是Mobcast的作者, 已经制作了一套处理工具将转换Podcast到一个java Midlet中（用户只需要在手机中调用javacast无线下载这个j2me应用程序，并可以播放podcast）。这使那些podcasters们通 过简单的增加一个下载这个midlet的链接就能很容易是获得他们的podcast。</td>
</tr>
</tbody></table>
</div>
”
Huang的Mobcast，确实非常著名，几个月以前，在我写toodouPodcastMidlet时就看过许多人介绍过他，但是就是连不上http://www.geocities.com/tvhuangsg/m...��睹真容。
转换各种格式的video为3gp，转换各种格式的audio为amr，这些在开源软件mplayer手下是随手拈来，只需要看懂mplayer的各种参数即可做到了。所以拜mplayer所赐，我也能够制作手机看交通实况录像，都要感谢那些mplayer的开发人员！
"移动收听必须对中断事件进行管理"，这个确实需要考虑。当进入Paused状态时，需要通知播放线程暂停，同时连接线程暂时就不要去抓取服务器的媒体数据了；等界面切换回来后，播放线程继续replay，连接线程继续下载音乐。
斑竹说“可以通过人工（或者用程序）将文件分割后部署放到服务器上来解决”，我想也是，简单的文件分割是不够的，或者说仅仅适合于wav这种原始数据格 式。应该事先将音乐文件用mencoder分解成一段一段的音乐文件放在服务器上，mencoder将处理每一段的格式问题保证能独立播放，这样手机下载 起来只需要按照编号一段一段地下载即可，服务器不再需要运算和添加头信息。
美中不足，如果两个player切换播放，中间会有一个卡啪声。
cleverpig said“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">有兴趣的话可以看jffmpeg，是一种能够处理音频视频的java媒体框架。</td>
</tr>
</tbody></table>
</div>
”以及
“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">想了一下，提出一个“移动音频流网关”的想法：可以使用服务器采用“实时”转化格式的方式，将mp3、wav等格式音频转换为amr格式，当然也可以做分 段，无论音频源是什么（甚至是podcast）都可以下载到手机上收听。但这样做的话，服务器的负载是个问题，尽管已用采集过的音频源不用再次处理。</td>
</tr>
</tbody></table>
</div>
”
其实，我原来写的toodouPodcast就是这么一个概念，由于那些播客们提供的音乐格式不符合手机播放，所以我都用toodouPodcast这么 个java web service调用ffmpeg工具进行音频转换。转换格式，确实是一个很费CPU资源的事情，而且时间很长，如果用户多的话，对服务器压力极大。
那么现在做做分段也不错，这样，更适合手机用户。
Jffmpeg应该是对ffmpeg这个C编写的工具的Java封装。
另一个封装的是
<a href="http://fobs.sourceforge.net/" target="_blank">http://fobs.sourceforge.net/</a>
FOBS, the C++ & JMF wrapper for ffmpeg.
