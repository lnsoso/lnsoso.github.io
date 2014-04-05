---
layout: post
title: 活学活用Windows Server 2003分区增容功能
author: gavinkwoe
date: !binary |-
  MjAwNy0wMS0wNyAxMDozOTo0OCArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wMS0wNyAwMjozOTo0OCArMDgwMA==
---
分区增容就是当一个分区的空间不能满足使用需求时，为其额外加大空间的方法。很多朋友遇到这种情况时，一般都使用PartitionMagic完成的。但实际上，使用Windows XP/Server 2003的用户完全可以使用系统内置的磁盘管理功能来完成分区的空间“增容”。下面我们以实例的方式来探讨一下。
　　一、划出自由空间
假设现在需要对D盘增容50MB的空间，这个空间需要从E盘上提取。那么首先要就从E盘上划分出这50MB的空间才行。这个操作的过程如下：
　　首先将E盘所有数据转移到其它分区，然后单击“开始&rarr;运行”，输入“Diskmgmt.msc”后回车，打开“磁盘管理”窗口。选中E盘并点击右键，在弹出的快捷菜单中选择“删除此逻辑驱动器”项。在弹出的提示框中点击“是”按钮继续。操作完毕后，将会在“磁盘0”列中出现与删除分区相同大小的可用空间。
　　二、给分区增容
　　此时请注意D盘当前空间为855MB，现在我们来进行为其增加50MB的操作。单击“开始&rarr;程序&rarr;附件&rarr;命令提示符”，在打开的窗口中依次输入“Diskpart”、“List volume”、“Select volume 2”、“Extend Size=50”四条命令。
　　其中，“Diskpart”命令用来调用DOS磁盘管理程序，“Diskpart/?”命令可以看到该命令的DOS下中文帮助信息。“List Volume”用于显示系统上所有磁盘的详细信息，从而得知所需扩充分区的卷号。这里可以看出D盘的卷号为“2”；“Select Volume 2”命令用于选择卷，这里根据上一步得出的提示选择卷2；“Extend Size=50”用于将D盘空间增容，这个增容的来源空间当然是划分出的自由空间了。从命令执行的结果“DiskPart成功地扩展了卷”来看，我们对D盘的空间增容已经成功了。
　　最后在“磁盘管理窗口”中选择剩余的可用空间，依次点击“操作&rarr;所有任务&rarr;新建逻辑驱动器”命令，根据提示为该空间分配驱动器号和进行格式化操作即可。<!--NEWSZW_HZH_BEGIN-->
