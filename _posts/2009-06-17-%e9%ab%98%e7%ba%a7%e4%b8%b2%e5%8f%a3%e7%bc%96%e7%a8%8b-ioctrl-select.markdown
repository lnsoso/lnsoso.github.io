---
layout: post
title: 高级串口编程 - ioctrl & select
author: gavinkwoe
date: !binary |-
  MjAwOS0wNi0xNyAxMDowNjowOSArMDgwMA==
date_gmt: !binary |-
  MjAwOS0wNi0xNyAwMjowNjowOSArMDgwMA==
---
<p align="right"><span style="font-size: large;"><strong>Chapter 4, Advanced Serial Programming
第四章，高级串口编程</strong></span>
This chapter covers advanced serial programming techniques using the ioctl(2) and select(2) system calls.
<span style="font-size: large;"><strong>Serial Port IOCTLs</strong>
</span>
In Chapter 2, Configuring the Serial Port we used the tcgetattr and tcsetattr functions to configure the serial port. Under UNIX these functions use the ioctl(2) system call to do their magic. The ioctl system call takes three arguments:
<strong>int ioctl(int fd, int request, ...);</strong>
The fd argument specifies the serial port file descriptor. The request argument is a constant defined in the
<termios.h> header file and is typically one of the constants listed in Table 10.
本章主要谈论使用ioctl(2)和select(2)系统调用的高级串口编程技术。
<p align="left"><span style="font-size: large;"><strong>串行端口 IOCTLs
</strong></span><span style="font-size: large;"><strong>
</strong></span>在第二章，配置串口中我们使用了 tcgetattr 和 tcsetattr 函数来做串口的设置。在 UNIX 下，这些函数都是使用 ioctl(2) 系统调用来完成他们的任务的。ioctl 系统调用有如下三个参数：
<em>int ioctl(int fd, int request, ...);
</em>
参数 fd 指定了串行端口的文件描述符。
参数 request 是一个定义在 <termios.h> 头文件里的常量，它的一些常用值都在 表10 里面定义了。
<p align="center">Table 10 - IOCTL Requests for Serial Ports
表10 - IOCTL 的串口调用
<table border="1" cellspacing="1" cellpadding="0">
<tbody>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><strong><span lang="EN-US"><span style="font-family: 'Times New Roman';">Request</span></span></strong>
</td>
<td width="301">
<p class="MsoNormal" align="center"><strong><span lang="EN-US"><span style="font-family: 'Times New Roman';">Description</span></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><strong><span lang="EN-US"><span style="font-family: 'Times New Roman';">POSIX Function</span></span></strong>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCGETS<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Gets the current serial port settings.</span></span>
<p class="MsoNormal" align="center"><span>读取当前的串口属性</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcgetattr<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCSETS<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Sets the serial port settings immediately</span></span>
<p class="MsoNormal" align="center"><span>设置串口属性并立即生效</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcsetattr(fd, TCSANOW, &options)<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCSETSF<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Sets the serial port settings after flushing the input and output buffers.</span></span>
<p class="MsoNormal" align="center"><span>设置串口属性，等到输入输出缓冲区都清空了再生效</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcsetattr(fd, TCSAFLUSH, &options)<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCSETSW<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Sets the serial port settings after allowing the input and output buffers to drain/empty.</span></span>
<p class="MsoNormal" align="center"><span>设置串口属性，等到允许清空输入输出缓冲区了或数据传完后设置生效</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcsetattr(fd, TCSADRAIN, &options)<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCSBRK<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Sends a break for the given time.</span></span>
<p class="MsoNormal" align="center"><span>在指定时间后发送</span><span lang="EN-US"><span style="font-family: 'Times New Roman';">break<strong></strong></span></span>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcsendbreak, tcdrain<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCXONC<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Controls software flow control.</span></span>
<p class="MsoNormal" align="center"><span>控制软件流控</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcflow<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TCFLSH</span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Flushes the input and/or output queue.</span></span>
<p class="MsoNormal" align="center"><span>将输入输出队列全部发出</span><span lang="EN-US"></span>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">tcflush<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCMGET<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Returns the state of the "MODEM" bits.</span></span>
<p class="MsoNormal" align="center"><span>返回</span><span><span style="font-family: 'Times New Roman';"> <span lang="EN-US">“MODEM” </span></span></span><span>位的状态</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">None<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCMSET<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Sets the state of the "MODEM" bits.</span></span>
<p class="MsoNormal" align="center"><span>设置“</span><span lang="EN-US"><span style="font-family: 'Times New Roman';">MODEM</span></span><span>”位的状态</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">None<strong></strong></span></span>
</td>
</tr>
<tr>
<td width="98">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">FIONREAD<strong></strong></span></span>
</td>
<td width="301">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Returns the number of bytes in the input buffer.</span></span>
<p class="MsoNormal" align="center"><span>返回输入缓冲区内的字节数</span><strong><span lang="EN-US"></span></strong>
</td>
<td width="238">
<p class="MsoNormal" align="center"><span lang="EN-US"><span style="font-family: 'Times New Roman';">None<strong></strong></span></span>
</td>
</tr>
</tbody></table>
<span style="font-size: large;"><strong>Getting the Control Signals</strong></span>
The TIOCMGET ioctl gets the current "MODEM" status bits, which consist of all of the RS-232 signal lines except RXD and TXD, listed in Table 11.
To get the status bits, call ioctl with a pointer to an integer to hold the bits, as shown in Listing 5.
<span style="font-size: large;"><strong>获得控制信号
</strong></span>TIOCMGET - ioctl 获得当前“MODEM”的状态位，其中包括了除 RXD 和 TXD 之外，所有的RS-232 信号线，见列表 11。
为了获得状态位，使用一个包含比特位的整数的指针来调用 ioctl，见清单5。
<p align="left"><em><span style="color: #2200aa;">Listing 5 - Getting the MODEM status bits.
清单 5 - 读取 DODEM 的状态位.
#include <unistd.h>
#include <termios.h>
int fd;
int status;
ioctl(fd, TIOCMGET, &status);</span></em>
<p align="center">Table 11 - Control Signal Constants
表 11 - 控制信号常量
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span style="font-family: 'Times New Roman';"><strong><span lang="EN-US">Constant</span></strong><span lang="EN-US"></span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span style="font-family: 'Times New Roman';"><strong><span lang="EN-US">Description</span></strong><span lang="EN-US"></span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_LE</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">DSR (data set ready/line enable)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_DTR</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">DTR (data terminal ready)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_RTS</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">RTS (request to send)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_ST</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Secondary TXD (transmit)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_SR</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Secondary RXD (receive)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_CTS</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">CTS (clear to send)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_CAR</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">DCD (data carrier detect)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_CD</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Synonym for TIOCM_CAR</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_RNG</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">RNG (ring)</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_RI</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">Synonym for TIOCM_RNG</span></span>
</td>
</tr>
<tr>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">TIOCM_DSR</span></span>
</td>
<td width="322" valign="top">
<p class="MsoNormal" align="left"><span lang="EN-US"><span style="font-family: 'Times New Roman';">DSR (data set ready)</span></span>
</td>
</tr>
</tbody></table>
<strong><span style="font-size: large;">Setting the Control Signals</span></strong>
The TIOCMSET ioctl sets the "MODEM" status bits defined above. To drop the DTR signal you can use the code in Listing 6.
<span style="font-size: large;"><strong>设置控制信号</strong>
</span>TIOCMSET - ioctl 设置“MODEM”上述定义的状态位。可以使用 清单6 的代码来给DTR信号置低。
<em><span style="color: #1f0099;">Listing 6 - Dropping DTR with the TIOCMSET ioctl.
清单 6 - 使用 TIOCMSET ioctl 置低 DTR 信号
#include <unistd.h>
#include <termios.h>
int fd; int status;
ioctl(fd, TIOCMGET, &status);
status &= ~TIOCM_DTR;
ioctl(fd, TIOCMSET, &status);</span></em>
The bits that can be set depend on the operating system, driver, and modes in use. Consult your operating system documentation for more information.
能进行设置的比特位有操作系统，驱动以及使用的模式决定。查询你的操作系统的档案可以获取更多的信息。
<strong><span style="font-size: large;">Getting the Number of Bytes Available</span></strong>
The FIONREAD ioctl gets the number of bytes in the serial port input buffer. As with TIOCMGET you pass in a pointer to an integer to hold the number of bytes, as shown in Listing 7.
<span style="font-size: large;"><strong>获取可供读取的字节数
</strong></span>FIONREAD - ioctl 读取串行端口输入缓冲区中的字节数。与 TIOCMGET 一起传递一个包含字节数的整数的指针，如 清单7 所示。
<em><span style="color: #0000bb;">Listing 7 - Getting the number of bytes in the input buffer.
清单7 - 读取串行端口输入缓冲区中的字节数
#include <unistd.h>
#include <termios.h>
int fd;
int bytes;
ioctl(fd, FIONREAD, &bytes);</span></em>
This can be useful when polling a serial port for data, as your program can determine the number of bytes in the input buffer before attempting a read.
在查询串口是否有数据到来的时候这一段是很有用的，可以让程序在准备读取之前用来确定输入缓冲区里面的可读字节数。
<strong><span style="font-size: large;">Selecting Input from a Serial Port
</span></strong>While simple applications can poll or wait on data coming from the serial port, most applications are not simple and need to handle input from multiple sources.
UNIX provides this capability through the select(2) system call. This system call allows your program to check for input, output, or error conditions on one or more file descriptors. The file descriptors can point to serial ports, regular files, other devices, pipes, or sockets. You can poll to check for pending input, wait for input indefinitely, or timeout after a specific amount of time, making the select system call extremely flexible.
Most GUI Toolkits provide an interface to select; we will discuss the X Intrinsics ("Xt") library later in this chapter.
<span style="font-size: large;"><strong>从串行端口选择输入
</strong></span>虽然简单的程序可以通过poll串口或者等待串口数据到来读取串口，大多数的程序却并不这么简单，有时候需要处理来自多个源的输入。
UNIX 通过 select(2) 系统调用提供了这种能力。这个系统调用允许你的程序从一个或多个文件描述符获取输入/输出或者错误信息。文件描述符可以指向串口，普通文件，其他设备，管道，或者socket 。你可以查询待处理的输入，等待不确定的输入，或者在一指定的超时到达后超时退出，这些都使得 select 系统调用非常的灵活。
大多数的 GUI 工具提供一个借口指向 select，我们会在本章后面的部分讨论 X Intrinsics 库(“Xt”)。
<span style="font-size: large;"><strong>The SELECT System Call</strong></span>
The select system call accepts 5 arguments:
int select(int max_fd, fd_set *input, fd_set *output, fd_set *error, struct timeval *timeout);
The max_fd argument specifies the highest numbered file descriptor in the input, output, and error sets. The input, output, and error arguments specify sets of file descriptors for pending input, output, or error conditions; specify NULL to disable monitoring for the corresponding condition. These sets are initialized using three macros:
FD_ZERO(fd_set);
FD_SET(fd, fd_set);
FD_CLR(fd, fd_set);
The FD_ZERO macro clears the set entirely. The FD_SET and FD_CLR macros add and remove a file descriptor from the set, respectively.
The timeout argument specifies a timeout value which consists of seconds (timeout.tv_sec) and microseconds (timeout.tv_usec). To poll one or more file descriptors, set the seconds and microseconds to zero. To wait indefinitely specify NULL for the timeout pointer.
The select system call returns the number of file descriptors that have a pending condition, or -1 if there was an error.
<span style="font-size: large;"><strong>SELECT 系统调用</strong></span>
Select 系统调用可以接受 5 个参数：
int select(int max_fd, fd_set *input, fd_set *output, fd_set *error, struct timeval *timeout);
参数 max_fd 定义了所有用到的文件描述符（input, output, error集合）中的最大值。
参数 input, output, error 定义了待处理的输入，输出，错误情况；置 NULL 则代表不去监查相应的条件。这几个集合用三个宏来进行初始化
FD_ZERO(fd_set);
FD_SET(fd, fd_set);
FD_CLR(fd, fd_set);
宏 FD_ZERO 清空整个集合； FD_SET 和 FD_CLR 分别从集合中添加、删除文件描述符。
参数 timeout 定义了一个由秒（timeout.tv_sec）和毫秒（timeout.tv_usec）组成的一个超时值。要查询一个或多个文件描述符，把秒和毫秒置为 0 。要无限等待的话就把 timeout 指针置 NULL 。
select 系统调用返回那个有待处理条件的文件描述符的值，或者，如果有错误发生的话就返回 -1。
<span style="font-size: large;">Using the SELECT System Call
</span>Suppose we are reading data from a serial port and a socket. We want to check for input from either file descriptor, but want to notify the user if no data is seen within 10 seconds. To do this we'll need to use the select system call, as shown in Listing 8.
<strong>使用 select 系统调用
</strong>假设我们正在从一个串口和一个socket 读取数据。我们想确认来自各文件描述符的输入，又希望如果10秒钟内都没有数据的话通知用户。要完成这些工作，我们可以像 清单8 这样使用select系统调用：
Listing 8 - Using SELECT to process input from more than one source.
清单8 - 使用select 处理来自多个源的输入
#include <unistd.h>
#include <sys/types.h>
#include <sys/time.h>
#include <sys/select.h>
int n;
int socket;
int fd;
int max_fd;
fd_set input;
struct timeval timeout; /* Initialize the input set 初始化输入集合*/
FD_ZERO(input);
FD_SET(fd, input);
FD_SET(socket, input);
max_fd = (socket > fd ? socket : fd) + 1; /* Initialize the timeout structure 初始化 timeout 结构*/
timeout.tv_sec = 10;
timeout.tv_usec = 0;
/* Do the select */
n = select(max_fd, &input, NULL, NULL, &timeout);
/* See if there was an error 看看是否有错误发生*/
if (n < 0)
perror("select failed");
else if (n == 0)
puts("TIMEOUT");
else { /* We have input 有输入进来了*/
if (FD_ISSET(fd, input))
process_fd();
if (FD_ISSET(socket, input))
process_socket();
}
You'll notice that we first check the return value of the select system call. Values of 0 and -1 yield the appropriate warning and error messages. Values greater than 0 mean that we have data pending on one or more file descriptors.
To determine which file descriptor(s) have pending input, we use the FD_ISSET macro to test the input set for each file descriptor. If the file descriptor flag is set then the condition exists (input pending in this case) and we need to do something.
你会发现，我们首先检查select系统调用的返回值。如果是 0 和 -1 则表示相应的警告和错误信息。大于 0 的值代表我们在一个或多个文件描述符上有待处理的数据。
要知道是哪个文件描述符有待处理的输入，我们要使用 FDISSET 宏来测试每个文件描述符的输入集合。如果文件描述符标志被设置了，则符合条件（这时候就有待处理的输入了）我们需要进行一些操作。
<strong><span style="font-size: large;">Using SELECT with the X Intrinsics Library
</span></strong>The X Intrinsics library provides an interface to the select system call via the XtAppAddInput(3x) and XtAppRemoveInput(3x) functions:
int XtAppAddInput(XtAppContext context, int fd, int mask, XtInputProc proc, XtPointer data);
void XtAppRemoveInput(XtAppContext context, int input);
The select system call is used internally to implement timeouts, work procedures, and check for input from the X server. These functions can be used with any Xt-based toolkit including Xaw, Lesstif, and Motif.
The proc argument to XtAppAddInput specifies the function to call when the selected condition (e.g. input available) exists on the file descriptor. In the previous example you could specify the process_fd or process_socket functions.
Because Xt limits your access to the select system call, you'll need to implement timeouts through another mechanism, probably via XtAppAddTimeout(3x).
<span style="font-size: large;"><strong>在X Intrinsics library 中使用select</strong></span>
X Intrinsics library 提供了一个接口，通过 XtAppAddInput(3x) 和 XtAppRemoveInput(3x) 函数来使用 select 系统调用。
int XtAppAddInput(XtAppContext context, int fd, int mask, XtInputProc proc, XtPointer data);
void XtAppRemoveInput(XtAppContext context, int input);
select 系统调用是用来实现内部的超时，工作过程，并且检查来自 X Server 的输入。这些函数可以在任何 基于Xt 的工具上使用，包括Xaw, Lesstif, 和 Motif。
XtAppAddInput 的参数 proc 定义了当某一个文件描述符上的select条件满足时函数的调用（比如有输入进来）。在之前的例子里，你可以定义process_fd 或 processs_socket 函数。
由于 Xt 限制了你对 select 系统调用的访问，你需要通过其他机制实现超时，可以通过 XtAppAddTimeout(3x)。
<span style="color: #3d0099;">Carol: 已获原作者简体中文翻译授权。
为避免重复劳动，愿意参与翻译的朋友请直接与我联系 puccacarol AT hotmail DOT com
原文名称：Serial Programming Guide for POSIX Operating Systems
<</span><a href="http://www.easysw.com/~mike/serial/"><span style="color: #3d0099;">http://www.easysw.com/~mike/serial/</span></a><span style="color: #3d0099;">>
此翻译为初稿，语言较粗糙，不断完善中。。。 欢迎大家提出宝贵意见。</span>
