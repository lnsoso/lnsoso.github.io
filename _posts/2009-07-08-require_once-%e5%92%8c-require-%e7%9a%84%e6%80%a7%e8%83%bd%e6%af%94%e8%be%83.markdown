---
layout: post
title: require_once 和 require 的性能比较
author: Alvin
date: !binary |-
  MjAwOS0wNy0wOCAxNzoxNTo0NSArMDgwMA==
date_gmt: !binary |-
  MjAwOS0wNy0wOCAwOToxNTo0NSArMDgwMA==
---
之前大家都是在程序里写上简单的 require_once 和 require 然后直接跑一遍 ab 来看时间，这回 Konstantin Rozinon 在 apache debug 模式下看了一下 lstat64 的操作数量，对比结果说 require_once 和 require 在时间上相关非常非常小，但是在读文件时是用绝对路径还是相对路径对性能还是有一些影响，因为绝对路径会少一些 stat。
引用原文：
<small><code><span class="html">- When using absolute_path there are fewer stat() system calls.
- When using relative_path there are more stat() system calls because it has to start stat()ing from the current directory back up to / and then to the include/ directory.</span></code></small>
个人习惯上还是推荐用 require_once，并且这个不是显示的写在各个文件中，而是在中心的 loader 里统一负责根据 php5 的 __call 这个特性来去 require_once 相应的文件，一些性能上的损耗可以通过其它方式来弥补。比如 APC、XCache、Eacc 这些，opcode cache 现在成了 PHP 的必需品了。
原文：<a href="http://cn.php.net/require_once">点击进入</a>
btw: zend studio 7 beta 真慢……
