---
layout: post
title: 设计模式之Double Checked Locking模式
author: Alvin
date: !binary |-
  MjAwNi0xMS0yNyAxNTo1MzozOCArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0yNyAwNzo1MzozOCArMDgwMA==
---
转至 <a href="http://www.Huihoo.org">www.Huihoo.org</a>

 (作者:Douglas C. Schmidt ,<font color="green"><em>by </em>huihoo.org</font> CORBA课题 Thzhang 译 , Allen整理,制作) 
<h3>意图</h3>无论什么时候当临界区中的代码仅仅需要加锁一次，同时当其获取锁的时候必须是线程安全的，可以用Double Checked Locking 模式来减少竞争和加锁载荷。 

<h3>动机</h3>1、标准的单例。开发正确的有效的并发应用是困难的。程序员必须学习新的技术（并发控制和防止死锁的算法）和机制（如多线程和同步API）。此外，许多熟 悉的设计模式（如单例和迭代子）在包含不使用任何并发上下文假设的顺序程序中可以工作的很好。为了说明这点，考虑一个标准的单例模式在多线程环境下的实 现。单例模式保证一个类仅有一个实例同时提供了全局唯一的访问这个实例的入口点。在c++程序中动态分配单例对象是通用的方式，这是因为c++程序没有很 好的定义静态全局对象的初始化次序，因此是不可移植的。而且，动态分配避免了单例对象在永远没有被使用情况下的初始化开销。 

<pre><code>
class Singleton
{
public:
static Singleton *instance (void)
{
if (instance_ == 0)
// Critical section.
instance_ = new Singleton;
return instance_;
}
void method (void);
// Other methods and members omitted.
private:
static Singleton *instance_;
};

</code> </pre>
应用代码在使用单例对象提供的操作前，通过调用静态的instance方法来获取单例对象的引用，如下所示： 
Singleton::instance ()->method (); 
2、问题：竞争条件。不幸的是，上面展示的标准单例模式的实现在抢先多任务和真正并行环境下无法正常工作。例如，如果在并行主机上运行的多个线程在单例对 象初始化之前同时调用Singleton::instance方法，Singleton的构造函数将被调用多次，这是因为多个线程将在上面展示的临界区中 执行new singleton操作。临界区是一个必须遵守下列定式的指令序列：当一个线程/进程在临界区中运行时，没有其他任何线程/进程会同时在临界区中运行。在 这个例子中，单例的初始化过程是一个临界区，违反临界区的原则，在最好的情况下将导致内存泄漏，最坏的情况下，如果初始化过程不是幂等的 （idempotent.），将导致严重的后果。 
3、	通常的陷阱和弊端。实现临界区的通常方法是在类中增加一个静态的Mutex对象。这个Mutex保证单例的分配和初始化是原子操作，如下：  

<pre><code>
class Singleton
{
public:
static Singleton *instance (void)
{
// Constructor of guard acquires lock_ automatically.
Guard<mutex> guard (lock_);
// Only one thread in the critical section at a time.
if (instance_ == 0)
instance_ = new Singleton;
return instance_;
// Destructor of guard releases lock_ automatically.
}
private:
static Mutex lock_;
static Singleton *instance_;
};
</mutex></code> </pre>
guard类使用了一个c++的习惯用法，当这个类的对象实例被创建时，它使用构造函数来自动获取一个资源，当类对象离开一个区域时，使用析构器 来自动释放这个资源。通过使用guard，每一个对Singleton::instance方法的访问将自动的获取和释放lock_。 
即使这个临界区只是被使用了一次，但是每个对instance方法的调用都必须获取和释放lock_。虽然现在这个实现是线程安全的，但过多的加锁负载是不能被接受的。一个明显（虽然不正确）的优化方法是将guard放在针对instance进行条件检测的内部： 

<pre><code>
static Singleton *instance (void)
{
if (instance_ == 0) {
Guard<mutex> guard (lock_);
// Only come here if instance_ hasn't been initialized yet.
instance_ = new Singleton;
}
return instance_;
}
</mutex></code> </pre>这将减少加锁负载，但是不能提供线程安全的初始化。在多线程的应用中，仍然存在竞争条件，将导致多次初始化instance_。例如，考虑两 个线程同时检测 instance_ == 0，都将会成功，一个将通过guard获取lock_另一个将被阻塞。当第一线程初始化Singleton后释放lock_，被阻塞的线程将获取 lock_，错误的再次初始化Singleton。 
4、解决之道，Double Checked Locking优化。解决这个问题更好的方法是使用Double Checked Locking。它是一种用于清除不必要加锁过程的优化模式。具有讽刺意味的是，它的实现几乎和前面的方法一样。通过在另一个条件检测中包装对new的调 用来避免不必要的加锁： 

<pre><code>
class Singleton
{
public:
static Singleton *instance (void)
{
// First check
if (instance_ == 0)
{
// Ensure serialization (guard constructor acquires lock_).
Guard<mutex> guard (lock_);
// Double check.
if (instance_ == 0)
instance_ = new Singleton;
}
return instance_;
// guard destructor releases lock_.
}
private:
static Mutex lock_;
static Singleton *instance_;
};
</mutex></code> </pre>
第一个获取lock_的线程将构建Singleton，并将指针分配给instance_，后续调用instance方法的线程将发现 instance_ != 0，于是将跳过初始化过程。如果多个线程试图并发初始化Singleton，第二个检测件阻止竞争条件的发生。在上面的代码中，这些线程将在lock_上 排队，当排队的线程最终获取lock_时，他们将发现instance_ != 0于是将跳过初始化过程。 
上面Singleton::instance的实现仅仅在Singleton首次被初始化时，如果有多个线程同时进入instance方法将导致加锁负 载。在后续对Singleton::instance的调用因为instance_ != 0而不会有加锁和解锁的负载。 通过增加一个mutex和一个二次条件检测，标准的单例实现可以是线程安全的，同时不会产生过多的初始化加锁负载。 

<h3>适应性</h3>> 当一个应用具有下列特征时，可以使用Double Checked Locking优化模式： 
1、应用包含一个或多个需要顺序执行的临界区代码。 
2、多个线程可能潜在的试图并发执行临界区。 
3、临界区仅仅需要被执行一次。 
4、在每一个对临界区的访问进行加锁操作将导致过多加锁负载。 
5、在一个锁的范围内增加一个轻量的，可靠的条件检测是可行的。 

<h3>结构和参与者</h3>通过使用伪代码能够最好地展示Double Checked Locking模式的结构和参与者，图1展示了在Double Checked Locking模式有下列参与者： 
<center> <img src="http://www.huihoo.com/ace_tao/i/dcl1.jpg" alt="" /> </center>  
1、仅有一次临界区（Just Once Critical Section,）。临界区所包含的代码仅仅被执行一次。例如，单例对象仅仅被初始化一次。这样，执行对new Singleton的调用（只有一次）相对于Singleton::instance方法的访问将非常稀少。 
2、mutex。锁被用来序列化对临界区中代码的访问。 
3、标记。标记被用来指示临界区的代码是否已经被执行过。在上面的例子中单例指针instance_被用来作为标记。 
4、	应用线程。试图执行临界区代码的线程。 

<h3>协作</h3>图2展示了Double Checked Locking模式的参与者之间的互动。作为一种普通的优化用例，应用线程首先检测flag是否已经被设置。如果没有被设置，mutex将被获取。在持有 这个锁之后，应用线程将再次检测flag是否被设置，实现Just Once Critical Section，设定flag为真。最后应用线程释放锁。 
<center> <img src="http://www.huihoo.com/ace_tao/i/dcl2.jpg" alt="" /> </center> 

<h3>结论</h3>使用Double Checked Locking模式带来的几点好处： 
1、最小化加锁。通过实现两个flag检测，Double Checked  Locking模式实现通常用例的优化。一旦flag被设置，第一个检测将保证后续的访问不要加锁操作。 
2、防止竞争条件。对flag的第二个检测将保证临界区中的事件仅实现一次。 
使用Double Checked Locking模式也将带来一个缺点：产生微妙的移植bug的潜能。这个微妙的移植问题能够导致致命的bug，如果使用Double Checked Locking模式的软件被移植到没有原子性的指针和正数赋值语义的硬件平台上。例如，如果一个instance_指针被用来作为Singleton实现 的flag，instance_指针中的所有位（bit）必须在一次操作中完成读和写。如果将new的结果写入内存不是一个原子操作，其他的线程可能会试 图读取一个不健全的指针，这将导致非法的内存访问。 
在一些允许内存地址跨越对齐边界的系统上这种现象是可能的，因此每次访问需要从内存中取两次。在这种情况下，系统可能使用分离的字对齐合成flag，来表示instance_指针。 
如果一个过于激进（aggressive）编译器通过某种缓冲手段来优化flag，或是移除了第二个flag==0检测，将带来另外的相关问题。后面会介绍如何使用volatile关键字来解决这个问题。 

<h3>实现和例子代码</h3>ACE在多个库组件中使用Double Checked Locking模式。例如，为了减少代码的重复，ACE使用了一个可重用的适配器ACE Singleton来将普通的类转换成具有单例行为的类。下面的代码展示了如何用Double Checked Locking模式来实现ACE Singleton。 
<pre><code>
// A Singleton Adapter: uses the Adapter
// pattern to turn ordinary classes into
// Singletons optimized with the
// Double-Checked Locking pattern.
template <class lock="" class="" type="">
class ACE_Singleton
{
public:
static TYPE *instance (void);
protected:
static TYPE *instance_;
static LOCK lock_;
};
template <class lock="" class="" type=""> TYPE *
ACE_Singleton</class></class></code> </pre>ACE Singleton类被TYPE和LOCK来参数化。因此一个给定TYEP的类将被转换成使用LOCK类型的互斥量的具有单例行为的类。 
ACE中的Token Manager.是使用ACE Singleton的一个例子。Token Manager实现在多线程应用中对本地和远端的token（一种递归锁）死锁检测。为了减少资源的使用，Token Manager被按需创建。为了创建一个单例的Token Manager对象，只是需要实现下面的typedef： 
typedef ACE_Singleton
<pre><code>
// Acquire the mutex.
int Mutex_Token::acquire (void)
{
// If the token is already held, we must block.
if (mutex_in_use ()) {
// Use the Token_Mgr Singleton to check
// for a deadlock situation *before* blocking.
if (Token_Mgr::instance ()->testdeadlock ())
{
	errno = EDEADLK;
	return -1;
}
else
{
	// Sleep waiting for the lock...
	// Acquire lock...
}
</code>}
} </pre>
<h3>变化</h3>一种变化的Double Checked Locking模式实现可能是需要的，如果一个编译器通过某种缓冲方式优化了flag。在这种情况下，缓冲的粘着性（coherency）将变成问题，如 果拷贝flag到多个线程的寄存器中，会产生不一致现象。如果一个线程更改flag的值将不能反映在其他线程的对应拷贝中。 
另一个相关的问题是编译器移除了第二个flag==0检测，因为它对于持有高度优化特性的编译器来说是多余的。例如，下面的代码在激进的编译器下将被跳过对flag的读取，而是假定instance_还是为0，因为它没有被声明为volatile的。
<pre><code>
Singleton *Singleton::instance (void)
{
if (Singleton::instance_ == 0)
{
// Only lock if instance_ isn't 0.
Guard<mutex> guard (lock_);
// Dead code elimination may remove the next line.
// Perform the Double-Check.
if (Singleton::instance_ == 0)
// ...
</mutex></code> </pre>解决这两个问题的一个方法是生命flag为Singleton的volatile成员变量，如下： 
private:  
static volatile long Flag_; // Flag is volatile. 
使用volatile将保证编译器不会将flag缓冲到编译器，同时也不会优化掉第二次读操作。使用volatile关键字的言下之意是所有对flag的访问是通过内存，而不是通过寄存器。 

<h3>相关模式</h3>Double Checked Locking模式是First-Time-In习惯用法的一个变化。First-Time-In习惯用法经常使用在类似c这种缺少构造器的程序语言中，下面的代码展示了这个模式： 	
<pre><code>static const int STACK_SIZE = 1000;
static T *stack_;
static int top_;
void push (T *item)
{
// First-time-in flag
if (stack_ == 0) {
stack_ = malloc (STACK_SIZE * sizeof *stack);
assert (stack_ != 0);
top_ = 0;
}
stack_[top_++] = item;
// ...
}


	第一次push被调用时，stack_是0，这将导致触发malloc来初始化它自己。</code></pre>
