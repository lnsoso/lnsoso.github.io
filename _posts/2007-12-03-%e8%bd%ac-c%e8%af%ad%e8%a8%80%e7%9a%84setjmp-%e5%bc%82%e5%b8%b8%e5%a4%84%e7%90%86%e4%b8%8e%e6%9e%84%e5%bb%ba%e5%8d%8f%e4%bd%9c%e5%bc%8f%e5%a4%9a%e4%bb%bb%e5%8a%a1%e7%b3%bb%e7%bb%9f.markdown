---
layout: post
title: ! '[转] C语言的setjmp: 异常处理与构建协作式多任务系统'
author: gavinkwoe
date: !binary |-
  MjAwNy0xMi0wMyAxNTo1Nzo0OCArMDgwMA==
date_gmt: !binary |-
  MjAwNy0xMi0wMiAyMzo1Nzo0OCArMDgwMA==
---
转至 <a href="http://www.upsdn.net/html/2004-11/47.html" title="http://www.upsdn.net/html/2004-11/47.html">http://www.upsdn.net/html/2004-11/47.html</a>
在C标准库中有一对非常有趣的函数setjmp()函数与longjmp()函数,用来实现代替goto实现一些非常重要的功能，如异常处理。C语言中，标准库函数setjmp和longjmp形成了结构化异常工具的基础。简单的说即setjmp实例化异常处理程序，而longjmp产生异常。
先介绍setjmp
int setjmp(jmp_buf envbuf)
宏函数setjmp()在缓冲区envbuf中保存系统堆栈里的内容，供longjmp()以后使用,setjmp()必须使用头文件setjmp.h。
调用setjmp()宏时，返回值为0，然而longjmp()把一个变原传递给setjmp(),该值（恒不为0）就是调用longjmp()后出现的setjmp()的值。
setjmp函数用于保存程序的运行时的堆栈环境，接下来的其它地方，你可以通过调用longjmp函数来恢复先前被保存的程序堆栈环境。当setjmp和longjmp组合一起使用时，它们能提供一种在程序中实现“非本地局部跳转”（"non-local goto"）的机制。并且这种机制常常被用于来实现，把程序的控制流传递到错误处理模块之中；或者程序中不采用正常的返回（return）语句，或函数的正常调用等方法，而使程序能被恢复到先前的一个调用例程（也即函数）中。
对setjmp函数的调用时，会保存程序当前的堆栈环境到env参数中；接下来调用longjmp时，会根据这个曾经保存的变量来恢复先前的环境， 并且当前的程序控制流，会因此而返回到先前调用setjmp时的程序执行点。此时，在接下来的控制流的例程中，所能访问的所有的变量（除寄存器类型的变量 以外），包含了longjmp函数调用时，所拥有的变量。
void longjmp(jmp_buf envbuf,int status);
     函数longjmp()使程序在最近一次调用setjmp()出重新执行。setjmp()和longjmp()提供了一种在函数间调转的手段，必须使用头部文件setjmp.h。
     函数longjmp()通过把堆栈复位成envbuf中描述的状态进行操作，envbuf的设置是由预先调用setjmp()生成的。这样使程序的执行在 setjmp()调用后的下一个语句从新开始，使计算机认为从未离开调用setjmp()的函数。从效果上看，longjmp()函数似乎“绕”过了时间 和空间（内存）回到程序的原点，不必执行正常的函数返回过程。
     缓冲区envbuf具有<setjmp.h>中定义的buf_jmp类型，它必须调用longjmp()前通过调用setjmp()来设置好。
    值status变成setjmp()的返回值，由此确定长调转的来处。不允许的唯一值是0，0是程序直接调用函数setjmp()时由该函数返回的，不是间接通过执行函数longjmp()返回的。
     longjmp()函数最常用于在一个错误发生时，从一组深层嵌套的实用程序中返回。
longjmp函数用于恢复先前程序中调用的setjmp函数时所保存的堆栈环境。setjmp和longjmp组合一起使用时，它们能提供一种在程序中实现“非本地局部跳转”（"non-local goto"）的机制。并且这种机制常常被用于来实现，把程序的控制流传递到错误处理模块，或者不采用正常的返回（return）语句，或函数的正常调用等方法，使程序能被恢复到先前的一个调用例程（也即函数）中。
　 　对setjmp函数的调用时，会保存程序当前的堆栈环境到env参数中；接下来调用longjmp时，会根据这个曾经保存的变量来恢复先前的环境，并且 因此当前的程序控制流，会返回到先前调用setjmp时的执行点。此时，value参数值会被setjmp函数所返回，程序继续得以执行。并且，在接下来 的控制流的例程中，它所能够访问到的所有的变量（除寄存器类型的变量以外），包含了longjmp函数调用时，所拥有的变量；而寄存器类型的变量将不可预 料。setjmp函数返回的值必须是非零值，如果longjmp传送的value参数值为0，那么实际上被setjmp返回的值是1。
　　在调用setjmp的函数返回之前，调用longjmp，否则结果不可预料。
在使用longjmp时，请遵守以下规则或限制：
　　· 不要假象寄存器类型的变量将总会保持不变。在调用longjmp之后，通过setjmp所返回的控制流中，例程中寄存器类型的变量将不会被恢复。
　　· 不要使用longjmp函数，来实现把控制流，从一个中断处理例程中传出，除非被捕获的异常是一个浮点数异常。在后一种情况下，如果程序通过调用 _fpreset函数，来首先初始化浮点数包后，它是可以通过longjmp来实现从中断处理例程中返回。
如何实现异常处理
首先设置一个跳转点（setjmp() 函数可以实现这一功能），然后在其后的代码中任意地方调用 longjmp() 跳转回这个跳转点上，以此来实现当发生异常时，转到处理异常的程序上，在其后的介绍中将介绍如何实现。 setjmp() 为跳转返回保存现场并为异常提供处理程序，longjmp() 则进行跳转（抛出异常），setjmp() 与 longjmp() 可以在函数间进行跳转，这就像一个全局的 goto 语句，可以跨函数跳转。
<strong>jmp_buf 异常结构</strong>
        使用 setjmp() 及 longjmp() 函数前，需要先认识一下 jmp_buf 异常结构。jmp_buf 将使用在 setjmp() 函数中，用于保存当前程序现场（保存当前需要用到的寄存器的值），jmp_buf 结构在 setjmp.h 文件内声明：
        typedef struct
        {
                unsigned j_sp;  // 堆栈指针寄存器
                unsigned j_ss;  // 堆栈段
                unsigned j_flag;  // 标志寄存器
                unsigned j_cs;  // 代码段
                unsigned j_ip;  // 指令指针寄存器
                unsigned j_bp; // 基址指针
                unsigned j_di;  // 目的指针
                unsigned j_es; // 附加段
                unsigned j_si;  // 源变址
                unsigned j_ds; // 数据段
        } jmp_buf;
        jmp_buf 结构存放了程序当前寄存器的值，以确保使用 longjmp() 后可以跳回到该执行点上继续执行。
setjmp() 与 longjmp() 函数都使用了 jmp_buf 结构作为形参，它们的调用关系是这样的：
        首先调用 setjmp() 函数来初始化 jmp_buf 结构变量 jmpb，将当前CPU中的大部分影响到程序执行的积存器存入 jmpb，为 longjmp() 函数提供跳转，setjmp() 函数是一个有趣的函数，它能返回两次，它应该是所有库函数中唯一一个能返回两次的函数，第一次是初始化时，返回零，第二次遇到 longjmp() 函数调用后，longjmp() 函数使 setjmp() 函数发生第二次返回，返回值由 longjmp() 的第二个参数给出（整型，这时不应该再返回零）。
        在使用 setjmp() 初始化 jmpb 后，可以其后的程序中任意地方使用 longjmp() 函数跳转会 setjmp() 函数的位置，longjmp() 的第一个参数便是 setjmp() 初始化的 jmpb，若想跳转回刚才设置的 setjmp() 处，则 longjmp() 函数的第一个参数是 setjmp() 所初始化的 jmpb 这个异常，这也说明一件事，即 jmpb 这个异常，一般需要定义为全局变量，否则，若是局部变量，当跨函数调用时就几乎无法使用（除非每次遇到函数调用都将 jmpb 以参数传递，然而明显地，是不值得这样做的）；longjmp() 函数的第二个参数是传给 setjmp() 的第二次返回值，这在介绍 setjmp() 函数时已经介绍过。
        下面是 setjmp() 函数与 longjmp() 函数的一个示意图：
通过示意图可以看出 setjmp() 函数与 longjmp() 函数的关系，如何跳转。
呵呵！现在是否对程序的执行流程一目了然，其中最关键的就是setjjmp和longjmp函数的调用处理。我们分别来分析之。
　　当程序运行到第②步时，调用setjmp函数，这个函数会保存程序当前运行的一些状态信息，主要是一些系统寄存器的值，如ss，cs，eip， eax，ebx，ecx，edx，eflags等寄存器，其中尤其重要的是eip的值，因为它相当于保存了一个程序运行的执行点。这些信息被保存到 mark变量中，这是一个C标准库中所定义的特殊结构体类型的变量。
　　调用setjmp函数保存程序状态之后，该函数返回0值，于是接下来程序执行到第③步和第④步中。在第④步中语句执行时，如果变量n2为0值，于是便 引发了一个浮点数计算异常，，导致控制流转入fphandler函数中，也即进入到第⑤步。
　　然后运行到第⑥步，调用longjmp函数，这个函数内部会从先前的setjmp所保存的程序状态，也即mark变量中，来恢复到以前的系统寄存器的 值。于是便进入到了第⑦步，注意，这非常有点意思，实际上，通过longjmp函数的调用后，程序控制流（尤其是eip的值）再次戏剧性地进入到了 setjmp函数的处理内部中，但是这一次setjmp返回的值是longjmp函数调用时，所传入的第2个参数，也即-1，因此程序接下来进入到了第⑧ 步的执行之中。
因为 jmp_buf 全局只能存放一个 jmpb 结构，使得只有最后一组宏可以响应异常；    这是无法嵌套异常的原因，要实现多重嵌套可以建立一个全局堆栈来维护一组 jmpb 结构
实现机理
setjmp()保存其调用返回点的ebx, esi, edi, ebp, esp, eip,对于sigsetjmp(), 还保存当前的信号屏蔽字, longjmp恢复这些寄存器及信号屏蔽字,同时传递一个返回值。
ongjmp只是使CPU的状态和setjmp时的状态一致，相同的CPU初始状态执行相同的代码并不意味着产生相同的结果,setjmp, longjmp并不关心内存变量的变化，而只关心CPU的状态,
内存变量的确定性应该根据上下文来灵活处理.
如果将函数之间的调用关系看成一种树型结构, 将可执行文件的入口函数看成它的最内层节点(根节点), 那么setjmp提供了标记节点的方法, longjmp提供了从外层节点直接返回到更内层节点的方法,
在同一层节点之间或者从内层节点到外层节点的longjmp是没有意义的, 因为目标节点在此时根本不存在.
<strong>摘要：</strong>讨论一个利用标准C语言setjmp库函烽实现查询式协作多任务系统，给出完整的内核和样例程序并对源代码进行说明。该系统具有简单易用的特点，只需要编写存取堆栈指针的宏就可方便地移植到新的平台上。文章详述了系统的优缺点，讨论一些性能扩展的方法。该内核适用
于中小规模的嵌入式软件。
<strong>关键词：</strong>协作式多任务 C语言 setjmp
<strong>引言</strong>
本文介绍的是利用标准C语言setjmp库函数实现的具备此特点的协作式多任务系统。从本 质上讲，实时多任务操作系统应该具备按照优先级抢占调度的内核。然而，在实际应用中，抢中式的多任务某种程序上带来了用户程序设计时数据保护的困难，并 且，具备抢占功能的多任务内核设计时困难也比较多，这会增加操作系统自身的代码，也使它在小资源单片机系统中应用较少；而协作多任务系统的调度只在用户指 定的时机发生，这会大大简化内核和用户系统的设计，尤其本文实现的系统通过条件查询来放弃CPU，既符合传统单片机程序设计的思维，又带来了多任务、模块 化、可重入的编程便利。
Setjmp是标准C语言库函数的组成部分，它可以实现程序执行中的远程转操作。具体来 说，它可以在一个函数中使用setjmp来初始化一个全局标号，然后只要该函数未曾返回，那么在其它任何地方都可以通过longjmp调用来跳转到 setjmp的下一条语句执行。实际上，setjmp函数将发生调用处的局部环境保存在一个jmp_buf的结构当中，只要主调函数中对应的内存未曾释放 （函数返回时局部内存就失效了），那么在调用longjmp的时候就可以根据已保存的jmp_buf参数恢复到setjmp的地方执行。我们的系统中就是 分析了setjmp标准库函数的特点，以简单的方式实现了协作式多任务。
<strong>1 演示程序</strong>
为了便于理解，首先给出多任务演示程序的源代码。这个程序演示了协作式多任务切换、任务的 动态生成、多任务共用代码等功能，一共使用了init_coos初始化根任务（也就是C语言main函数）、creat_task创建新任务和 WAITFOR查询条件这3个基本的系统调用。由于面向嵌入式系统，因而程序不会中止并且运行中也没有进行任何输出，需要借助适合的调试工具来理解多任务 系统的运行。
example.c文件清单：
#include<stdlib.h>
#include“co-os.h”
void tskfunc1(int argc,void *argv);
void tskfunc2(int argc,void *argv);
void subfunc(void);
volatile int cnt,test;
int main(void){
int i;
init_coos(400);
creat_tsk(tskfunc1,12,NULL,400);
creat_tsk(tskfunc2,0,NULL,400);
i=0；
while(1){
WAITFOR(cnt= =8);
while(i++<cnt)test=i;
cnt++;
}
}
void tskfunc1(int argc,void *argv){
int i;
static int creat=0;
if(!creat){
creat_tsk(tskfunc1,9,NULL,400);
creat=1;
}
i=0；
while(1){
WAITFOR(cnt>argc);
test=0x55;
/*使用函数调用在子程序中测试WAITFOR*/
subfunc()；
while(i++<cnt)test=i^0xAA;
}
}
void tskfunc2(int argc,void *argv){
while(1){
WAITFOR(++cnt>15);
cnt=0;
}
}
void subfunc(void){
int i;
WAITFOR(cnt<5);
for(i=0;i<++)test=0x10*i；
}
<strong>2 内核构成</strong>
内核包括一个供外部用户程序包含的头文件（co-os.h）和具体实现的源文件（co-os.c），它们提供了演示程序中用到的3个系统调用。
内核的实现代码假定了CPU堆栈是向下增长的，并且通过宏来直接操作堆栈指针。以下代码在 Microsoft VC6 for x86、Borland C++ Builder 5.5、SDS CrossCode7.0 for 68K和GCC3.2 for AVR四种平台中测试过，只需在co-os.h头文件中定义相应的平台类型即可顺利编译。
（1）co-os.h文件清单
#include<setjmp.h>
/*选择X86_VC6，X86_BC5，AVR_GCC或M68H_SDS.*/
#define X86_VC6
#define MAX_TSK 10
typedef struct {
void (*entry)(int argc,void *argv);
jmp_buf env;
int argc;
void *argv;
}TVB;
extern TCB tcb[MAX_TSK];
extern int task_num,tskid;
void init_coos(int mainstk);
int creat_tsk(void(*entry)(int argc,void *argv),int argc,void *argv,int stksize);
#define WAITFOR(condition)do{
setjmp(tcb[tskid].env);
if(!(condition)){
tskid++;
if(tskid>=task_num)tskid=0;
longijmp(tcb[tskid].env,1);
}
}while(0)
(2)co-os.c文件清单
#include "co-os.h"
#if defined(X86_VC6)||defined(X86_BC5)
#define SAVE_SP(p) _asm mov p,esp
#define RESTORE_SP(p) _asm mov esp,p
#elif defined(AVR_GCC)
#include<io.h>
#define SAVE_SP(p) p=(int*)SP
#define RESTORE_SP(p) SP=(int)p
#elif defined(M68K_SDS)
#define SAVE_SP(p) asm("MOVE.L A7,{"#p"}")
#define RESTORE_SP(p) asm("MOVE.L {"#p"},A7")
#endif
TCB tcb[MAX_TSK];
Int task_num=1;
Int tskid;
Static int stktop,oldsp;
Void init_coos(int mainstk){
SAVE_SP(stktop);
stktop=stktop+sizeof(void(*)(void))/sizeof(int)
-(mainstk+sizeof(int)-1)/sizeof(int);
}
int creat_tsk(void(*entry)(int argc,void *argv),
int argc,void *argv,int stksize){
if(task_num>=MAX_TSK)terurn-1;
SAVE_SP(oldsp);
RESTORE_SP(stktop);
If(!setjmp(tcb[task_num].env)){
RESTORE_SP(oldsp);
tcb[task_num].entry=entry;
tcb[task_num].argc=argc;
tcb[task_num].argv=argv;
task_num++;
stktop-=(stksize+sizeof(int)-1)/sizeof(int);
}
else tcb[tskid].entry(tcb[tskid].argc,tcb[tskid].argv);
return 0;
}
<strong>3 代码说明</strong>
任务代码通过执行setjmp设置本任务下次查询时的返回点，然后在等待条件放弃掉CPU 跳转到下一任务的返回点处执行。如此周而复始，让各任务都获得轮转运行的机会，也要求各任务都需要主动通过等待条件的方式放弃掉CPU。系统中除了中断服 务程序之外，所有任务都是平等的，都应该遵循同样的规则和其它任务一起协作运行。基本系统中没有设计杀死任务的调用，这要求各任务都应当设计成某种形式的 无限循环。
任务中等待的条件可以是任务复杂的表达式或都函数调用，也可以是中断服务程序设置的全局变 量（注意加volatile定义）。一般在任务执行时会让下次等待的条件不再满足，避免某个任务一直霸占CPU将系统饿死。在嵌入式软件中还经常会遇到任 务定时启动和超时等待在I/O操作上，在我们的系统中可以维护一个时间计数器，只需在适当的地方记录时刻，然后在任务查询条件中判断当前计数器和记录时刻 之间的差值就可以了。
内核实现的代码则相当简法。由于主要的保护和恢复任务现场的工作都由C语言标准库 setjmp实现了，我们就只需要操纵一下堆栈指针防止不同的任务使用了重叠的局部环境，这个工作在初始化和创建任务的时候通过预定义的两个宏来实现。在 init_coos函数中，记录了主任务（main函数）保留mainstk字节堆栈后的新栈顶位置（stktop），然后在每次creat_task时 都根据要求为每个任务保留stksize字节的堆栈并重新计算下一个stktop。在creat_task函数中利用了setjmp函数的返回值（直接返 回时为0，longjmp跳转返回时非0），使得一方面creat_task能正常回到调用者，又让下次轮转到新任务时能够找到创建时的入口。
co-os.h中定义的WAITFOR使用了一个do{…while(0)实现多语句宏的 C语言小技巧，这样能保证在任何情况下WAITFOR都可以如单条语句一样在源程序中使用，需要担心多了或者少了大括弧破坏if/else匹配之类的问 题，并且，所有的编译器都会优化掉这个假循环。
为了尽量使程序简单并说明问题，以上代码中没有考虑中断相关的数据保护问题。实际运行的系 统中，如果首先写堆栈指针不能一步完成（如AVR这样的8位机），那么，在写操作正在进行的时候绝对不能允许中断；另外，在任务中查询的条件如果和中断有 关，那么也必须考虑数据的完整性。
<strong>4 性能分析</strong>
所有的协作式多任务系统中任务切换时间都和用户代码（是否长期占用CPU）、就绪时刻有 关，比标准系统略差的是，我们的简单系统中不支持任务优先级，并且完成一轮查询调度的时间还和任务数目有关。这也是为了达到简单和可移植性目标而不得已作 出的牺牲。在各任务间切换查询条件通过C语言标准库函数longjmp实现，一般来讲longjmp函数要从内存中恢复大部分CPU寄存器，执行它也需要 若干条指令的时间。
为了面向嵌入式系统应用，任务控制块（TCB）采用静态数组来实现，这样要求预先确定系统 的最大任务数（co-os.h中的MAX_TSK）。如果需要，也可以通过环波链表来动态管理任务控制块（TCB），这时可以简单实现任务的动态创建和删 除，并且通过指针来访问TCB也要比通过下标（tskid）略快一点。
在某些情况下，如果在中断返回后需要执行某关键任务，可以考虑通过设置“高级中断”的方法 来实现。具体地讲，就是在中断返回前改变返回地址到某函数入口（“高级中断服务程序”），同时保留原返回地址到堆栈中，这样在“高级中断服务程序”完成后 执行return就又回到了正常的多任务查询流程。使用“高级中断”时要注意现场保护的衔接，并且这种技巧显然和CPU和体系结构有关。
<strong>5 结论</strong>
setjmp是标准C语言中用于远程跳转的库函数，利用它可方便实现一个简单易移植的协作式多任务系统。该系统功能完备、编程简单、易于学习，适合一些中小规模的嵌入式软件使用；并且，以此为基础，还可以用一些与平台相关的编程技巧提高其实时性和灵活性。
部分资料来源于希赛,单片机与嵌入式系统应用
