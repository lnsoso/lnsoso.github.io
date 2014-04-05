---
layout: post
title: 设计模式之Life time controller模式
author: Alvin
date: !binary |-
  MjAwNi0xMS0yNyAxNjo0NTo0MiArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0yNyAwODo0NTo0MiArMDgwMA==
---
转至 <a href="http://www.Huihoo.org">www.Huihoo.org</a>

 (作者:Douglas C. Schmidt ,<font color="green"><em>by </em>huihoo.org</font> CORBA课题 Thzhang 译 , Allen整理,制作) 
<h3>目的</h3>对象生命周期管理者模式可以被用来控制对象的整个生命周期，从对象被首次使用前创建它们到应用程序中止前完全的销毁它们。此外通过在应用启动/中止时进行对象自动的预先创建/销毁，使这个模式能够用来替代静态对象的创建/销毁。 

<h3>例子</h3>单例（singleton）是一种通用的创建模式，它对唯一的类实例提供了一个全局的访问点同时能够延迟实例的创建直到它首次被访问。如果一个单例在程序 的整个生命周期中没有被需要，它将不会被创建。单例模式并没有提及在什么时候它的实例应该被销毁这个问题，但对于特定的应用或操作系统这将是个问题。 
为了说明为什么提及销毁语义是重要的，考虑下面的日志组件，它通过向客户提供编程API接口实现分布式的日志服务。 		 

<pre><code>
class Logger
{
public:
// Global access point to Logger singleton.
static Logger *instance (void) {
if (instance_ == 0)
instance_ = new Logger;
return instance_;
}
// Write some information to the log.
int log (const char *format, ...);
protected:
// Default constructor (protected to
// ensure Singleton pattern usage).
Logger (void);
static Logger *instance_;
// Contained Logger singleton instance.
// . . . other resources that are held by the singleton . . .
};
// Initialize the instance pointer.
Logger *Logger::instance_ = 0;
</code> </pre>
Logger的构造函数，出于简化而忽略掉了，实现分配各种OS的endsystem资源，像SOCKET句柄，共享内存段，和/或系统范围的信号量，它们被用于实现日志服务提供的客户API。 
为了减少尺寸，提高记录信息的可读性，一个应用可以选择用批处理而不是分立的方式来记录某些数据，像时间统计数据。例如，下面的统计类根据每个单独的标识，批量处理时间数据：  

<pre><code>
class Stats
{
public:
// Global access point to the statistics singleton.
static Stats *instance (void) {
if (instance_ == 0)
instance_ = new Stats;
return instance_;
}
// Record a timing data point.
int record (int id,const timeval &tv);
// Report recorded statistics to the log.
void report (int id) {
Logger::instance ()->
log ("Avg timing %d: "
"%ld sec %ld usec\n",
id,average_i (id).tv_sec,
average_i (id).tv_usec);
}
protected:
// Default constructor.
Stats (void);
// Internal accessor for an average.
const timeval &average_i (void);
// Contained Stats singleton instance.
static Stats *instance_;
// . . . other resources that are held by the instance . . .
};
// Initialize the instance pointer.
Stats *Stats::instance_ = 0;
}
</code> </pre>
在记录了各种统计数据后，程序调用report方法，它将使用Logger单例根据标识来记录平均的时间统计。 
Logger和Stats类对应用提供了截然不同的服务：Logger类提供了通用的日志能力，而Stats类提供了具体化的批量处理和记录时间统计功能。这些类都是使用单例模式实现的，于是在应用中每一个类都只有一个实例。 
下面的例子展示了一个应用可能使用Logger和Stats单例对象的一种情况： 

<pre><code>
int main (int argc, char *argv[])
{
// Interval timestamps.
timeval start_tv, stop_tv;
// Logger, Stats singletons do not yet exist.
// Logger and Stats singletons created  during the first iteration.
for (int i = 0; i < argc; ++i) {
::gettimeofday (&start_tv);
// do some work between timestamps . . .
::gettimeofday (&stop_tv);
// then record the stats . . .
timeval delta_tv;
delta_tv.sec = stop_tv.sec - start_tv.sec;
delta_tv.usec = stop_tv.usec - start_tv.usec;
Stats::instance ()->record (i, delta_tv);
// . . . and log some output.
Logger::instance ()->
log ("Arg %d [%s]\n", i, argv[i]);
Stats::instance()->report (i);
}
// Logger and Stats singletons are not cleaned up when main returns.
return 0;
}
</code> </pre>注意，应用没有清晰的创建或销毁Logger和Stats单例，也就是说它们的生命周期管理是与应用逻辑相分离的。这是一个通常的习惯在应用程序退出的时候不对单例对象进行销毁处理。 
但是，由于单例模式仅仅涉及单例对象的创建，而没有应对它的销毁，这将产生几个缺陷。值得注意的是，当上面的应用程序中止时，Logger和Stats单 例对象都没有被清理。在最好的情况下，这将导致内存泄漏的错误报告，最坏的情况下，重要的系统资源可能没有被完全的释放和销毁。 
例如，如果Logger和Stats单例对象持有OS的资源，像系统范围的信号量，I/O缓冲，或者其他被分配的OS资源，将产生问题。当程序关闭的时候 没有适当的释放这些资源可能导致死锁和其他同步灾难。为了减少这样的问题，在程序退出前每个单例实例的析构函数应该被调用。 
一种试图保证单例销毁的单例模式实现方法是，在一个文件范围内声明一个利用区域锁习惯用法实现的静态单例类实例。例如,下面的Singleton Destroyer模板提供了一个析构器用于销毁单例对象。 

<pre><code>
	template <class t=""> Singleton_Destroyer
{
public:
Singleton_Destroyer (void): t_ (0) {}
void register (T *) { t_ = t; }
Singleton_Destroyer (void) { delete t_; }
private:
T *t_; // Holds the singleton instance.
};
</class></code> </pre>
为了使用这个类，所有应该做的是通过定义一个静态的Singleton_Destroyer类实例来修改Logger和Stats类。下面的例子展示了对Logger类的修改： 
<pre><code>
static Singleton_Destroyer<logger> logger_destroyer;
// Global access point to the
// Logger singleton.
static Logger *instance (void) {
if (instance_ == 0) {
instance_ = new Logger;
// Register the singleton so it will be
// destroyed when the destructor of logger_destroyer is run.
logger_destroyer.register (instance_);
}
return instance_;
}
</logger></code> </pre>
注意，logger_destroyer是如何获取单例对象并在程序退出时将其销毁的。针对Stats类也有类似的修改。 
不幸的是，清晰的实例化一个静态的Singleton_Destroyer实例将带来一些问题。例如，在C++中，每一个 Singleton_Destroyer应该在不同的编译单元中定义（译者：内联定义。因为静态数据具有内联特性，一般不将其放在头文件中，因为每个包含 这个头文件的编译单元都将拥有一个静态数据的副本。）。在这种情况下，它们的析构函数的调用次序是没有保证的，这将导致未定义的行为。特别的，如果在不同 编译单元中的单例对象共享了一些资源，如SOCKET句柄，共享内存片段和/或系统范围的信号量，程序将不能干净的退出。在C++中单例对象的这种不确定 的销毁次序将很难保证（1）在最后一个使用资源的单例对象被完全的销毁前，而不是（2）在一个单例对象存在，并依然使用这些资源前，OS去回收这些资源。 
总的来说，在上面的例子中没有解决的关键问题是：(1)被单例分配的资源应该在程序退出时被最终释放。(2)不受约束的创建和销毁静态实例的次序将导致严重的程序错误。(3)将软件开发人员和管理对象生命周期的责任分离开来，将减少系统出错的可能。 

<h3>语境</h3>> 	一个应用和系统对其所创建的对象进行全面的生命周期控制，对于保证系统的正确性是必要的。 

<h3>问题</h3>许多应用并没有适当的处理对象的整个生命周期。特别是那些使用创建模式的应用，如单例模式，经常没有考虑这些对象的销毁。同样的，那些使用静态对象来提供对象销毁的应用经常遭受不一致的初始化和销毁过程。下面是这些问题的简要说明： 
1、单例对象析构带来的问题。单例对象的实例可能被动态创建，一个动态创建的单例实例必将会造成资源的泄漏，如果它们没有被销毁。但是，单例对象造成的泄漏经常被忽略，这是因为： 
（1）它们对应用不会造成显著的影响， 
（2）在大多数通用的操作系统中，如UNIX、WINDOWS，在程序中止时它们会被自动清理。 不幸的是，在下面的环境中资源泄漏会导致麻烦： 
（a）当系统需要优雅的关闭时。单例对象应该为其所获取的系统资源，如系统范围的锁、打开的网络连接、共享的内存片断负责。清晰的销毁单例对象是值得做的，这可以保证当程序中止时，所有这些资源能够在预定好的点上被销毁。 
（b）当一个单例对象拥有另一个单例对象的引用时。清晰的管理单例对象的销毁次序，对于避免在应用中止时由于空悬（dangling）引用产生的问题是必要的。 
（c）当检测内存泄漏时。内存检测工具，如NuMega BoundsCheck, ParaSoft Insure++,和Rational Purify，被用于检测像C/C++这类需要清晰的动态分配和释放内存的语言。这些工具会指示单例实例作为一处内存泄漏。 
（d）当从一个全局内存池中动态分配内存时。一些实时操作系统，如VxWorks和PSOS，有一个对应于所有应用的唯一的全局堆。因此，应用任务在中止时必须释放任何动态分配的内存，否则，这些内存不会被释放而被其他应用使用直到系统重新启动。 
2、静态对象生命周期的问题。一些对象必须先于任何使用前被创建。在C++语言中，这些实例往往被定义成静态变量，它们先于程序主函数入口点被调用前创建，在程序中止时被销毁。但是静态对象有几个严重的缺陷： 
（a）不确定的构造/析构的次序。C++语言仅仅指定了在一个编译单元内部静态对象的构造/析构的次序。构造的次序是对象声明的次序、析构则与之相反。但 是没有指定处于不同编译单元的静态对象的构造/析构的次序。因此构造/析构的次序是实现依赖的。使用静态对象来处理初始化相关性是很难写出可以移植的C+ +代码。通常，比较简单的方式是完全避免使用静态对象，而不是解决、防止这些相关性。为了在某些平台上实现正确的程序操作，清晰的单例管理是必须的，因为 它们能够事先销毁单例对象。例如，早先JDK中的垃圾回收器，能够销毁没有任何参考指向的对象，即使这个对象是被设置成单例。虽然这个问题在后续的JDK 中被解决，但是对象生命周期管理者能够在的应用的控制下，通过维护单例的参考来解决这个问题。 
（b）不能被嵌入式系统很好的支持。由于历史原因，嵌入系统都是使用C语言因此，它们总是不能对OO程序语言特性提供无缝的支持。例如，在C+ +语言中的静态对象的构造/析构经常会复杂化嵌入系统的编程。嵌入式OS可能已经支持清晰的调用静态的构造/析构函数，但没有达到程序员期望的最佳状态。 一些嵌入式OS不支持一个程序有一个唯一的入口点的概念。例如VxWorks支持多个task，task类似于线程因为它们共享一个地址空间。但是对于每 一个应用并没有指定主task。因此，这些嵌入式系统可以被配置成在模块载入/卸载时分别的调用静态对象的构造器/析构器。 
（c）静态对象增加了应用启动的时间。静态对象可能先于任何主入口点的调用，在应用启动时被初始化。如果这些对象在一个特定的运行中没有被使 用，那么应用启动（退出）的时间被白白的增加。减少这种浪费的一种方法是，使用安需创建对象替代静态对象，如使用单例模式。使用单例替代静态对象也可以用 于延迟对象对象的构建直到被首次使用时，而且这也将减少启动时间。一些实时的应用已经发现在进入主入口点后和对象被正是需要前的一个特定时间点创建单例对 象带来的好处。一个或多个这样静态对象的缺点就足以提供将它们从程序中移走的全部动机。通常，比较好的做法是不使用它们，而是应用下面的方案替代。 

<h3>解决之道</h3>定义一个对象生命周期管理者，它是一个包含了预分配对象和被管理对象集合的单例。它的职责是在应用启动和中止时分别创建和销毁预分配对象。它更深一层的职责是在程序中止时保证所有的被管理对象能够被完全的销毁。 

<h3>适应性</h3>在下列情况下使用对象生命周期管理者： 
1、在程序中止时，单例或其他动态分配的对象能够在不需要程序本身进行干预的情况下被销毁。单例和其他创建模式都没有提及它们所创建的对 象应该什么时候被销毁和由谁来销毁这个问题。与之相反，对象生命周期管理者提供了一个便利的用于删除被动态创建对象的全局对象。由创建模式产生的对象可以 向对象生命周期管理者进行注册，确保在程序中止时能够被销毁。 
2、静态对象必须从应用中移除。正像前面所描述的那样，静态对象是麻烦的制造者，特别是在某些语言和某些平台上。对象生命周期管理者提供了一种利用预先分配对象替代静态对象的机制。预分配对象在应用使用它们前被创建，在应用中止前被销毁。 
3、对于那些不支持静态对象创建和销毁的平台。一些嵌入式系统，如VXWORKS和PSOS不总是在程序启动时创建静态对象，在程序中止时销毁静态对象。通常，比较好的方式是移除静态对象。 
4、虽然应用需要这样，但是底层平台不能提供一个主程序的概念。在缺少对主程序概念支持的平台上，将缺少对静态对象创建和销毁的支持。对象生命周期管理者通过划分地址空间的方式可以被用来仿效一个主程序。从应用的角度来看，每一个对象生命周期管理者描绘了一个程序的范畴。 
5、对象被销毁的次序必须由应用来指定。动态创建的对象可以通过向对象生命周期管理者注册方式来使自身被销毁。对象生命周期管理者可以以任意希望的次序来实现对象的销毁。 
6、应用需要一个明确的单例对象管理机制。像上面所描述的，单例对象可能被过早的销毁。如早期的JAVA平台。对象生命周期管理者可以延迟对象的销毁直到应用中止。 

<h3>结构和参与者</h3>在下面的图中，使用UML方式展示了对象生命周期管理者的结构和参与者。 对象生命周期管理者：每一个对象生命周期管理者，它是一个包含了预分配对象和被管理对象集合的单例。 
<center> <img src="http://www.huihoo.com/ace_tao/i/lifecycle1.jpg" alt="" /> </center> 
1、被管理对象：任何一个向对象生命周期管理者注册并由其负责销毁的对象。对象销毁发生在对象生命周期管理者本身被销毁的时候，通常都是在程序中止的时候。 
2、预分配对象：被对象生命周期管理者在其内部通过硬编码方式实现创建和销毁的对象。它和对象生命周期管理者具有相同的生命周期，也就是执行应用的进程的生命周期。 
3、应用：应用清晰或非清晰的创建和销毁对象生命周期管理者。此外，应用向本身可能包含预分配对象的对象生命周期管理者注册被管理对象。 

<h3>动态特征</h3>下图中展示了对象生命周期管理者模式中的参与者之间的动态协作： 
这个图描述了四个分离的活动： 
<center> <img src="http://www.huihoo.com/ace_tao/i/lifecycle2.jpg" alt="" /> </center>  
1、对象生命周期管理者创建和初始化，它将依次创建预分配对象。 
2、应用创建管理对象，并向对象生命周期管理者注册。 
3、应用使用已经注册的管理对象和预先分配对象。 
4、对象生命周期管理者的销毁，这将销毁所有它控制的管理对象和预分配对象。 

<h3>实现</h3>对象生命周期管理者可以用下面展示的步骤来实现。这个实现是基于ACE框架提供的对象管理者来实现的，它将引出一些将在这个单元讨论的有趣问题。下面讨论的一些步骤是语言相关的，因为ACE是用C++语言编写的。 
1、定义对象生命周期管理者组件。这个组件向应用提供一个接口，通过它注册那些生命周期必须被管理对象，确保在系统中止时能够完全的销毁这些对 象。此外，这个组件还定义了一个智囊团（repository）用于确保它所管理的对象能够被完全销毁。对于那些被注册，在程序中止时被销毁的预分配对象 和管理对象来说，对象生命周期管理者是一个容器。 
下面是用于实现对象生命周期管理者组件的子步骤： 
（a）定义一个用于注册被管理对象的接口。一种用于向对象生命周期管理者注册被管理对象的方法是使用C语言库中的atexit函数，它在程序退出 时将调用特定的中止函数（用于实现退出时的清理操作）。但是，不是所有的平台都支持atexit函数，而且atexit函数的实现限制了最大被注册的中止 函数数目为32。因此。对象生命周期管理者必须支持下面的两个技术用于实现被管理对象的注册：使用一个容器来容纳所有的被管理对象，在程序退出时自动清除 这些被管理对象。 
i.定义一个cleanup函数接口。对象生命周期管理者允许应用向其注册任意类型的对象。当程序中止时，对象生命周期管理者将自动的清理这些对象。下面的C++类展示了一个在ACE中使用的特殊的CLEANUP_FUNC，用于注册能够被清除的对象或数组。 

<pre><code>
typedef void (*CLEANUP_FUNC)(void *object,void *param);
class Object_Lifetime_Manager
{
public:
static int at_exit (void *object,
CLEANUP_FUNC cleanup_hook,
void *param);
};
</code> </pre>
这个静态的at_exit函数注册一个能够在程序退出时被清除的对象或数组对象。Cleanup_hook参数指向全局的函数或时静态的 方法，该方法在清理时被调用用于销毁对象或数组。在销毁时，对象生命周期管理者向Cleanup_hook函数传递对象和相关的参数。参数包含任何被 Cleanup_hook函数需要的额外信息，如数组中对象的个数等。 
ii.定义一个cleanup基类接口。这个接口允许应用向对象生命周期管理者注册销毁任何从cleanup接口继承的对象。这个 cleanup基类接口应该有一个虚的析构函数和一个虚的cleanup方法，这个方法的实现仅仅是简单的调用delete this，它将导致所有继承类的析构函数被依次调用。下面的代码段展示了在ACE中这个接口是如何实现的： 

<pre><code>
class Cleanup
{
public:
// Destructor.
virtual ?Cleanup (void);
// By default, simply deletes this.
virtual void cleanup (void *param = 0);
};
</code> </pre>
下面的代码段展示了在ACE中，对象生命周期管理者被用于注册从cleanup接口继承对象的接口。
<pre><code>
class Object_Lifetime_Manager
{
public:
static int at_exit (Cleanup *object,
void *param = 0);
};
<code> </code></code></pre>
这个静态的at_exit函数注册一个能够在程序退出时被清除的cleanup对象。在析构时，对象生命周期管理者调用cleanup对象中的cleanup方法，param参数包含了任何被cleanup函数需要的额外信息。 
（b）定义一个单例适配器。虽然使用上面定义的对象生命周期管理者方法是可以清晰的编码实现单例对象，但这样做是冗余的和易错的。因此，定义一个 单例的适配器模板用于封装创建单例对象和向对象生命周期管理者注册的细节是非常有用的。此外，单例适配器可以使用线程安全的双检测加锁优化模式 （double checked locking optimization pattern）来创建和访问类型指定的单例对象实例。 下面的代码片断展示了单例适配器在ACE中是如何实现的： 

<pre><code>
template <class type="">
class Singleton : public Cleanup
{
public:
// Global access point to the	wrapped singleton.
static TYPE *instance (void) {
// Details of Double Checked  Locking Optimization omitted . . .
if (singleton_ == 0) {
singleton_ = new Singleton<type>;
// Register with the Object Lifetime
// Manager to control destruction.
Object_Lifetime_Manager::at_exit (singleton_);
}
return &singleton_->instance_;
}
protected:
// Default constructor.
Singleton (void);
// Contained instance.
TYPE instance_;
// Instance of the singleton adapter.
static Singleton<type> *singleton_;
};
</type></type></class></code> </pre>
Singleton类模板从cleanup类继承，这样就允许单例实例向对象生命周期管理者注册自己 
（d）定义一个用于注册预分配对象的接口。预分配对象总是在应用的主进程启动前被创建。例如，在一些应用中，同步锁必须在任何使用前被创建，用于 防止竞争条件。这样一来，这些对象必须在每个对象生命周期管理者类中通过硬编码的方式实现。将这些对象的创建封装在对象生命周期管理者的初始化阶段，不给 应用代码添加任何的复杂性。 
对象生命周期管理者能够预先分配对象或数组。它要么能够静态的在全局数据中实现这些预先分配，要么在堆中动态的实现。一种有效的实现方式是在数 组中存储每个预分配对象。像C++这种特定的语言不支持数组容纳异类对象。因此，用指针替代对象本身来实现数组存储。实际的对象是被对象生命周期管理者在 其初始化的过程中动态创建的，在其析构的时候被销毁。 
下面的子步骤用于实现预分配对象： 
1.	限制暴露。为了最小化头文件的暴露，通过宏或枚举来标识预分配对象。 
<pre><code>
enum Preallocated_Object_ID
{
ACE_FILECACHE_LOCK,
ACE_STATIC_OBJECT_LOCK,
ACE_LOG_MSG_INSTANCE_LOCK,
ACE_DUMP_LOCK,
ACE_SIG_HANDLER_LOCK,
ACE_SINGLETON_NULL_LOCK,
ACE_SINGLETON_RECURSIVE_THREAD_LOCK,
ACE_THREAD_EXIT_LOCK,
};
</code> </pre>
2.	使用cleanup适配器。Cleanup适配器类模板从cleanup基类继承，包装那些没有从cleanup基类继承的类型，使它们能够被对象生命周期管理者管理起来。 

<pre><code>
#define PREALLOCATE_OBJECT(TYPE, ID) {\
Cleanup_Adapter<type> *obj_p;\
obj_p = new Cleanup_Adapter<type>;\
preallocated_object[ID] = obj_p;\
}
#define DELETE_PREALLOCATED_OBJECT(TYPE, ID)\
cleanup_destroyer (\
static_cast<cleanup_adapter><type> *>\
(preallocated_object[ID]), 0);\
preallocated_object[ID] = 0;
</type></cleanup_adapter></type></type></code> </pre>
3.定义预分配对象的访问接口。应用需要简便的和类型安全的预分配对象的访问接口。因为对象生命周期管理者支持不同类型的预分配对象。提供一个分 离的，包含有一个接受一个ID参数的成员函数的，在函数内部对象被预先分配，返回一个指向对象的正确的类型指针的类模板适配器是必要的。 下面的代码片断展示了在ACE中通过类模板适配器如何实现这个接口： 

<pre><code>
template <class type="">
class Preallocated_Object_Interface
{
public:
static TYPE *
get_preallocated_object
(Object_Lifetime_Manager::
Preallocated_Object_ID id)
{
// Cast the return type of the object
// pointer based on the type of the
// function template parameter.
return
&(static_cast<cleanup_adapter><type> *>
(Object_Lifetime_Manager::
preallocated_object[id]))->object ();
}
// . . . other methods omitted.
};
</type></cleanup_adapter></class></code> </pre>
（e）定义被注册对象的析构次序。对象生命周期管理者可以实现以任意次序销毁被注册对象。例如，可以使用优先级别标注，使销毁的次序按照优先级降 序进行。应该提供一个用于设定和改变对象优先级别的接口函数。我们已经发现，以注册顺序相反的次序销毁对象的策略对于ACE应用来说已经足够了。一个应用 可以通过控制对象的注册次序来指定对象的销毁次序。 
（f）定义一个中止函数接口。到目前为止所讨论的生命周期管理功能仅涉及在程序中止时销毁被管理对象。但是对象生命周期管理者能够提供一个更通 常的功能：使用其内部的相同实现机制在程序中止时能够调用一个函数。 例如，为了在程序中止时确保完全清楚打开的win32 SOCKET,WSACleanup函数必须被调用。可以使用一个区域锁习惯用法的变化实现，创建一个特殊的包装门面类，它的构造函数会调用初始化函数， 它的析构函数会调用cleantup函数。于是，应用可以向对象生命周期管理者注册这样类的实现，这样一来在对象生命周期管理者销毁时会销毁内部的被管理 对象，中止API也将会被调用。 
但是，这样的设计对大多数应用来说太困难了，而且它还是容易出错的，因为应用必须保证这个类被用作单例对象，因为这些API函数只能被调用一次。 作为替代，这些API中止函数将被作为对象生命周期管理者的中止方法的一部分被调用。 
（g）移动通用接口和实现细节到一个基础类中。分解一些内部的细节到Object Lifetime Manager Base类中将使Object Lifetime Manager的实现更加简单，健壮。定义一个Object Lifetime Manager Base类用于支持创建不同类型的Object Lifetime Manager。 
3、确定如何管理对象生命周期管理者自己的生命周期。对象生命周期管理者有责任初始化其他全局对象和静态对象，但是这将引发连带的问题，就是这个单例对象如何初始化和析构它自己？下面是用于初始化对象生命周期管理者实例的几种选择： 
（a）静态初始化。如果应用对静态对象的创建和销毁没有次序限制，可以创建对象生命周期管理者为静态对象。例如，ACE中的对象生命周期管理者可以被作为静态对象创建。  
（b）栈初始化。当一个主程序线程明确的定义了程序的入口点和中止点时，可以在主程序线程的栈上创建对象生命周期管理者，这样可以简化创建和销毁 对象生命周期管理者的编程逻辑。这种用于初始化对象生命周期管理者的方法是假设每个程序中只有一个唯一的主线程。这个线程确定了程序本身，也就是，程序在 运行，当且仅当主线程是活动的。这个方法有一个显著的优点：对象生命周期管理者实例在每一条离开MAIN函数的路径上都将被自动的销毁。 
（c）清晰的初始化。这个方法是，在应用程序的控制之下，清晰的创建对象生命周期管理者实例，对象生命周期管理者类中init和fini方法允许应用在任何需要的时候创建和销毁对象生命周期管理者实例。这个选择减轻了在使用DLL带来的复杂度。 
（d）动态库初始化。在这个方法中，创建和销毁对象生命周期管理者分别在它的DLL被加载和卸载的时候。许多动态库工具都包含调用如下方法能力：(1)一个出世化方法当DLL被加载时（2）一个中止函数当DLL被卸载时。 

<h3>结论</h3>使用它带来的好处： 	
1、	在程序中止时销毁单例对象和其他被管理对象。对象生命周期管理者模式允许应用"干净的"中止，释放被管理对象占用的内存，连同它们持有的其他资源。 
2、	指定销毁的次序。对象被销毁的顺序可以被指定。这个指定的销毁机制可以任意复杂任意简单。 
3、	从库和应用中移除了静态对象。可以使用预分配对象替代静态对象。这将防止应用依赖于静态对象的创建和销毁的次序。 
使用它带来的缺陷: 
1、管理者自己的生命周期管理。应用本身必须保证尊重对象生命周期管理者的生命周期，不在其外调用对象生命周期管理者的服务。例如，应用不要先于 对象生命周期管理者完全出世化之前试图去访问预分配对象，同样的，应用不要先于最后访问预分配对象或被管理对象前销毁对象生命周期管理者。最后，如果可以 假定对象生命周期管理者仅被一个线程初始化，那么实现对象生命周期管理者是非常简单的。这将免除在其初始化函数中使用静态锁 
2、和共享库一起使用。在支持共享库运行时动态加载和卸载的平台上，应用程序必须小心的对待平台指定的特性对对象生命周期管理者的生命周期的影 响。例如，在windows NT平台上，对象生命周期管理者应该被应用或是包含它的DLL来初始化，这将避免潜在死锁状态，由于OS内部已经串行化装载DLL操作。 一个相关的问题是在DLL创建单例对象，被应用代码中的对象生命周期管理者管理。当DLL先于应用中止前被卸载，那么在应用中止时，对象生命周期管理者就 会试图去使用已经不在应用中的代码来销毁这个单例对象。 

<h3>实现样例代码</h3>下面的代码仅仅展示了预分配对象的处理过程。（译者：而被管理对象的处理过程被忽略了，这个过程比较复杂，涉及较多的ACE其他的封装类，包括ACE_OS_Exit_Info、ACE_Cleanup_Info_Node等。后面将有比较详细的讨论。） 

<pre><code>
class Object_Lifetime_Manager_Base
{
public:
virtual int init (void) = 0;
// Explicitly initialize. Returns 0 on success,
// -1 on failure due to dynamic allocation
// failure (in which case errno is set to
// ENOMEM), or 1 if it had already been called.
virtual int fini (void) = 0;
// Explicitly destroy. Returns 0 on success,
// -1 on failure because the number of <fini>
// calls hasn't reached the number of <init>
// calls, or 1 if it had already been called.
enum Object_Lifetime_Manager_State {
OBJ_MAN_UNINITIALIZED,
OBJ_MAN_INITIALIZING,
OBJ_MAN_INITIALIZED,
OBJ_MAN_SHUTTING_DOWN,
OBJ_MAN_SHUT_DOWN
};
protected:
Object_Lifetime_Manager_Base (void) :
object_manager_state_ (OBJ_MAN_UNINITIALIZED),
dynamically_allocated_ (0),
next_ (0) {}
virtual ?Object_Lifetime_Manager_Base (void) {
// Clear the flag so that fini
// doesn't delete again.
dynamically_allocated_ = 0;
}
int starting_up_i (void) {
return object_manager_state_ <
OBJ_MAN_INITIALIZED;
}
// Returns 1 before Object_Lifetime_Manager_Base
// has been constructed. This flag can be used
// to determine if the program is constructing
// static objects. If no static object spawns
// any threads, the program will be
// single-threaded when this flag returns 1.
int shutting_down_i (void) {
return object_manager_state_ >
OBJ_MAN_INITIALIZED;
}
// Returns 1 after Object_Lifetime_Manager_Base
// has been destroyed.
Object_Lifetime_Manager_State object_manager_state_;
// State of the Object_Lifetime_Manager;
u_int dynamically_allocated_;
// Flag indicating whether the
// Object_Lifetime_Manager instance was
// dynamically allocated by the library.
// (If it was dynamically allocated by the
// application, then the application is
// responsible for deleting it.)
Object_Lifetime_Manager_Base *next_;
// Link to next Object_Lifetime_Manager,
// for chaining.
};
class Object_Lifetime_Manager :
public Object_Lifetime_Manager_Base
{
public:
virtual int init (void);
virtual int fini (void);
static int starting_up (void) {
return instance_ ?
instance_->starting_up_i () : 1;
}
static int shutting_down (void) {
return instance_ ?
instance_->shutting_down_i () : 1;
}
enum Preallocated_Object
{
# if defined (MT_SAFE) && (MT_SAFE != 0)
OS_MONITOR_LOCK,
TSS_CLEANUP_LOCK,
# else
// Without MT_SAFE, There are no
// preallocated objects. Make
// sure that the preallocated_array
// size is at least one by declaring
// this dummy.
EMPTY_PREALLOCATED_OBJECT,
# endif /* MT_SAFE */
// This enum value must be last!
PREALLOCATED_OBJECTS
};
// Unique identifiers for Preallocated Objects.
static Object_Lifetime_Manager *instance (void);
// Accessor to singleton instance.
public:
// Application code should not use these
// explicitly, so they're hidden here. They're
// public so that the Object_Lifetime_Manager
// can be onstructed/destructed in main, on
// the stack.
Object_Lifetime_Manager (void) {
// Make sure that no further instances are
// created via instance.
if (instance_ == 0)
instance_ = this;
init ();
}
?Object_Lifetime_Manager (void) {
// Don't delete this again in fini.
dynamically_allocated_ = 0;
fini ();
}
private:
static Object_Lifetime_Manager *instance_;
// Singleton instance pointer.
static void *
preallocated_object[PREALLOCATED_OBJECTS];
// Array of Preallocated Objects.
};
Object_Lifetime_Manager *
Object_Lifetime_Manager::instance_ = 0;
// Singleton instance pointer.
Object_Lifetime_Manager *
Object_Lifetime_Manager::instance (void)
{
// This function should be called during
// construction of static instances, or
// before any other threads have been created
// in the process. So, it's not thread safe.
if (instance_ == 0) {
Object_Lifetime_Manager *instance_pointer =
new Object_Lifetime_Manager;
// instance_ gets set as a side effect of the
// Object_Lifetime_Manager allocation, by
// the default constructor. Verify that . . .
assert (instance_pointer == instance_);
instance_pointer->dynamically_allocated_ = 1;
}
return instance_;
}
int
Object_Lifetime_Manager::init (void)
{
if (starting_up_i ()) {
// First, indicate that this
// Object_Lifetime_Manager instance
// is being initialized.
object_manager_state_ = OBJ_MAN_INITIALIZING;
if (this == instance_) {
# if defined (MT_SAFE) && (MT_SAFE != 0)
PREALLOCATE_OBJECT (mutex_t,
OS_MONITOR_LOCK)
// Mutex initialization omitted.
PREALLOCATE_OBJECT (recursive_mutex_t,
TSS_CLEANUP_LOCK)
// Recursive mutex initialization omitted.
# endif /* MT_SAFE */
// Open Winsock (no-op on other
// platforms).
socket_init (/* WINSOCK_VERSION */);
// Other startup code omitted.
}
// Finally, indicate that the
// Object_Lifetime_Manager instance
// has been initialized.
object_manager_state_ = OBJ_MAN_INITIALIZED;
return 0;
} else {
// Had already initialized.
return 1;
}
}
int
Object_Lifetime_Manager::fini (void)
{
if (shutting_down_i ())
// Too late. Or, maybe too early. Either
// <fini> has already been called, or
// <init> was never called.
return object_manager_state_ ==
OBJ_MAN_SHUT_DOWN ? 1 : -1;
// Indicate that the Object_Lifetime_Manager
// instance is being shut down.
// This object manager should be the last one
// to be shut down.
object_manager_state_ = OBJ_MAN_SHUTTING_DOWN;
// If another Object_Lifetime_Manager has
// registered for termination, do it.
if (next_) {
next_->fini ();
// Protect against recursive calls.
next_ = 0;
}
// Only clean up Preallocated Objects when
// the singleton Instance is being destroyed.
if (this == instance_) {
// Close down Winsock (no-op on other
// platforms).
socket_fini ();
// Cleanup the dynamically preallocated
// objects.
# if defined (MT_SAFE) && (MT_SAFE != 0)
// Mutex destroy not shown . . .
DELETE_PREALLOCATED_OBJECT (mutex_t,
MONITOR_LOCK)
// Recursive mutex destroy not shown . . .
DELETE_PREALLOCATED_OBJECT (
recursive_mutex_t,
TSS_CLEANUP_LOCK)
# endif /* MT_SAFE */
}
// Indicate that this Object_Lifetime_Manager
// instance has been shut down.
object_manager_state_ = OBJ_MAN_SHUT_DOWN;
if (dynamically_allocated_)
delete this;
if (this == instance_)
instance_ = 0;
return 0;
}
</init></fini></init></fini></code> </pre>

<h3>译者补充</h3>被管理对象的处理类图如下： 
<center> <img src="http://www.huihoo.com/ace_tao/i/lifecycle3.gif" alt="" /> </center> 
每个ACE_Object_Manager包含一个私有的ACE_OS_Exit_Info成员。当应用通过 ACE_Object_Manager提供的at_exit函数注册被管理对象时，这个函数将调用ACE_Object_Manager中的一个私有函数 at_exit_i，这个函数将调用ACE_OS_Exit_Info成员中的at_exit_i函数。在这个函数中进行如下操作： 
ACE_Cleanup_Info new_info;  
new_info.object_ = object; 
new_info.cleanup_hook_ = cleanup_hook;  
new_info.param_ = param; 
registered_objects_ ＝ registered_objects_->insert 
(new_info)；其中registered_objects是指向ACE_Cleanup_Info_Node的指针，是ACE_OS_Exit_Info的私有数据成员。 
ACE_Cleanup_Info_Node本身构成一个单向的链表，其包含一个ACE_Cleanup_Info实例和指向下一个ACE_Cleanup_Info_Node节点的指针。 
这样，应用注册的被管理对象的指针、中止函数和需要参数都将以链表的形式存放在ACE_OS_Exit_Info中。当 ACE_Object_Manager退出时，其会调用其内部的fini函数，该函数将调用ACE_OS_Exit_Info中的call_hooks函 数。这个函数按照注册相反的顺序一次遍历每个ACE_Cleanup_Info_Node，并执行其中的 
ACE_Cleanup_Info中的相关退出操作： 

<pre><code>
for (ACE_Cleanup_Info_Node *iter = registered_objects_;
       iter  &&  iter->next_ != 0;
       iter = iter->next_)
    {
      ACE_Cleanup_Info &info = iter->cleanup_info_;
      // The object is an ACE_Cleanup.
      ace_cleanup_destroyer (ACE_reinterpret_cast (ACE_Cleanup *,
                                                     info.object_),
                               info.param_);
     }
</code> </pre>
