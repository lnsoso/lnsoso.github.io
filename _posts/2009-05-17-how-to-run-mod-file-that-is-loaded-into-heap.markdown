---
layout: post
title: How to run mod file that is loaded into HEAP
author: gavinkwoe
date: !binary |-
  MjAwOS0wNS0xNyAxNToyMzowNyArMDgwMA==
date_gmt: !binary |-
  MjAwOS0wNS0xNyAwNzoyMzowNyArMDgwMA==
---
05-15-2008, 11:20 PM
 Brewin
Registered User   Join Date: Mar 2006
Posts: 152
Rep Power: 4
 How to run mod file that is loaded in the HEAP...
--------------------------------------------------------------------------------
Hi everyone,
How can I run a MOD file that is loaded into the HEAP???? Any idea's...
__________________
Thanks everyone for the info they share here..
Ramki@TTSL
 VALUE HAS A VALUE ONLY IF ITS VALUE IS VALUED
--------------------------------------------------------------------------------
Last edited by Brewin : 05-15-2008 at 11:27 PM. 
Brewin
View Public Profile
Send a private message to Brewin
Send email to Brewin
Find all posts by Brewin
Add Brewin to Your Buddy List 
  #2   06-04-2008, 07:36 PM
ArdyFalls
Registered User   Join Date: Apr 2007
Posts: 23
Rep Power: 0
Steps for loading a mod into the heap
--------------------------------------------------------------------------------
1) malloc memory on the heap that is the size of the mode file + 4 in bytes.
2) Set the initial 4 btyes to point to the AEE struct thing that has all the AEEStdlib funcitons.
3) Set the processors program counter (PC register) to address 0
Please note, I could be kinda off, look at the code for AEE_ModLoad, it is always at address zero. It will tell you the arguments that you need to pass to it before setting the pc register to it. Remember, the first four arguments of any function are passed in registers r0 -r4.
I hope this helps
__________________
Ardavon Falls
Senior Software Engineer
MobiTV Inc
ArdyFalls
View Public Profile
Send a private message to ArdyFalls
Find all posts by ArdyFalls
Add ArdyFalls to Your Buddy List 
  #3   07-28-2008, 10:45 PM
 Brewin
Registered User   Join Date: Mar 2006
Posts: 152
Rep Power: 4
 Loading and running a compressed mod file...
--------------------------------------------------------------------------------
Hi All,
To run a compressed mod file in BREW you need to create a mod loader(Approxmately 700+ bytes, atleast mine).
For 2.x brew devices, your mod loader file name should be same as of your app name.
Ex: myapp.mif, myapp.mod(your mod loader), filename.gz(your compressed myapp.mod file, you can name this file as filename.bar so that you don't have any problems when user disables your app).
For 3.x brew devices on which we can read a mod file by setting the required permissions in MIF can add this mod loader file content at the start of your compressed mod file or you can use the same as that used for 2.x which don't required any permissions or settings in MIF.
Ex: myapp.mif, myapp.mod(your mod loader + filename.gz).
To create a mod loader, you have every information you need in 2 threads.
First read only the first post by user "ajiva" in the following thread. Then return to this thread for additional info you need.
Quote:
http://brewforums.qualcomm.com/showthread.php?t=11637 
A call to AEEMod_Load that you have seen in the first thread will set the params in the registers.
Quote:
Note 1: Don't release the buffer that contains the uncompressed content of your actual mod file in your mod loader. Once the actual app is closed, it will released by BREW system.
Note2: Release all other allocated buffers and interfaces you created in mod loader before making a call to AEEMod_Load.
Thanks for ajiva and Ardy Falls for the valuable info they provided. 
__________________
Thanks everyone for the info they share here..
Ramki@TTSL
 VALUE HAS A VALUE ONLY IF ITS VALUE IS VALUED
--------------------------------------------------------------------------------
Last edited by Brewin : 08-06-2008 at 12:59 AM. 
Brewin
View Public Profile
Send a private message to Brewin
Send email to Brewin
Find all posts by Brewin
Add Brewin to Your Buddy List 
  #4   07-29-2008, 12:36 AM
Rajni
Registered User   Join Date: Apr 2005
Posts: 32
Rep Power: 0
 Really usefull
--------------------------------------------------------------------------------
Hi Ramki,
Really very useful information.
Rajni
View Public Profile
Send a private message to Rajni
Find all posts by Rajni
Add Rajni to Your Buddy List 
  #5   08-06-2008, 12:57 AM
 Brewin
Registered User   Join Date: Mar 2006
Posts: 152
Rep Power: 4
 Loading and running a compressed extension mod file...
--------------------------------------------------------------------------------
Hi all,
The above procedure is not working in case if the compressed mod file is an extension. Applet compressed mod file is running fine. The decompressed file of the extension mod is Identical with the original extension mod file, but device is getting crashed when AEEMod_Load of the extension mod is executed.
if both an applet and extensions loads the same way then
why is this difference..
If I use normal extension mod everything goes well.
Do anyone has any idea about this. 
Quote:
The extension I used is xmlparser, I will write my own extension and check does it make any difference using debug info. 
__________________
Thanks everyone for the info they share here..
Ramki@TTSL
 VALUE HAS A VALUE ONLY IF ITS VALUE IS VALUED
--------------------------------------------------------------------------------
Last edited by Brewin : 08-06-2008 at 01:02 AM. 
Brewin
View Public Profile
Send a private message to Brewin
Send email to Brewin
Find all posts by Brewin
Add Brewin to Your Buddy List 
  #6   08-06-2008, 06:15 AM
 Brewin
Registered User   Join Date: Mar 2006
Posts: 152
Rep Power: 4
 Loading a compressed module(Application/Extension)
--------------------------------------------------------------------------------
I hope I got the resolution,
Quote:
Originally Posted by Brewin
Hi all,
The above procedure is not working in case if the compressed mod file is an extension. Applet compressed mod file is running fine. The decompressed file of the extension mod is Identical with the original extension mod file, but device is getting crashed when AEEMod_Load of the extension mod is executed.
if both an applet and extensions loads the same way then
why is this difference..
If I use normal extension mod everything goes well.
Do anyone has any idea about this. 
If the module irrespective of Application or Extension, is complied with standard AEEModGen.C then the MOD compression will work else the behavior is unknown.
Hope this helps, Please correct me if I am wrong.
But since all module will have the same entry point why this happens... 
__________________
Thanks everyone for the info they share here..
Ramki@TTSL
 VALUE HAS A VALUE ONLY IF ITS VALUE IS VALUED
--------------------------------------------------------------------------------
Last edited by Brewin : 08-06-2008 at 06:17 AM. 
Brewin
View Public Profile
Send a private message to Brewin
Send email to Brewin
Find all posts by Brewin
Add Brewin to Your Buddy List 
  #7   08-07-2008, 12:32 PM
TG1e
Registered User   Join Date: May 2008
Posts: 3
Rep Power: 0
Reference code please [if possible]
--------------------------------------------------------------------------------
Addressed to all who are working (or have worked) on this topic of loading a mod from heap mem...
Will it be possible for you to post some reference code?
Also, for clarity...
Do we only have to call AEEMod_Load?
Or do we have to call the other functions as well....How does the AEE come to know that we have loaded the compressed (and now decompressed) mod? How will the AEE send events to this newly loaded mod?
Awaiting your response(s)...
--------------------------------------------------------------------------------
Last edited by TG1e : 08-07-2008 at 12:34 PM. 
TG1e
View Public Profile
Send a private message to TG1e
Find all posts by TG1e
Add TG1e to Your Buddy List 
  #8   08-23-2008, 06:51 AM
 Brewin
Registered User   Join Date: Mar 2006
Posts: 152
Rep Power: 4
 Loading compressed mod file..Step by Step
--------------------------------------------------------------------------------
Quote:
Originally Posted by TG1e
Addressed to all who are working (or have worked) on this topic of loading a mod from heap mem...
Will it be possible for you to post some reference code?
What code you need, first compress you MOD file with gzip(if 7zip is used then you need to supply you own decompressor code in the mod loader or bootloader)
Example: You have an application with myapp.mod, myapp.bar, myapp.mif
After compressing MOD, your files are myapp.zip, myapp.bar,myapp.mif.
Now write a bootloader which will load myapp.zip into memory and uncompress..
1. Create a project with name ex: modloader.
2. copy AEEModGen.C and AEEModGen.H into your project and add the following header files
AEE.h, AEEShell.h, AEEUnzipStream.h, AEEFile.h, AEEHeap.h, AEEStdLib.h.
3. Delete everything not related to AEEMod_Load and AEEStaticMod_New in both files and add the below line
Quote:
For AEEMod_Load typecast.....
typedef int (*RunLoadMod)(IShell *ps, void * ph, IModule ** pMod);
2. Delete every thing inside the AEEStaticMod_New function.
3. Now inside the AEEStaticMod_New function declare pointers to IUnzipAStream, IFileMgr and IFile interfaces.
4. Create an Instance of IFileMgr and IUnzipStream(if compressed with gzip). else
provide your own decompress function here.
5.Now allocate some memory and copy the "ph" variable which is the pointer to AEEHelper Functions into the first four bytes of allocated memory.
6. Open myapp.zip and Set it for uncompressing using
" IUNZIPASTREAM_SetStream ".
7. Now read the uncompressed stream using " IUNZIPASTREAM_Read " and copy the contents from 5th byte onwards into the same buffer using MEMCPY, REALLOC as per requirements.
( Now your buffer contains pointer to AEEHelper functions + uncompressed myapp.zip, now buffer size will be a minimum of 4+sizeof(myapp.mod) ).
8. Once your buffer is ready with the above you are done. Release the IFileMgr, Ifile and IUnzipStream interface pointers.
9. Now type cast the 5th byte of the buffer to AEEMod_Load with a return statement like
Quote:
return (RunLoadMod)(buffer+4)(pIShell,ph,ppMod);
to execute the actual mod file....  
Note: Don't release the buffer, once the app exits this memory will be released by AEE.
You modloader is ready now.
Compile and create the mod file and rename the mod file as myapp.mod.
Now you app contains, myapp.mif, myapp.mod, myapp.zip, myapp.bar.
Quote:
Also, for clarity...
Do we only have to call AEEMod_Load?
Or do we have to call the other functions as well....How does the AEE come to know that we have loaded the compressed (and now decompressed) mod? How will the AEE send events to this newly loaded mod?
When you click on your app, AEE loads the mod file associated with the app name in this case it is myapp.mod(mod loader) and executes the first address i.e AEEMod_load which will feed the actual mod to the AEE, it's not the AEE that load your actual MOD file.
There is no applet related to modloader, when AEE encounter the last statement in the AEEStaticMod_New of the modloader AEE just jumps to the address where we have kept the actual MOD file and exectues from there in the process whatever is created and registered(IModuleVtbl, Applet, Applet_HandleEvent) are all belongs to the actual application mod file and AEE will send events to the registered handleEvent of the top visible applet which is nothing but your application handle Event. 
__________________
Thanks everyone for the info they share here..
Ramki@TTSL
 VALUE HAS A VALUE ONLY IF ITS VALUE IS VALUED
--------------------------------------------------------------------------------
Last edited by Brewin : 08-23-2008 at 06:59 AM. 
Brewin
View Public Profile
Send a private message to Brewin
Send email to Brewin
Find all posts by Brewin
Add Brewin to Your Buddy List 
  #9   01-13-2009, 07:55 AM
ed_est
Registered User   Join Date: Jan 2009
Posts: 3
Rep Power: 0
Hi Brewin,
I am interested in the functionality that you have described in this topic and I have tried to create a modloader by myself according the steps you have provided, but unfortunately my device just reboots when I am trying to execute the loaded into memory .mod file. I have tried my code on different devices with different BREW version and on all of them I had device reboot.
Here is my code in AEEStaticMod_New function ( I have simplified it because I don't use compression for .MOD file, also I have removed all checks):
ISHELL_CreateInstance(pIShell, AEECLSID_FILEMGR, (void**) &piFileMgr);
piFile = IFILEMGR_OpenFile(piFileMgr, "realmod.bin", _OFM_READ);
IFILE_GetInfo(piFile, &iFileInfo);
filesize = iFileInfo.dwSize;
pBuf = (byte*) MALLOC (filesize + 4);
MEMCPY(pBuf, ph, 4);
pBuf2 = pBuf + 4;
pFunc = (RunLoadMod)pBuf2;
bytesread = IFILE_Read(piFile, pBuf2, filesize);
IFILE_Release(piFile);
IFILEMGR_Release(piFileMgr);
return pFunc(pIShell,ph,ppMod);
Can you help me to understand why I can't start .MOD file I have loaded by modloader? I am doing all needed steps that you provided in your post and everything seems correct for me, but maybe you will find that I am doing something wrong.
Thanks in advance.
ed_est
View Public Profile
Send a private message to ed_est
Send email to ed_est
Find all posts by ed_est
Add ed_est to Your Buddy List 
  #10   01-13-2009, 08:44 PM
 Brewin
Registered User   Join Date: Mar 2006
Posts: 152
Rep Power: 4
Helper functions Entry point problem.....
--------------------------------------------------------------------------------
Quote:
Originally Posted by ed_est
MEMCPY(pBuf, ph, 4); *********
pBuf2 = pBuf + 4;
((RunLoadMod)pBuf2)(pIShell,ph,ppMod); 
You problem is here. It not properly copying the ph pointer(Helper functions Entry point).
MEMCPY(pBuf, ph, 4);
__________________
Thanks everyone for the info they share here..
Ramki@TTSL
 VALUE HAS A VALUE ONLY IF ITS VALUE IS VALUED
Brewin
View Public Profile
Send a private message to Brewin
Send email to Brewin
Find all posts by Brewin
Add Brewin to Your Buddy List 
  #11   01-14-2009, 06:17 AM
ed_est
Registered User   Join Date: Jan 2009
Posts: 3
Rep Power: 0
Brewin,
Thank you very match for your help.
I have fixed the this part of code and now it works.
