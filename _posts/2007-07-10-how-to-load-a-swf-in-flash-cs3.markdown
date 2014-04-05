---
layout: post
title: How to load a swf in flash cs3
author: gavinkwoe
date: !binary |-
  MjAwNy0wNy0xMCAxNzo0NDoyNyArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wNy0xMCAwOTo0NDoyNyArMDgwMA==
---
<pre>import flash.display.*;import flash.net.URLRequest;var rect:Shape = new Shape();rect.graphics.beginFill(0xFFFFFF);rect.graphics.drawRect(0, 0, 100, 100);addChild(rect);var ldr:Loader = new Loader();ldr.mask = rect;var url:String = "http://www.unknown.example.com/content.swf";var urlReq:URLRequest = new URLRequest(url);ldr.load(urlReq);addChild(ldr);</pre>
