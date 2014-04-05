---
layout: post
title: 异常处理 Exception Handling
author: gavinkwoe
date: !binary |-
  MjAwNi0xMC0yOSAyMjo0Mzo1OSArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yOSAxNDo0Mzo1OSArMDgwMA==
---
转至: <a href="http://davidripple.bokee.com">http://davidripple.bokee.com</a>
By David.Zhu 2005/7/20
Content List:
# Why Exception Handling when coding
# SEH Vs C++ Exception
# Exception Handling Modal in Visual C++
# Reference
<font color="#0000ff">1.Why Exception Handling when coding？
</font>     在写这篇Article之前，我问了公司的不少同事，你们Code的时候经常用Exception吗？结果，几乎没有什么人用！大家对Exception 好像并不是特别的感兴趣，有的认为Exception会降低程序30%左右的性能，因而弃之。我本人一直也没有用到异常，最近只是想用Windows SEH的异常捕获机制，来捕获诸如"0x780103cf指令应用0xC0000000内存的错误！"来防止程序Crash掉，作为使应用Robust的 一种方式。前几天看到Patrick Kooman 的《An Exceptional Model》一文才对Exception有一个较正确的认识。那么回到主题，究竟为什么要使用Exception Handling呢？Exception Handling机制能给我们带来什么便利呢？首先，我给出Patrick Kooman的两个图：
<div class="entity">
<li><center><img alt="" src="http://www.flipcode.com/articles/article_em_exceptional.jpg" />
Figure 1: the "sequential" model.</center>
<center><img alt="" src="http://www.flipcode.com/articles/article_em_sequential.jpg" />
Figure 2: the "exceptional" model. </center>
在Figure 1中我们看到如果不用Exception机制，那么下层的函数在发现错误时会一层一层的上报，典型的就是LoadArt-> LoadResource->Init，如果一个分层应用的层数比较多的话，那么用于这样错误检测的代码将会很多。而采用Exception机制， 底层的错误可以一步上传到Init,避免了中间环节，代码更简练啦，同时Exception机制还可以在构造函数中抛出，避免了出现Invalid objects的情况（因为构造函数没有返回值，遇到错误也只有忍气吞声啦）。使用Exception可以省去用于检验函数调用成功与否的函数返回值，因 而函数可以更加简练了。但使用Exception机制会增加在程序调试时的难度，增加程序的大小和程序性能上一定的损失！
<font color="#0000ff"><a name="1.2"></a>2.SEH Vs C++ Exception </font>
    SEH （结构化异常处理，C异常处理，Win32异常处理）是Windows提供的一套结构化异常处理机制，提供基于process的异常处理，进程可以通过 SetUnhandledExceptionFilter来添加自己的异常处理函数，这种机制是Final型的或称top型的：最后安装的seh处理例程 总是优先得到控制权。这有时并不是最好的解决方案，为此在WinXP中微软提供了向量化异常处理机制，Vectored Exception Handling比SEH有加强但Final型的缺点仍然没有得到实质解决！在VC中__try/__except/__finally/__leave 都是用于SEH的。SEH的异常对象一般都是unsigned int型的，结构异常可以捕获到系统除零错误，内存越界访问错误等错误，当然也可以用RaiseException来抛发结构异常。 
    C ++异常支持抛发一个C++对象而不仅仅是像结构型异常那样的unsigned int型，但C++异常却无法捕获到诸如系统除零错误，内存越界访问错误等系统重大错误。在VC中try/catch来实行C++异常捕获，而使用 throw来抛发C++异常。有关SEH和C++ EH的区别请参考MSDN的"Exception Handling Differences"。MFC里的CException机制就是利用的C++ 异常处理机制来实现的。 
    由于SEH只支持抛发 unsigned int异常对象，而C++异常支持抛发对象，因而我们可以整合这两个在接受到SEH异常时将int对象转成一个C++类对象再通过throw抛发出来。主 要的就是借助_set_se_translator和SetErrorMode两个函数了，有兴趣的可以参考Reference中给出的"SEH and C++ Exceptions - catch all in one"一文。需要注意的是在C异常和C++异常不能在同一个函数中同时使用，在C++程序中中使用C异常处理可能会导致在 stack unwind过程中调用stack中某些对象的析构函数没有得到调用()。 
    在MFC中也有一套异常处理机制，TRY,CATCH, AND_CATCH，END_CATCH,THROW，THROW_LAST.其实MFC的异常处理机制是建立在C++异常处理上的，所以MFC的异常处 理机制支持C++异常，但不支持SEH，MFC异常处理机制主要是对MFC封装的类在运行过程中可能出现的异常进行处理，所有的MFC异常对象都派生于 CException。 <font color="#0000ff">3.Exception Handling Modal in Visual C++</font>
    VC 中支持同步异常处理模型和异步异常处理模型。同步异常处理模型（/EHs），异步异常处理模型（/EHa），同时还可以指定 extern C的函数是否能抛发异常，/EHc将设置Compiler为认为 extern C 的函数不抛出异常。/GX 选项等同于/EHsc，在VC中默认的是/GX-即禁用C++异常处理。 
<font color="#0000ff">4.Reference</font>
</li># 1.An Exceptional Model by Patrick Kooman
# 2.异常处理（1～5） 选择自 cppbug 的 Blog
# 3.SEH and C++ Exceptions - catch all in one
# 4.windows XP下的向量化异常处理
# 5.C与C++中的异常处理
# 6.Exception Handling Overhead 
</div>
