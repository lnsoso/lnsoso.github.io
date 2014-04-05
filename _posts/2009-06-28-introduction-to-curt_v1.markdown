---
layout: post
title: Introduction to CuRT_v1
author: gavinkwoe
date: !binary |-
  MjAwOS0wNi0yOCAxMzoyNjozMiArMDgwMA==
date_gmt: !binary |-
  MjAwOS0wNi0yOCAwNToyNjozMiArMDgwMA==
---
<h2><a name="TOC-1"></a>前言</h2>
對大多數的人而言，一個作業系統和天書可能差別不大。然而事實是如此嗎？ 世界上有一大堆小的快不像樣的 OS，jserv 的 CuRT 就是一個例子。像這樣的 OS 理論上只要是資工系統的學生都應該要寫的出來，否則大家花了那麼多的時間學程式語言和作業系統是做什麼用的? 然而如果我們做個調查，我想大多數的人都認為自己寫一個 OS 是不可能的事。這份文件就是用來解答大家的疑惑，讓大家不要再覺得這是不可能的事情了。寫一個 OS 是多麼美好的事，在有限的生命中千萬不要遺漏了它。
<h2><a name="TOC-OS-"></a>OS 是什麼?</h2>
回去看看教科書吧! 或是我們的好朋友 <a rel="nofollow" href="http://zh.wikipedia.org/wiki/%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1">wikipedia</a>。在 wikipedia 中的架構圖中你可以看到作業系統大致由
<ul>
<li>Device driver</li>
<li>kernel</li>
<li>system call</li>
<li>shell</li>
</ul>
所組成。除此之外，由於作業系統是硬體上第一個被執行的程式。通常它還需要做一些初始化的動作，讓硬體知道如何和作業系統配合。這個初始化的動作可以被視為是 CPU 的 device driver。
所以我們就從初始化開始吧!
<h2><a name="TOC-2"></a>系統初始化</h2>
一個 CPU 需要做那些初始化的動作呢?
<h3><a name="TOC-3"></a><strong>設定中斷表</strong></h3>
不管在那一個 CPU 中，這通常是第一件要做的事。不過是通常也不必執行任何程式，大多數的 CPU 會要求中斷表被放在記憶空間中固定的地方。例如  ARM，中斷表就在 位址 0-31處。所以我們只要在編譯程式時確定它們一定會在最前面就可以了。在 CuRT 中，arch/arm/mach-pxa/start.S 的最前面就在做這件事。
        .text        .text
        /* exception handler vector table */
_start:
        b reset_handler
        b und_handler
        b swi_handler
        b abt_pref_handler
        b abt_data_handler
        b not_used
        b irq_handler
        b fiq_handler
請注意，在 ARM 中，中斷向量表中儲存的是指令而不是位址。
<strong><span style="color: #ff0000;">動腦時間: 想一想，這樣有什麼限制呢?</span></strong>(ex1)
<h3><a name="TOC-CPU-"></a><strong>設定 CPU 運作參數</strong></h3>
現代的 CPU 通常都非常複雜。一般都提供了非常多不同的選項，例如 CPU clock或是 記憶體模式(segment/flat)等等。如果我們不使用預值，那就必須改變它。在 CuRT 中，在 start.S 中的
<ul>
<li>set_misc: 初始化所有 coprocessor並且清掉所有 cache 中的資料。</li>
<li>init_clock_reg: 改變 CPU clock</li>
<li>set_os_timer: 設定 system timer 的速度</li>
<li>init_gpio: 大多數 CPU 的 GPIO 都是多用途的，它們可以做為 IO pin 或是其它特別功能，如 UART,PWM,....。根據硬體的設計，我們必須適當的設定它。</li>
</ul>
通常 GPIO 是最需要注意的部份，其它的部份只要用  vendor 的  sample code 通常就差不了太多了。GPIO 的部份通常需要看一個系統的電路圖來決定。每一個 GPIO pin 都可能是下列其中一個
<ul>
<li>Input: 用來輸入一個 0 或是 1。</li>
<li>Output: 用來輸出一個 0 或是 1。</li>
<li>High impedance: 斷路</li>
<li>Alternate function: 用來做為一個特別功能，如 USB/UART/PCI/PWM 等的接腳。</li>
</ul>
一般 CPU 都會為每一個 GPIO 提供 2-3 個 control bit 來控制
<ul>
<li>input/ouput</li>
<li>gpio/alternative function</li>
<li>high impedance or not</li>
</ul>
porting OS 最重要的事通常就是把每一個 bit 都設對。看電路圖的能力在這裡是非常重要的，一定要了解什麼是 pull high/pull low/ open drain/high impedance 等基本的電路的定義。
<h3><a name="TOC-4"></a>設定記憶體管理單元</h3>
CPU 設定好後，下一個當然就是記憶體了。記憶體有很多種，不過大致可能分成二種。一種是可能直接接在 memory bus 上的，如 SDRAM/DDR/DDR2/NOR Flash等。這一類的記憶體可以直接存放程式碼，CPU 可以透過 load/store 指令直接存取它們。因此 CPU 的 instrcution/data cache 可以直接和它們溝通。
另一類是不能直接被 CPU 存取的，如 NAND flash/SPI flash/SSD 等。它們都必須透過特殊的控制器來存取，這些控制器多半也沒有內建在 CPU 之中。所以這些記憶體是不能用來儲存程式的。這裡的記憶體管理單元通常指的是前者。
在 embeded system 中的記憶體通常不是模組，也就是說我們沒有辦法像 PC 的記憶體一樣從模組內讀到記憶體的參數。因為這些參數必須被直接寫在 OS 中。在 CuRT 中 init_mem_ctrl 就是用來設定 PXA 的記憶體控制器。難這裡要注意的是動態記憶體是需要被啟動的，也就是說當我們改變了參數後，必須做一個重新啟動的動作使得記憶體控制器和記憶體達到同步的情況。至於怎麼做就要視平台而定了。請看 CPU 的 datasheet。
另一件更重要的事。請不要假設記憶的內容在參數改變後還會存在。如果你想要在 warm boot 時讀到之前的內容，一定要在設定記憶體管理單元之前做。
<h3><a name="TOC-timer"></a>設定 timer</h3>
所有的作業系統都需要 timer interrupt。作業系統會在每次 timer 中斷來時做 scheduling 的動作。一般而言，timer 的設定是很簡單的。Timer 通常會有
<ul>
<li>一個 counter register</li>
<li>一個 match register 或初始值 register。Timer 多久中斷一次就看這個 register 而定。</li>
</ul>
<h3><a name="TOC-5"></a>設定堆疊</h3>
接下來就要為 CPU 安排堆疊了。幾乎全部的 CPU 者是堆疊架構。當中斷發生時 CPU 會把目前狀態儲存在 stack pointer register(SP) 所指的位址。在ARM 中就更特別了。ARM CPU 在不同的中斷會用不同的堆疊。所以我們必需一 設定。
首先是 FIQ 模式。FIQ 是代表 fast interrupt。在這個模式下 ARM 會將 r8-r15 切換到 banked register。也就是說我們不必儲存這些暫器的內容。如此一來，我們只要不要重 r0-r7 的內容，那就不必做context saving 的動作了。詳細的說明可以看 <a href="http://stenlyho.blogspot.com/2008/08/arm.html">http://stenlyho.blogspot.com/2008/08/arm.html</a>。
set_stack_pointer:
        /* FIQ mode */
        mrs r0, cpsr            /* move CPSR to r0 */
        bic r0, r0, #0x1f       /* clear all mode bits */
        orr r0, r0, #0xd1       /* set FIQ mode bits */
        msr CPSR_c, r0          /* move back to CPSR */
        ldr sp, =(fiq_stack + FIQ_STACK_SIZE - 4)       /* initialize the stack ptr */
CPSR 的定義可以在 <a rel="nofollow" href="http://hi.baidu.com/flfxt/blog/item/697320248b9a4d34c9955961.html">http://hi.baidu.com/flfxt/blog/item/697320248b9a4d34c9955961.html</a> 找到。CPSR 的 0-4 bits 是 代表了 CPU 目前的模式。FIQ 模式的值為 10001。上面的程式把 CPSR 改成 d1
        1   1   0   1   0   0   0   1
        I    F   T   Modes
然後把 stack 的尾端設到 SP 之中。下面的程式重復同樣的程序將不同的 stack 設到不同的模式之中。請注意一點，雖然都是 SP 但在不同樣式下它們其實是不同的暫存器。請參考上面的連結，不同的模式有些暫存器會不一樣會使用不同的暫存器。
        /* IRQ mode */
        mrs r0, cpsr            /* move CPSR to r0 */
        bic r0, r0, #0x1f       /* clear all mode bits */
        orr r0, r0, #0xd2       /* set IRQ mode bits */
        msr CPSR_c, r0          /* move back to CPSR */
        ldr sp, =(irq_stack + IRQ_STACK_SIZE - 4)       /* initialize the stack ptr */
        /* Abort mode */
        mrs r0, cpsr            /* move CPSR to r0 */
        bic r0, r0, #0x1f       /* clear all mode bits */
        orr r0, r0, #0xd7       /* set Abort mode bits */
        msr CPSR_c, r0          /* move back to CPSR */
        ldr sp, =(abt_stack + ABT_STACK_SIZE - 4)       /* initialize the stack ptr */
        /* Undef mode */
        mrs r0, cpsr            /* move CPSR to r0 */
        bic r0, r0, #0x1f       /* clear all mode bits */
        orr r0, r0, #0xdb       /* set Undef mode bits */
        msr CPSR_c, r0          /* move back to CPSR */
        ldr sp, =(und_stack + UND_STACK_SIZE - 4)       /* initialize the stack ptr */
        /* System mode */
        mrs r0, cpsr            /* move CPSR to r0 */
        bic r0, r0, #0x1f       /* clear all mode bits */
        orr r0, r0, #0xdf       /* set System mode bits */
        msr CPSR_c, r0          /* move back to CPSR */
        ldr sp, =(sys_stack + SYS_STACK_SIZE - 4)       /* initialize the stack ptr */
<h2><a name="TOC-OS"></a>準備進入 OS</h2>
最後，我們準備進入 OS 了。到目前為止，除了 BootRom外，我們實際上根本沒有使用到 CPU 以外的資源。CuRT 被設計成會將程式載入到動態記憶體中執行。這一步其實是可以不要做的，這我們後面再來討論。先看一下 CuRT 是怎樣將 OS 載入到記憶體執行。
首先將程式的啟始位置載入 r0 之中
relocate:
        adr r0, _start
CuRT 的做法很簡單，直接載入 1MB。這麼小的 OS 應該不會超過 1MB 吧! 這種做法實在太偷懶了一點.........
        // relocate the second stage loader
        add r2, r0, #(1024 * 1024)
a0000000 是動態記憶體的位置。友情的提醒大家一點，這個位址不見得在所有 ARM 都是對的。Porting 時要注意。
        ldr r1, =0xa0000000
        /* r0 = source address
         * r1 = target address
         * r2 = source end address
         */
copy_loop:
        ldmia   r0!, {r3-r10}
        stmia   r1!, {r3-r10}
        cmp     r0, r2
        ble     copy_loop
看了上面的程式，應該很佩服ARM 組合語言的強大吧! ldmia 一般會載入 r3-r10 一共 32 個位元，然後將 r0 加 32。 stmia 會將 r3-r10 存入記憶體，並將 r1 加 32。這在其它的 CPU 可是要用多好幾倍的指令才能完成的。
好了，目前整個 CuRT 己經在 0xa0000000 上了。 最後來個
       bl main
這樣就進入 C 之中了。
有沒有人覺得怪怪的，bl main 到底是跳到記憶體還是 BootRom 中了呢? 想到這一點，你有些高段了。如果你連答案都知道，那你應該不要繼續浪費時間看這份文件了。至於答案，讓我賣個關子吧!
<h2><a name="TOC-C-"></a>進入  C  的世界</h2>
和一般程式一樣 CuRT 也用 main 做為 C 的進入點。不過這個 main 可不是一般應用程式。它的工作是為系統做初始化的工作。
不過 CuRT 的功能實在少到一個程度，它是由下面三個函式組成。
        SerialInit();
        init_interrupt_control();
        init_curt();
SerialInit 是用來初始化 serial port。在大多數的 OS 中，serial port 都是最早被建立的子系統。有了他之後，我們就等於多了一對眼睛。可以比較容易的了解系統的狀況。可以這樣說，在 serial port 初始化之前，debug 是一門藝術。在它之後，則變成一門科學。
init_interrupt_control 的功能很簡單，就是把中斷打開。在這一點之後，軟硬體的中斷就開始生效了。這包括 timer 在內。
最後是 init_curt，這是三者之中最重要的。它的主要工作就是初始化 scheduler。CuRT 實作了一個有優先權的 round-robin scheduler。這和 Linux 中的 SCHED_RR 有點像。整個  scheduler 的狀態機由下面幾個串列組成
        for (i = 0; i < MAX_PRIO; i++) {
                prio_exist_flag[i] = false;
                init_list(&ready_list[i]);
        }
        init_list(&delayed_list);
        init_list(&blocked_list);
        init_list(&termination_wait_list);
<ul>
<li>ready_list[0..31]: 在優先權 0..31 的並在作用中的 theads。</li>
<li>delayed_list: 這是 timer list，每一個 trhead 可以讓自己進入睡覺狀態一段時間，此時thread 會被移入 delayed_list。在每一次 timer 時CuRT 都會檢查是否要喚醒這個 thread。</li>
<li>blocked_list:目前不在作用狀態的 thread，通常在等待 IO 或是 semaphore。</li>
<li>termination_wait_list: 包括己經宣告結束的 thread，等待其它行程回收。</li>
</ul>
CuRT 在每次 timer 時會根據選出優先權最高的 thread 執行。後面我們會再回來看這個scheduler。
在這個函數的最後，idle_thread 被初始化。這個 thread 的目的在消秏系統的時間，它也可以用來執行一個不重要的工作。目前它負責回收在 termination_wait_list 中己結束的行程。
        thread_create(&idle_thread,
                        &idle_thread_stk[THREAD_STACK_SIZE-1],
                        idle_thread_func,
                        "idle-thread",
                        IDLE_THREAD_PRIO,
                        NULL);
在 RTOS 中，並沒有 kernel mode /user mode 的存在。到底是 kernel thread 或是應用程式 thread 只是雖呼叫 thread_create 的差別而己。
<h2><a name="TOC-Thread-Thread-Thread"></a>Thread Thread Thread</h2>
所有東西都是 thread。在 RTOS 中一定要認和這一點。不管是 kernel, driver, application 都是 thread。system call 實際上也必須是一個 thread+semaphore 的組合。在目前的 CuRT 中，有下列的 thread
<ul>
<li>shell thread</li>
<li>ps thread</li>
<li>stat thread</li>
<li>help thread</li>
<li>hello thread</li>
<li>hello2 thread</li>
</ul>
當我們輸入一個命令，shell 會喚醒適當的 thread 來執行所需的功能。以 ps 為例做法是
                else if (!strcmp(buf, "ps")) {
                        thread_resume(info_tid);
                        thread_delay(1);
                }
 
thread_resume 會喚醒處於沉睡狀態的 thread。thread_delay(1) 則會將目前的 thread 放入沈睡狀態 1 個 tick。實際上就是把 thread 放入 delayed_list 之中。這個做法有些偷懶，應該要用 semaphore 來做才正確，這樣做如果 ps thread 執行太慢就可能出問題。
<strong style="color: #ff0000;">動腦時間: 想想看會出什麼問題?</strong>(ex1)
如果你要為 CuRT 加入一個應用程式，那就由 heelo 或 hello2 開始吧。
<strong style="color: #ff0000;">動腦時間: 寫個打地鼠吧!
</strong>
<h2><a name="TOC-6"></a>中斷處理程式</h2>
在 OS 中，中斷處理程式伴演了火車頭的角色，幾乎所有的動作都是由一個中斷開始。由上面的討論，我們了解CuRT 的中斷由 irq_handler 開始，而這個函數會直接呼叫 irq_service_routine。
<strong style="color: #ff0000;">動腦時間: 想想看為什麼不直接呼叫 irq_service_routine 就好了呢? 提示，RISC 的指令長度是固定的。</strong>(ex1)
解釋一下 interrupt service routine 如何在不同模式間切換。
<h2><a name="TOC-Scheduler"></a>Scheduler</h2>
終於到我的專長了，我們來好好看一下 CuRT 的 scheduler。一般而言，scheduler 會在下面幾種狀況被執行。
<ul>
<li>thread 自己釋收 CPU</li>
<li>thread 被分配的時間用完了</li>
<li>thread 需要等待某一個資源</li>
</ul>
在 CuRT 中，第一個會呼叫 scheduler(SCHED_TIME_EXPIRE)。第二個會呼叫 schedule(SCHED_THREAD_REQUEST)。
本節所討論的程式都在 kernel/kernel.c 之中。
先看看第一種情況，這種情況基本上是由 exit_interrupt 呼叫進來的
void void exit_interrupt()
{
        if (interrupt_nesting > 0)
                interrupt_nesting--;
        if (current_thread->time_quantum <= 0) {
                schedule(SCHED_TIME_EXPIRE);
        }
}
在每一次 timer 中斷進來時，advanced_time_tick 會被呼叫一次。它會做二個事，第一件是檢查 delayed_list 中的 thread 是否需要喚醒。
        if (!is_empty_list(&delayed_list)) {
                for (pnode = begin_list(&delayed_list);
                     pnode != end_list(&delayed_list);
                     pnode = next_list(pnode) ) {
                        pthread = entry_list(pnode, thread_struct, node);
                        pthread->delayed_time--;
                        /* ready to change the status */
                        if (readyed_thread != NULL) {
                                delete_list(&readyed_thread->node);
                                readyed_thread->state = READY;
                                readyed_thread->time_quantum = TIME_QUANTUM;
                                insert_back_list(
                                        &ready_list[readyed_thread->prio],
                                        &readyed_thread->node);
                                prio_exist_flag[readyed_thread->prio] = true;
                                readyed_thread = NULL;
                        }
                        if (pthread->delayed_time <= 0) {
                                readyed_thread = pthread;
                        }
                }
                if (readyed_thread != NULL) {
                        delete_list(&readyed_thread->node);
                        readyed_thread->state = READY;
                        readyed_thread->time_quantum = TIME_QUANTUM;
                        insert_back_list(
                                &ready_list[readyed_thread->prio],
                                &readyed_thread->node);
                        prio_exist_flag[readyed_thread->prio] = true;
                }
        }
第二件事就是把目前 thread 被分配的時間減一。
        current_thread->time_quantum--;
當 time_quantim 變成 0 時 exit_interrupt 就會呼叫  scheduler。首先我們先取得目前優先權最高的 thread
        top_prio = get_top_prio();
get_top_prio()  這個函數會傳回除目目前 thread 外最高的優先權。假如沒有人至少和它一樣高，那就繼續執行目前的 thread。
 
               if (current_thread->prio < top_prio) {
                        current_thread->time_quantum = TIME_QUANTUM;
                        restore_cpu_sr(cpu_sr);
                        return;
                }
 
否則就取出目前最高的 thread
                pnode = delete_front_list(&ready_list[top_prio]);
                if (is_empty_list(&ready_list[top_prio]))
                        prio_exist_flag[top_prio] = false;
並將它設成執行狀態。
                next_thread = entry_list(pnode, thread_struct, node);
                next_thread->state = RUNNING;
                next_thread->time_quantum = TIME_QUANTUM;
並將原先的行程放回 ready_list 中。
                current_thread->state = READY;
                insert_back_list(&ready_list[current_thread->prio],
                                 &current_thread->node);
                prio_exist_flag[current_thread->prio] = true;
 
請注意 insert_back_list 這個動作。這就是為什麼這是一個 priority-based round-robin 的原因。每次 thread 都會被放回同一優先權串列最後面，所以在同一優先級中它就變成是最後一名了。
<strong style="color: #ff0000;">動腦時間: 怎麼樣把  scheduler 改成 FIFO 而不是 round-robin 呢？只要改一行，試試看吧。</strong>(ex1)
<h2><a name="TOC-7"></a>驅動程式</h2>
到這裡，整個 CuRT 己經幾乎看完了。
那有人可能問，driver 呢?
OS 是不一定要有 driver 的，CuRT 嚴格檢說是沒有一般定義的 driver 架構的。和其它的小型 OS 一樣，driver 只不過是一個應用程式，或是一個可以由應用程式呼叫的程式庫而己。用另一個角度來看，每一個應用程式也都可以拌演 driver 的角色。
目前為止，CuRT 唯一的 driver  是 serial driver。它被定義在 device/serial.c 之中。它太簡單了，就自己看吧。基本上它就是一個簡單的 UART 讀寫程式庫。
驅動程式另一個可能性是用一個 thread 完成。在這種情況下一個驅動程式通常是由一個 thread 和一個程式庫共同完成。thread 負責由硬體讀回或送出資料，程式庫則負責和應用程式溝通。舉個例說以 IDE 為例，我們可以產生一個 thread 負責將資料在硬體和 IDEInBlockList 和 IDEOutBlockList 中搬移。而應用程式可以始用程式庫內的函數在它自己的 context 中操作 IDEInBlockList 和 IDEOutBlockList 中的項目。當我們要謮一個 block 時
<ul>
<li>AP
<ul>
<li>read 函數會在 將IDEInBlockListLock 鎖住</li>
<li>在IDEInBlockList中加入一個新的項目</li>
<li>IDEInBlockList 解鎖</li>
<li>喚醒IDE thread </li>
</ul>
</li>
<li>IDE thread
<ul>
<li>將IDEInBlockListLock 鎖住</li>
<li>由 IDEInBlockList 中謮出一個項目</li>
<li>將它傳換成適當的 IDE 命令送出</li>
<li>IDEInBlockList 解鎖</li>
<li>等待 interrupt service routine 把它喚醒</li>
</ul>
</li>
<li>Interrupt service routine
<ul>
<li>如果是 IDE interrupt，喚醒 IDE thread</li>
</ul>
</li>
<li>IDE thread
<ul>
<li>將資料放入 IDEInBlockList 的適當項目中</li>
<li>喚醒當初發出這個項目的 thread</li>
</ul>
</li>
<li>AP thread
<ul>
<li>得到一個由 IDE 中而來的資料</li>
</ul>
</li>
</ul>
目前 CuRT 的 interrupt service routine 很簡單只有檢查 timer interrupt，
void interrupt_handler()
{
        if (INT_REG(INT_ICIP) & BIT26) {
                TMR_REG(TMR_OSCR) = 0x00;
                advance_time_tick();
                TMR_REG(TMR_OSSR) = BIT0;
        }<span style="color: #ff0000;"> else if (INT_REG(INTI_CIP) &BITIDE) {</span><br style="color: #ff0000;" /><span style="color: #ff0000;">                 thread_resume(IDE_thread);</span><br style="color: #ff0000;" /><span style="color: #ff0000;">        }</span>
}
為了 IDE 我們必須加入一個新的檢查。在一般的作業系統我們會加入一個叫 register_irq 的函數讓驅動程式註冊自己的 interrupt service routine。CuRT 太簡單了，所以直接寫可能還更清楚一些。
<strong style="color: #ff0000;">動腦時間: 為 CuRT 加一個 IDE driver 如何? </strong>
<h2><a name="TOC-IPC"></a>IPC</h2>
[TBC....]
<h2><a name="TOC-CuRT-"></a>CuRT 還缺什麼</h2>
CuRT 雖然簡單但其實己經很有用，只要再加入下面的功能，基本上就是一個完整的 RTOS 了。至少，那來做 bootloader 是不錯的。
<ul>
<li>memory allocator</li>
<li>I2C/SPI driver</li>
<li>Flash driver</li>
<li>IDE driver</li>
<li>simple C library</li>
</ul>
<strong style="color: #ff0000;">動腦時間: 為 CuRT 加入這些功能如何?
</strong>
