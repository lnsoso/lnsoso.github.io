---
layout: post
title: ! 'boost::singleton : 简单，高效，线程安全的singleton模式实现'
author: gavinkwoe
date: !binary |-
  MjAwNi0xMC0yNyAxODowNTo1MyArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yNyAxMDowNTo1MyArMDgwMA==
---
<strong>转至：<a href="http://www.cppblog.com/eXile">http://www.cppblog.com/eXile</a></strong>
<strong>本着简单高效的原则,  boost::singleton是singleton模式的又一种实现,  它基于以下假设, 一个良好的设计, 在进入main函数前应该是单线程的，此时，我们可以采用和全局变量相似的办法来使用singleton，因为所有的全局变量在进入main以前已经全部初始化。这样我们就避开了多线程的竞争条件. 但直接使用全局变量有一个严重的缺陷，就是当你使用全局变量时，你并不能保证它已经得到初始化，这种情况发生在 static code中。(static code是boost文档中的用语, 我想它是指在进入main函数以前要执行的代码)。
     boost::singlton实现的关键有两点
         (1) sington 在进入main函数前初始化.
          (2)第一次使用时, singlton已得到正确的初始化(包括在static code中情况).
　boost中的实现代码如下所示：
　
template <typename T>
struct singleton
{
  private:
        struct object_creator
       {
               object_creator() { singleton<T>::instance(); }
               inline void do_nothing() const { }
       };
     static object_creator create_object;
     singleton();
  public:
     typedef  T  object_type;
     static object_type & instance()
    {
         static object_type obj;
         create_object.do_nothing();
         return obj;
    }
};
template <typename T>  typename singleton<T>::object_creator singleton<T>::create_object;</strong>
