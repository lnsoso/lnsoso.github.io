---
layout: post
title: ! '[转] 实现动态加载VXWORKS .O/.OUT模块'
author: gavinkwoe
date: !binary |-
  MjAwOC0wOS0xMSAwMToyMzoyMSArMDgwMA==
date_gmt: !binary |-
  MjAwOC0wOS0xMCAxNzoyMzoyMSArMDgwMA==
---
转至：<a href="http://www.cnblogs.com/hpunix/articles/355758.html">http://www.cnblogs.com/hpunix/articles/355758.html</a>
<span style="font-family: Arial; font-size: xx-small;">整个过程为：
创建文件系统--》下载文件--》加载模块--》查找符号地址并执行</span>
<span style="font-family: Arial; font-size: xx-small;">以下为演示该过程的一个简易实现文件，有使用或者引用的话，也
打个招呼，或者给评论一下：
==============START OF THE FILE=============
/*********************************************************
 * 版权所有 NP系统工作室。
 * 
 * 文件名称：    \FilseSystemCreate.c
 * 文件标识：    
 * 内容摘要：    创建文件系统，在系统启动之后保存文件
 * 其它说明： 
 * 当前版本：    S 1.0
 * 作    者：    william
 * 完成日期：    2005-10-12 17:04
 * 当前责任人-1：william
 *
 * 修改记录1：   
 *    修改日期：2006-3-21 12:00
 *    版 本 号：S 1.0
 *    修 改 人：william
 *    修改内容：创建 
 * 修改记录2：
 **********************************************************/</span>
<span style="font-family: Arial; font-size: xx-small;">#include <taskLib.h>
#include <vxWorks.h>
#include <stdio.h>
#include <string.h> 
#include <ioLib.h >
#include <symLib.h >
#include <loadlib.h ></span>
<span style="font-family: Arial; font-size: xx-small;">#include "dosFsLib.h"
#include "ramDrv.h"
#include "usrLib.h"</span>
<span style="font-family: Arial; font-size: xx-small;">typedef int                       STATUS;
#define ERROR                     -1
#define OK                        0
#define DIAG_RAM_DISK_SIZE        (0x100000*10)  /* 64M */
#define DIAG_RAM_DISK_NAME        "/selfdev/"</span>
<span style="font-family: Arial; font-size: xx-small;">STATUS FileSystem_Init()
{
    BLK_DEV     *pBlkDev;
    char        *pFileSysRamDiskBase = NULL;
    
    ramDrv();
    pFileSysRamDiskBase = malloc(DIAG_RAM_DISK_SIZE);
    if(NULL == pFileSysRamDiskBase)
        return ERROR;
        
    bzero(pFileSysRamDiskBase,DIAG_RAM_DISK_SIZE);</span>
<span style="font-family: Arial; font-size: xx-small;">    pBlkDev = ramDevCreate( pFileSysRamDiskBase,  
                            /* start address */                
                            512,        
                            /* sector size */                                                  
                            64,     
                            /* sectors per track */                                                      
                            (int)(DIAG_RAM_DISK_SIZE/512),    
                            /* total sectors 64 MBytes */
                            0);                               
                            /* offset */
    if(NULL == pBlkDev)
    {
        free(pFileSysRamDiskBase);
        return ERROR;
    }</span>
<span style="font-family: Arial; font-size: xx-small;">    if(NULL == dosFsMkfs (DIAG_RAM_DISK_NAME, pBlkDev))
    {
        free(pFileSysRamDiskBase);
        return ERROR;</span>
<span style="font-family: Arial; font-size: xx-small;">    }</span>
<span style="font-family: Arial; font-size: xx-small;">    return OK;
}</span>
<span style="font-family: Arial; font-size: xx-small;">extern SYMTAB_ID sysSymTbl ;
void runModule()
{
    STATUS status=ERROR;
    int fd;     
    MODULE_ID hModule ;
    FUNCPTR taskEntry = NULL ;
    SYM_TYPE * pType ;</span>
<span style="font-family: Arial; font-size: xx-small;">    if ((fd = open("/selfdev/youown.o", O_RDONLY, 0)) < 0) 
    {
          printf("\nCannot open memory device.\n");
          goto done;
    }
     
    if ((hModule=loadModule(fd,LOAD_ALL_SYMBOLS))==NULL)
    {
        printf("loadModule error = 0x%x.\n",errno) ;
        goto done;
    }
   
    status = symFindByName(sysSymTbl,
                           "willian_test",
                           (char **)&taskEntry,pType ) ;
    if (status==ERROR)
    {
          printf("symFindByName error=%d\\n", errno) ;
          goto done;
    }
    else
    {
        printf("taskEntryr=0x%x, type=%d\n.",
                    (int)taskEntry,(int)*pType);         
        status = taskSpawn("test1",
                           100,
                           0,
                           30000,
                           taskEntry,
                           0,0,0,0,0,0,0,0,0,0) ;
        if (status==ERROR)
        {
            printf("taskSpawn error=%d\n",errno) ;
            goto done;
        }
    }
    
done: 
     if (fd >= 0)
       close(fd);    
}</span>
 
<span style="font-family: Arial; font-size: xx-small;">int downLoadModules(
    char *hostName,
    char *srcfileName,
    char *destfileName,
    char *usr,
    char *passwd)
{
    .........
    /*实现下载远端PC模块到本地机*/</span>
<span style="font-family: Arial; font-size: xx-small;">    return (filesize);
}</span>
<span style="font-family: Arial; font-size: xx-small;">STATUS test_dynamic_download()
{
    STATUE status = OK;
    int fileLenth = 0;
    
    status = FileSystem_Init();
    if(status == ERROR)
    {
        printf("\nerror occured during init file system\n");
        return ERROK;        
    }
    
    fileLenth = downLoadModules("winner2",
                                "youown.o",
                                "/selfdev/youown.o",
                                "target",
                                "target");
    if(ERROR == fileLenth)
    {
        printf("\nSome error occured when download files\n"); 
        return ERROK;                 
    }                                
    else
    {
        if(0 == fileLenth)  
        {
            return OK;    
        }
    }
    
    runModule();
}
=============END OF THE FILE==========</span>
<span style="font-family: Arial; font-size: xx-small;">几个需要注意的问题：
1：在文件系统初始化中使用的是MALLOC，该部分是使用的实际是
   BSP的空间在实际申请的过程中一定要根据自己可能拷贝的文
   件大小和实际的BSP的空间</span>
<span style="font-family: Arial; font-size: xx-small;">2：在创建文件系统之后，可以查看该目录的文件列表，或者执行
   COPY，前提是必须在TORNADO中选择初始化文件系统组件；
   
3：在下载文件的过程中，有的BSP初始化已经可以直接通过COPY
   在远端FTP SERVER直接拷贝文件，比如：
         copy "youown.o","/selfdev/youown.o"
   这种情况没有必要再调用downLoadModules，至于如何使能，
   大家关注的话可以在后边对其专门论述。     </span>
<span style="font-family: Arial; font-size: xx-small;">4：加载模块的过程可以调用如下代码：
        if ((hModule=loadModule(fd,LOAD_ALL_SYMBOLS))==NULL)
    同样也可以如下实现：
        ld(1,0,"/selfdev/youown.o");
    至于各个参数什么意思，参考HELP就可以找到结论了！
    
    
5：在    status = symFindByName(sysSymTbl,
                           "willian_test",
                           (char **)&taskEntry,pType ) ;
   中，有可能实际返回错误，但是通过lkup 是可以实际找到的，
   对于这个问题，我看网上答案比较少，原因是这样的：
   
   假如你在主机运行shell,默认的远端目标机是没有符号表
   （symbol table）的，只有在主机的shell上，你运行LKUP的
   时候，才能找到这个符号，不信你可以这样尝试一下，打开两
   个SHELL，一个通过串口，一个通过TORNADO的网口，你在其中
   一个中能够使用LKUP找到，而在另外一个则不能找到，是不是
   呀？？   
       接下来继续解释，我们的symFindByName是在目标机器上
   执行的，所以自然的就返回错误了，也是没有找到了！
       解决方法是这样的：在目标机中包含符号表，同时将其和
   主机保持同步。
   Wind River told us to include all this defines in the 
        config files for the target:
        
       #define INCLUDE_SYM_TBL_SYNC
       #define INCLUDE_LOADER
       /* object module loading */
       #define INCLUDE_NET_SYM_TBL  
       /* load the symbol table by whatever means */
       #define INCLUDE_SHELL 
       /* target-based shell */
       #define INCLUDE_SHOW_ROUTINES 
       /* optional target-based system utilities */
       #define INCLUDE_STAT_SYM_TBL  
       /* optional target error status routines */
       #define INCLUDE_SYM_TBL         
       /* symbol table package */
       #define INCLUDE_UNLOADER        
       /* optional object module unloading */
       #define INCLUDE_WDB</span>
