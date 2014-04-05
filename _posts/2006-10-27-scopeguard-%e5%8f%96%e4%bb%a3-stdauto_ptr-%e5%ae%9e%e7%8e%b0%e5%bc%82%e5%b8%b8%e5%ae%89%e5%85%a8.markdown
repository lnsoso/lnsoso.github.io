---
layout: post
title: ScopeGuard 取代 std::auto_ptr 实现异常安全
author: gavinkwoe
date: !binary |-
  MjAwNi0xMC0yNyAxODoxMDoxNyArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yNyAxMDoxMDoxNyArMDgwMA==
---
<strong>转至：<a href="http://www.cppblog.com/eXile">http://www.cppblog.com/eXile</a></strong>
<strong>为了实现异常安全，经常见到下列代码，而且这也是被标准推荐的方式：　
void  f()
{　
　　std::auto_ptr<SomeType>  ptr(new SomeType); 　
　　//...and other operation
 }
boost替代scoped_ptr 来强化这个概念。这种方案的缺陷是只能用于删除指针，它实际上表达的是以下概念：
void f()
{　
　　SomeType * ptr = new SomeType; 
         ON_BLOCK_EXIT(delete ptr);
　　//...and other operation
 }
如何在退出作用域时自动执行所指定的函数，实现更广泛意义上的异常安全，而不是scoped_ptr, scoped_array,  scoped_function 等一系列替代品。LOKI的那个变态大师又提出了一种更好的办法，类似于下列代码：
template <class T> inline void Delete(T* p)
{  delete p; }
void f()
{　
　　SomeType * ptr = new SomeType; 
         ON_BLOCK_EXIT(&Delete,   ptr);
　　//...and other operation
 }
实现　ON_BLOCK_EXIT　的自动调用，关键是实现一个高效的 ScopeGuard，LOKI中实现大致如下：
　　
</strong><strong>　class ScopeGuardImplBase
{
public:
    void Dismiss() const throw()
    {    dismissed_ = true;    }
protected:
    ScopeGuardImplBase() : dismissed_(false)
    {}
    ScopeGuardImplBase(const ScopeGuardImplBase& other)
    : dismissed_(other.dismissed_)
    {    other.Dismiss();    }
    ~ScopeGuardImplBase() {} // nonvirtual (see below why)
    mutable bool dismissed_;
private:
    // Disable assignment
    ScopeGuardImplBase& operator=(
        const ScopeGuardImplBase&);
};</strong> 
<pre>				<strong>						
template <typename Fun, typename Parm>class ScopeGuardImpl1 : public ScopeGuardImplBase{public:    ScopeGuardImpl1(const Fun& fun, const Parm& parm)    : fun_(fun), parm_(parm)     {}    ~ScopeGuardImpl1()    {        if (!dismissed_) fun_(parm_);    }private:    Fun fun_;    const Parm parm_;};</strong>		</pre>
<pre>				<strong>						
template <typename Fun, typename Parm>ScopeGuardImpl1<Fun, Parm>MakeGuard(const Fun& fun, const Parm& parm){    return ScopeGuardImpl1<Fun, Parm>(fun, parm);}</strong>		</pre>
<strong>typedef const ScopeGuardImplBase& ScopeGuard;　
#define ON_BLOCK_EXIT      ScopeGuard g = MakeGuard
这只是实现了一个参数的情况，并作了简化处理，更具体的实现，请参看Loki库。</strong> 
