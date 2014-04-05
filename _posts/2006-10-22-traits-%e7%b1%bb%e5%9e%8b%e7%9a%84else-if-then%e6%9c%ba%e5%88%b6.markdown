---
layout: post
title: ! 'Traits: 类型的else-if-then机制'
author: gavinkwoe
date: !binary |-
  MjAwNi0xMC0yMiAxNzo0OTo1MyArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yMiAwOTo0OTo1MyArMDgwMA==
---
什麽是traits，为什麽人们把它认为是 C++ Generic Programming 的重要技术？
简短截说，traits如此重要，是因为此项技术允许系统在编译时根据类型作一些决断，
就好像在运行时根据值来作出决断一样。更进一步，此技术遵循“另增一个间接层”
的谚语，解决了不少软件工程问题，traits使您能根据其产生的背景(context)
来作出抉择。这样最终的代码就变得清晰易读，容易维护。如果你正确运用了traits
技术，你就能在不付出任何性能和安全代价的同时得到这些好处，或者能够契合其他
解决方案上的需求。
例子：Traits不仅是泛型程序设计的核心工具，而且我希望以下的例子能够使你相信，
在非常特定的问题中，它也是很有用的。
假设你现在正在编写一个关系数据库应用程序。可能您一开始用数据库供应商提供的
API库来进行反问数据库的操作。但是理所当然的，不久之後你会感到不得不写一些
包装函数来组织那些原始的API，一方面是为了简洁，另一方面也可以更好地适应
你手上的任务。这就是生活的乐趣所在，不是吗？
一个典型的API是这样的：提供一个基本的方法用来把游标(cursor, 一个行集和或
者查询结果)处的原始数据传送到内存中。现在我们来写一个高级的函数，用来把某
一列的值取出来，同时避免暴露底层的细节。这个函数可能会是这个样子：
（假想的DB API用db或DB开头）
// Example 1: Wrapping a raw cursor int fetch
// operation.
// Fetch an integer from the
//     cursor "cr"
//     at column "col"
//     in the value "val"
void FetchIntField(db_cursor& cr, 
    unsigned int col, int& val)
{
    // Verify type match
    if (cr.column_type[col] != DB_INTEGER)
        throw std::runtime_error(
        "Column type mismatch");
    // Do the fetch
    db_integer temp;
    if (!db_access_column(&cr, col))
        throw std::runtime_error(
        "Cannot transfer data");
    memcpy(&temp, cr.column_data[col],
        sizeof(temp));
    // Required by the DB API for cleanup
    db_release_column(&cr, col);
    // Convert from the database native type to int
    val = static_cast<int>(temp);
}
这种接口函数我们所有人都可能不得不在某个时候写上一遍，它不好对付但又非常重
要，处理了大量细节，而且这还只是一个简单的例子。FetchIntField抽象，提供了
高一层次的功能，它能够从游标处取得一个整数，不必再担心那些纷繁的细节。
既然这个函数如此有用，我们当然希望尽可能重用它。但是怎麽做？一个很重要的泛化
步骤就是让这个函数能够处理int之外的类型。为了做到这一点，我们得仔细考虑代码中
跟int类型相关的部份。但首先，DB_INTEGER和db_integer是什麽意思，它们是打哪儿
来的？是这样，关系数据库供应商通常随API提供一些type-mapping helpers，为其所
支持的每种类型和简单的结构定义一个符号常量或者typedef，把数据库类型对应到
C/C++类型上。
下面是一段假想的数据库API头文件：
#define DB_INTEGER 1
#define DB_STRING 2
#define DB_CURRENCY 3
...
typedef long int db_integer;
typedef char     db_string[255];
typedef struct {
    int integral_part;
    unsigned char fractionary_part;
} db_currency;
...
我们试  来写一个FetchDoubleField函数，作为走向泛型化的第一步。此函数从游标处得到
一个double值。数据库本身提供的类型映像(type mapping)是db_currency，但是我们希望
能用double的形式来操作。FetchDoubleField看上去跟FetchIntField很相似，简直就是孪
生兄弟。例2：
// Example 2: Wrapping a raw cursor double fetch operation.
//
void FetchDoubleField(db_cursor& cr, unsigned int col, double& val)
{
    if (cr.column_type[col] != DB_CURRENCY)
        throw std::runtime_error("Column type mismatch");
    if (!db_access_column(&cr, col))
        throw std::runtime_error("Cannot transfer data");
    db_currency temp;
    memcpy(&temp, cr.column_data[col], sizeof(temp));
    db_release_column(&cr, col);
    val = temp.integral_part + temp.fractionary_part / 100.;
}
看上去很像FetchIntField吧  我们可不想对每一个类型都写一个单独的函数，所以
如果能够在一个地方把FetchIntField, FetchDoubleField以及其他的Fetch函数合
为一体就好了。
我们把这两片代码的不同之处列举如下：
  &middot;输入类型：double/int
  &middot;内部类型：db_currency/db_integer
  &middot;常数值类型：DB_CURRENCY/DB_INTEGER
  &middot;算法：一个表达式/static_cast
输入类型(int/double)与其他几点之间的对应关系看上去没什麽规律可循，而是很随意，
跟数据库供应商（恰好）提供的类型关系密切。Template机制本身无能为力，它没有提供
如此先进的类型推理机制。也没法把不同的类型用继承关系组织起来，因为我们处理的是
原始类型。受到API的限制以及问题本身的底层特性，乍看上去我们好像没辙了。不过我们
还有一条活路。
进入TRAITS大门：Traits技术就是用来解决上述问题的：把与各种类型相关的代码片断合体，
并且具有类似and/or结构的能力，到时可以根据不同的类型产生不同的变体。
Traits依赖显式模版特殊化(explicit template specialization)机制来获得这种结果。
这一特性使你可以为每一个特定的类型提供模板类的一个单独实现，见例3：
// Example 3: A traits example
//
template <class T>
class SomeTemplate
{
    // generic implementation (1)
    ...
};
// 注意下面特异的语法
template <>
class SomeTemplate<char>
{
    // implementation tuned for char (2)
    ...
};
...
SomeTemplate<int> a;      // will use (1)
SomeTemplate<char*> b;    // will use (1)
SomeTemplate<char> c;     // will use (2)
如果你用char类型来实例化SomeTemplate类模板，编译器会用那个显式的模板声明来特殊化。
至於其他的类型，当然就用那个通用模板来实例化。这就像一个由类型驱动if-statement。
通常最通用的模板(相当于else部份)最先定义，if-statement靠後一点。你甚至可以决定
完全不提供通用的模板，这样只有特定的实例化是允许的，其他的都会导致编译错误。
现在我们把这个语言特性跟手上的问题联系起来。我们要实现一个模板函数FetchField，
用需要读取的类型作为叁数来实例化。在该函数内部，我会用一个叫做TypeId的东西代表
那个符号常量，当要获取int型值时它的值就是DB_INTEGER，当要获取double型值时它的
值就是DB_CURRENCY。否则，就必须在编译时报错。类似的，根据要获取的类型的不同，
我们还需要操作不同的API类型(db_integer/db_currency)和不同的转换算法（表达式/static_cast).
让我们用显式模板特殊化机制来解决这个问题。我们得有一个FetchField，可以针对一个
模板类来产生不同的变体，而那个模板类又能够针对int和double进行显式特殊化。每个
特殊化都必须为这些变体提供统一的名称。
// Example 4: Defining DbTraits
//
// Most general case not implemented  最通用的情况没有实现
template <typename T> struct DbTraits;
// Specialization for int
template <>
struct DbTraits<int>
{
    enum { TypeId = DB_INTEGER };
    typedef db_integer DbNativeType;
    // 注意下面的Convert是static member function      译者
    static void Convert(DbNativeType from, int& to)
    {
        to = static_cast<int>(from);
    }
};
// Specialization for double
template <>
struct DbTraits<double>
{
    enum { TypeId = DB_CURRENCY };
    typedef db_currency DbNativeType;
    // 注意下面的Convert是static member function      译者
    static void Convert(const DbNativeType& from, double& to)
    {
        to = from.integral_part + from.fractionary_part / 100.;
    }
};
现在，如果你写DbTraits<int>::TypeId，你得到的就是DB_INTEGER，而对於
DbTraits<double>::TypeId，得到的就是DB_CURRENCY，对於
DbTraits<anything_else>::TypeId，得到的是什麽呢？Compile-time error!
因为模板类本身只是声明了，并没有定义。
是不是一劳永逸了？看看我们如何利用DbTraits来实现FetchField就放心了。
我们把所有变化的部份    枚举类型  API类型  转换算法    都放在了DbTraits
里，这下我们的函数里只包含FetchIntField和FetchDoubleField的相同部份了：
// Example 5: A generic, extensible FetchField using DbTraits
//
template <class T>
void FetchField(db_cursor& cr, unsigned int col, T& val)
{
    // Define the traits type
    typedef DbTraits<T> Traits;
    if (cr.column_type[col] != Traits::TypeId)
        throw std::runtime_error("Column type mismatch");
    if (!db_access_column(&cr, col))
        throw std::runtime_error("Cannot transfer data");
    typename Traits::DbNativeType temp;
    memcpy(&temp, cr.column_data[col], sizeof(temp));
    Traits::Convert(temp, val);
    db_release_column(&cr, col);
}
搞定了  我们只不过实现和使用了一个traits模板类而已  
Traits依靠显式模板特殊化来把代码中因类型不同而发生变化的片断拖出来，用统一的
接口来包装。这个接口可以包含一个C++类所能包含的任何东西：内嵌类型，成员函数，
成员变量，作为客户的模板代码可以通过traits模板类所公开的接口来间接访问之。
这样的traits接口通常是隐式的，隐式接口不如函数签名(function signatures)那麽
严格，例如，尽管DbTraits<int>::Convert和DbTraits<double>::Convert有  非常不
同的签名，但它们都可以正常工作。
Traits模板类在各种类型上建立一个统一的接口，而又针对各种类型提供不同的实现细节。
由於Traits抓住了一个概念，一个相关联的选择集，所以能够在相似的contexts中被重用。
定义： A traits template is a template class, possibly explicitly 
specialized, that provides a uniform symbolic interface over a coherent 
set of design choices that vary from one type to another. 
       
       Traits模板是一个模板类，很可能是显式特殊化的模板类，它为一系列根据不同类
   型做出的设计选择提供了一个统一的  符号化的接口。
TRAITS AS ADAPTERS: 用作适配子的TRAITS
数据库的问题说得够多了，现在我们来看一个更一般的例子    smart pointers。
假设你正在设计一个SmartPtr模板类。对於一个smart pointer来说，最美妙的事情是它们
可以自动管理内存问题，同时在其他方面又像一个常规指针。而不那麽美妙的事情是，它们
的实现代码可不是好对付的，有些C++的smart pointer实现技术简直就是在黑暗中变戏法。
这一残酷的事实带来了一个重要实践经验：你最好尽一切可能一劳永逸，写出一个出色的  
具有工业强度的smart pointer来满足你所有的需求。此外，你通常不能修改一个类来适应
你的smart pointer，所以你的SmartPtr一定要足够灵活。
有不少类层次使用引用计数(reference counting)以及相应的函数管理对象的生存期。然而，
并没有reference counting的标准实现方法，每一个C++库的供应商在实现的语法和/或语义上
都有所不同。例如，在你的应用程序中有这样两个interfaces：
大部份的类实现了RefCounted接口: 
class RefCounted
{
public:
    void IncRef() = 0;
    bool DecRef() = 0; // if you DecRef() to zero
        // references, the object is destroyed
        // automatically and DecRef() returns true
    virtual ~RefCounted() {}
};
而由第三方提供的Widget类使用不同的接口：
class Widget
{
public:
    void AddReference();
    int RemoveReference(); // returns the remaining
        // number of references; it's the client's
        // responsibility to destroy the object
    ...
};
不过你并不想维护两个smart pointer类，你想让两种类共享一个SmartPtr。一个基於traits的
解决方案把两种不同的接口用语法和语义上统一的接口包装起来，建立针对普通类的通用模板，
而针对Widget建立一个特殊化版本，如下：
// Example 6: Reference counting traits
//
template <class T>
class RefCountingTraits
{
    static void Refer(T* p)
    {
        p->IncRef(); // assume RefCounted interface
    }
    static void Unrefer(T* p)
    {
        p->DecRef(); // assume RefCounted interface
    }
};
template <>
class RefCountingTraits<Widget>
{
    static void Refer(Widget* p)
    {
        p->AddReference(); // use Widget interface
    }
    static void Unrefer(Widget* p)
    {
        // use Widget interface
        if (p->RemoveReference() == 0)
            delete p;
    }
};
在SmartPtr里，我们像这样使用RefCountingTraits:
template <class T>
class SmartPtr
{
private:
    typedef RefCountingTraits<T> RCTraits;
    T* pointee_;
public:
    ...
    ~SmartPtr()
    {
        RCTraits::Unrefer(pointee_);
    }
}；
当然在上面的例子里，你可能会争论说你可以直接特殊化Widget类的SmartPtr的构造与析构函数。
你可以使用把模板特殊化技术用在SmartPtr本身，而不是用在trait上头，这样还可以消除额外的
类。尽管对这个问题来说这种想法没错，但还是由一些你需要注意的缺陷：
[译者为了说明起见，提供一个针对SmartPtr本身进行显式特殊化的范例供作者批判:]
// 通用类型版
template <class T>
class SmartPtr {
private:
    T *pointee_;
public:
    SmartPtr(T* pobj) {
 pointee_ = pobj;
 pobj->IncRef();
    }
    ...
    ~SmartPtr() {
 pointee_->DecRef();
    }
};
// Widget类专用版
template <>
class SmartPtr<Widget> {
private:
    T *pointee_;
public:
    SmartPtr(T* pobj) {
 pointee_ = pobj;
 pobj->AddReference();
    }
    ...
    ~SmartPtr() {
 if (pointee_->RemoveReference() == 0)
     delete pointee_;
    }
};
  &middot;这麽干缺乏可扩展性。如果给给SmartPtr再增加一个模板叁数，喏，全完蛋了  你不能特殊
    化这样一个SmartPtr<T. U>成员函数，其中模板叁数T是Widget，而U可以为其他任何类型。不，你做
    不到。附带说一句，smart pointer经常用作模板叁数。
[译者附释：作者说做不到的情形如下：
 template <class T, class U>
 class Demo {
 public:
 void dostuff() {...}  //Generic版本dostuff()
 };
 template<>
 void Demo<char, int>::dostuff() {...}  //这是可以的，针对成员函数进行specialize
 template <class U>
 void Demo<Widget, U>::dostuff() {...} // 这是不行的，编译器会去寻找一个Demo<Widget, U>的
                                       // partial specialization defination, 由于并没有这
           // 么一个defination，所以编译失败。
  &middot;最终代码不那麽清晰。Trait有一个名字，而且把相关的东西很好的组织起来，因此使用traits的
    代码更加容易理解。相比之下，用直接特殊化SmartPtr成员函数的代码，看上去更招黑客的喜欢。
  &middot;你没法对一种类型使用多种traits。
用继承机制的解决方案，就算本身完美无瑕，也至少存在上述的缺陷。解决这样一个变体问题，
使用继承实在是太笨重了。此外，通常用以取代继承方案的另一种经典机制    containment，
用在这里也显得画蛇添足，繁琐不堪。相反，traits方案乾净利落，简明有效，物合其用，恰到好处。
Traits的一个重要的应用是“interface glue”（接口胶合剂），通用的  可适应性极强的
适配子。如果不同的类对於一个给定的概念有  不同的实现，traits可以把这些实现再组织统
一成一个公共的接口。
对於一个给定类型提供多种TRAITS：现在，我们假设所有的人都很喜欢你的SmartPtr模板类，
直到有一天，在你的多线程应用程序里开始现了神秘的bug，美梦被惊醒了。你发现罪魁祸首
是Widget，它的引用计数函数并不是线程安全的。现在你不得不亲自实现Widget::AddReference和
Widget::RemoveReference，最合理的位置应该是在RefCountingTraits中，打上个补丁吧：
// Example 7: Patching Widget's traits for thread safety
//
template <>
class RefCountingTraits<Widget>
{
    static void Refer(Widget* p)
    {
        Sentry s(lock_); // serialize access
        p->AddReference();
    }
    static void Unrefer(Widget* p)
    {
        Sentry s(lock_); // serialize access
        if (p->RemoveReference() == 0)
            delete p;
    }
private:
    static Lock lock_;
};
不幸的是，虽然你重新编译  测试之後正确运行，但是程序慢得像蜗牛。仔细分析之後发现，你
刚才的所作所为往程序里塞了一个糟糕的瓶颈。实际上只有少数几个Widget是需要能够被好几个
线程访问的，余下的绝大多数Widget都是只被一个线程访问的。
你要做的是告诉编译器按你的需求分别使用多线程traits和单线程traits这两个不同版本。
你的代码主要使用单线程traits.
如何告诉编译器使用那个traits？这麽干：把traits作为另一个模板叁数传给SmartPtr。缺省
情况下传递老式的traits模板，而用特定的类型实例化特定的模板。
template <class T, class RCTraits = RefCountingTraits<T> >
class SmartPtr
{
    ...
};
你对单线程版的RefCountingTraits<Widget>不做改动，而把多线程版放在一个单独的类中：
class MtRefCountingTraits
{
    static void Refer(Widget* p)
    {
        Sentry s(lock_); // serialize access
        p->AddReference();
    }
    static void Unrefer(Widget* p)
    {
        Sentry s(lock_); // serialize access
        if (p->RemoveReference() == 0)
            delete p;
    }
private:
    static Lock lock_;
};
现在你可将SmartPtr<Widget>用于单线程目的，将SmartPtr<Widget, MtRefCountingTraits>
用于多线程目的。这就是了  就跟Scott Meyers可能会说的那样，“你要是没体会过快乐，
就不知道怎麽找乐。”
如果一种类型只要一个trait由可以应付，那麽只要用显式模板特殊化就够了。现在即使一个类型
需要多个trait来应付我们也搞得定。所以，traits必须能够从外界塞进来，而不是在内部“算出来”。
一个应当谨记的惯用法是提供一个traits类作为最後一个模板叁数。缺省的traits通过模板叁数缺
省值给定。
定义：一个traits类（与traits模板类相对）或者是一个traits模板类的实例，或者是一个与traits
模板类展现出相同接口的单独的类。
（完)
 
