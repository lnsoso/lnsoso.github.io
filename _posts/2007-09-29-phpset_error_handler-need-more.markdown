---
layout: post
title: PHP:set_error_handler &#8230;&#8230;need more
author: Alvin
date: !binary |-
  MjAwNy0wOS0yOSAxODoxNzo0MSArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wOS0yOSAxMDoxNzo0MSArMDgwMA==
---
本来想自己写个 error 处理的 logger 结果发现通过 set_error_handler 没办法捕获到 fatal error & parse error 唉，真愁人呐。
在 php.net 上也没有找到办法，后来反到是在 zend.com 上找到了解决 catch fatal error 的办法就是在 auto_prepend_file 和 auto_append_file 上做手脚。
prepend 的文件里面有一个 string 里面是个 error page 的 html 包括一个 script 可以把错误信息发送到 server 的一个 api 上。
而在 append 的文件里通过 ob_get_contents() 来把那个 string 给去掉，如果能去掉就说明程序中间的执行流程正确，无错，如果有 fatal error 则没有办法到达 append 文件这步，所以会显示那个 html 页面。
方法还可以，但是因为每个 php 都要 prepend & append 可能效率会不行，和 Xuanyan 讨论了一小会暂时还是没有什么好办法来捕获 fatal & parse error 。
:(
