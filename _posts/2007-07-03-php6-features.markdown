---
layout: post
title: PHP6 features
author: gavinkwoe
date: !binary |-
  MjAwNy0wNy0wMyAxNTo1OTowMCArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wNy0wMyAwNzo1OTowMCArMDgwMA==
---
去年在巴黎举行的PHP开发者大会中，PHP6开发的消息开始流传开来，于PHP大会讨论的PHP6，将有很大幅度的变化，但这只是草案阶段，并不代表所有会议的机率都会随着PHP6的发布而包含记录中所有的变更也就是说，在发布PHP6之前，还是会有异动的情形，但是可以确定的是下面所列的数项变化，将会随着PHP6一同面世（当然不是百分百乐，）赶快来看看这些新特性吧 
1.支持Unicode 
支持Unicode是有其必然，虽然Unicode占用较多的空间，但Unicode带来的便利性，远超过占用空间的缺点，尤其在国际化的今天，硬件设备越来越强大，网速也大幅度的提升，这么一点小小的缺点是可以忽略的。另外一点，PHP也可以在.ini文件中设定是否开启支持Unicode，决定权在你自己，这是一个不错的点子，关掉Unicode的支持，PHP的性能并不会有大幅度的提升，主要的影响在于需要引用字符串的函数。 
2.Register Globals 将被移除 
这是一个重要的决定，说多新进的PHP开发者会觉得Register Globals满方便的，但是却忽略了Register Globals会带来程序上安全性的隐患，大多数的主机上此项功能是关闭的，印象中从PHP4.3.x版开始时，此项默认设置值即是关闭状态，PHP6正式移除Register Globals也代表着如果程序是由PHP3时代的产物，将完全无法使用，除了改写一途外，别无他法。相信现在的PHP世界里，仍使用PHP3时代所产生的程序应该是少之又少。 
3.Magic Quotes 将消失 
Magic Quotes主要是自动转义需要转义的字符，此项功能移除叶符合大多数PHP开发者的心声。 
4.Safe Mode 取消 
老实说，这个模式不知道哪里不好，取消就取消吧，反正也用不到 
5.&rsquo;var&rsquo; 别名为 &lsquo;public&rsquo; 
在类中的var声明变成public的别名，相信是为了兼容PHP5而作的决定，PHP6现在也可以称作为OO语言了。 
6.通过引用返回将出错 
现在透过引用返回编译器将会报错 例如$a =& new b()、function &c()，OO语言默认就是引用，所以不需要再使用&了。 
7.zend.ze1 compatbility mode 将被移去 
Zend.ze1相容模式将被移去，PHP5是为兼容旧有PHP4，所以在.ini中可选择是否开启相容模式，原因在于PHP5使用的是第二代解析引擎，但是相容模式并不是百分之百能解析PHP4语法，所以旧时代的产物，移除。 
8.Freetype 1 and GD 1 support 将不见 
这两个是很久的Libs，所以不再支持，GD1早已被现在的GD2取代了。 
9.dl() 被移到 SAPI 中 
dl()主要是让设计师加载extension Libs，现在被移到 SAPI 中 
10.Register Long Array 去除 
从PHP5起默认是关闭，再PHP6中正式移除。 
11.一些Extension的变更 
例如 XMLReader 和 XMLWriter 将不再是以Extension的方式出现，他们将被移入到PHP的核心之中，并且默认是开启，ereg extension将被放入PECL，代表着它将被移出PHP核心，这也是为了让路给新的正则表达式extension，此外，Fileinfo extension 也将被导入PHP的核心之中。 
12.APC将被导入核心 
这是一个提高PHP性能的功能，现在它将被放入PHP核心中，并且可以选择是否启用APC 
13.告别ASP风格的起始标签 
原来是为了取悦ASP开发者转向使用PHP，现今已经不再需要这种做法了， 
最后，别期望PHP6的性能可以全面超过PHP5，有可能的是PHP6的执行效率会比PHP5还要来的慢的，但是可以预期的是，PHP开发小组将会努力的完善PHP5，超越PHP5。 
那么，对PHP6有兴趣的朋友现在可以到PHP官方网站上下载，试试这些功能是否真的已经 
在PHP6中体现出来了，下载地址http://snaps.php.net/

