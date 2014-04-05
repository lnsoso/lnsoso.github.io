---
layout: post
title: 本站 ejeliot 模板下载，修正图片超大的 BUG
author: Alvin
date: !binary |-
  MjAwNi0xMC0xNiAxNjoxMDozMCArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0xNiAwODoxMDozMCArMDgwMA==
---
点击下载模板(10月16日修正版)
2006-10-24 更新
<ul>
<li>10.24 感谢<a href="http://www.gallonwang.com">王佳伦</a>同学的提示在首页宽度显示的 CSS Bug(Fixed)</li>
<li>修正图片大小的问题</li>
<li>并且把页面里默认字体大小固体为 12px</li>
<li>文章内容自动缩进两个字</li></ul>
如果你感觉现在的图片大小不合适的话，可以在 SCRIPT/ common.js 最下面的 iWidth 里手动调整。
在“网站设置”里设置的图片仅对用 UBB 插入的图片有效，对直接在发贴框里粘贴的失效，所以我只好在 common.js 里自己加了一个 onload 事件的处理。
这次修改时发现，要是 zblog 能对所有模板里的 const 变量自动替换就好了。
