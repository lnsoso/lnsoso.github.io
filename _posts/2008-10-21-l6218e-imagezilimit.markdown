---
layout: post
title: ! 'L6218E: Image$$ZI$$Limit'
author: gavinkwoe
date: !binary |-
  MjAwOC0xMC0yMSAwOTo0NjoxMiArMDgwMA==
date_gmt: !binary |-
  MjAwOC0xMC0yMSAwMTo0NjoxMiArMDgwMA==
---
昨天遇到一个十分麻烦的问题，我把MTK编译时遇到这样的错误：
Error : L6218E: Undefined symbol Image$$ZI$$Limit (referred from sys_stackheap.o).
Not enough information to produce a SYMDEFs file.
而没有这三个：
Not enough information to list image symbols.
Not enough information to list the image map.
Not enough information to list the image sizes and/or totals.
网上只有两种解释，是这样：
1. reimplement __user_initial_stackheap()
解决办法大概意思是重新装配__user_initial_stackheap()函数。
2.分配内存的时候,要分配内存的结构中使用了ARM不支持的数据类型.通常定义了结构体的指针，然后用
malloc分配空间时为结构体类型指针，而ARM不支持这种数据类型，所以会有这种错误。解决办法：用
typedef预定义这个结构类型，使得编译器识别这种类型。
第一种方法离我太远，应该不会涉及到；
第二种方法试过，没用，但我也大概知道方向是malloc函数的问题。
今天终于解决：
这个问题是由于代码或者Lib中调用了 C Lib的malloc或者类似于strdup，printf 这样的会调用malloc的
C Lib function 引起的。MTK Platform不支持 C lib的malloc，而用 Ctrl Buffer机制代替了malloc，
以便于调试memory leak问题。MTK中的Osl层有专门处理内存的函数，于是我想，我用malloc是跳过Osl层
直接分配内存，这样没有经过系统处理，危险性大。所以我用系统自带的OslMalloc和OslMfree来处理内
存空间，问题解决。
