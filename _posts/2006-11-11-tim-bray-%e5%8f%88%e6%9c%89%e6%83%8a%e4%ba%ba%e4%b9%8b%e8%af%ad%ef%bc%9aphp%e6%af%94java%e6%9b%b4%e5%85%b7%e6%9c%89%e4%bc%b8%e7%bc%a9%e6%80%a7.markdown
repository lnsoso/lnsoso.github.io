---
layout: post
title: Tim Bray 又有惊人之语：PHP比Java更具有伸缩性
author: gavinkwoe
date: !binary |-
  MjAwNi0xMS0xMSAxNjoxNzozNSArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0xMSAwODoxNzozNSArMDgwMA==
---
在 CSDN 上看到的,原文在Tim bray的blog ：comparison intrinsic qualities of Java, Rails, and PHP  中（<a href="http://www.tbray.org/ongoing/When/200x/2006/11/10/Comparing-Frameworks">http://www.tbray.org/ongoing/When/200x/2006/11/10/Comparing-Frameworks</a>）
Tim首先明确了它这个观点的适用的范围：Web应用程序。对于那些基于浏览器的，从数据库显示一些信息，然后能够更新数据库的这种应用程序。
<img class="norm" title="Comparing Web Frameworks" alt="Comparing Web Frameworks" src="http://www.tbray.org/ongoing/When/200x/2006/11/10/Comparison-1.png" width="300" />
1 伸缩性
Java 开发了EBay ，而 PHP 开发了 Wikipedia 和 Yahoo! Finance. 它们的伸缩性都足够好。
2 开发速度
3 工具支持
Java是最大的赢家，Rails有TextMate，PHP有Zend
4 可维护性
一个好的应用程序需要 面向对象，MVC架构，代码可读，代码的数量越少越好。
Tim认为这是PHP的痛脚，虽然PHP完全可以写出上面的代码，但是PHPer通常不这么做。大多数PHP程序是意大利面式的代码裹着意大利面式的SQL和意大利面式的HTML。（笑死人了）
Tim最后说，不要问它PHP，Rails,Java那个好，它依赖于你对项目进行的选择。最近几年PHP和Rails教会了我们开发的速度是多么的重要。在一切的开发中，维护才是最重要的。
InfoQ对Tim进行了访问：
<span style="FONT-WEIGHT: bold">             InfoQ: 为什么Rails比Java更具有可维护性?</span> 
<blockquote>
主要是因为代码少。事实是Ruby强制使用MVC模式，其模板机制和ORM，以及测试和程序代码耦合的太紧密了。请记住，我们到现在还搞不清，Rails对于那些不适用于CRUD形式的程序到底有什么用。
<span style="FONT-WEIGHT: bold">InfoQ: 为什么PHP比Java更具有伸缩性？</span> 
不是这个意思，而是在web应用领域，它易于伸缩（没有中间件或服务要共享）。
<span style="FONT-WEIGHT: bold">InfoQ: 那一种特性在你的比较中最为重要？</span></blockquote>
<blockquote>可维护性。</blockquote>
Tim 继续在其blog中解释了可维护性：
<blockquote>在这个疯狂的Web2.0的世界里，能够快速的构建系统非常重要。面对投资和开发需求，你要在最短的时间交付系统。但是真正做过企业开发的聪明的程序员和经理们都知道。开发最大的成本是从你产品交付的那一刻，才刚刚开始。</blockquote>
