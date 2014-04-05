---
layout: post
title: ! '[转] J2me流媒体技术实现讨论[3] '
author: gavinkwoe
date: !binary |-
  MjAwOC0wOC0zMSAxOToxNjowOCArMDgwMA==
date_gmt: !binary |-
  MjAwOC0wOC0zMSAxMToxNjowOCArMDgwMA==
---
Jffmpeg应该是对 ffmpeg 这个C编写的工具的Java封装。
另一个封装的是
<a href="http://fobs.sourceforge.net/" target="_blank">http://fobs.sourceforge.net/</a>
FOBS, the C++ & JMF wrapper for ffmpeg.
Cleverpig said：“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">其实，感觉上可以自己编写一套流媒体规范的实现，比如将源文件指定为wav格式或者其它的raw格式，然后分段发送到mobile。。
但是这样做确实效率低，而且浪费带宽。本人研究了一下Tea Vui Huang的mobilecast实现有些心得，在此与大家讨论一下：
1。使用MMS发送radiocast：由于MMS服务可以使用图片、音乐等多媒体元素，而且技术比较成熟，所以将它作为radiocast的载体是方便的选择。而对于mobile用户来讲，cast的使用方式可以采用请求和订阅两种模式；
2。radio文件格式的选择：对于某些手机不能支持mp3格式文件，即使支持mp3也受到memory size的限制，所以采用更为普遍、压缩比更大的amr格式是比较好的choice；
3。amr文件的分割：由于目前大多数手机仅能支持100KB左右的彩信，所以最佳的cast长度应该是50秒。比如将大约5分钟的mp3文件分割为6个 amr章节文件，每个章节文件所包含的audio长度为45-50秒。而每个amr格式的压缩比将是普通mp3格式3-6倍。按照播放率为 600KB/min的mp3格式计算，保守地假定amr格式压缩比为mp3格式的6倍，amr格式的播放率为100KB/min，而45秒的amr文件大 小为75KB。
所以Tea Vui Huang的做法是很clever的。”</td>
</tr>
</tbody></table>
</div>
我试验过了，利用ffmpeg的这两个参数，可以控制让ffmpeg来将一个大mp3劈分成许多小段的独立播放的amr文件。
-ss time_off        set the start time offset
-t duration         set the recording time
比如你写这么个perl文件，然后运行：
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Code:</div>
<pre class="alt2" style="border: 1px inset; margin: 0px; padding: 6px; overflow: auto; width: 620px; height: 114px; text-align: left;" dir="ltr">@inputFilename = "C:\\opt\\media\\changjin.wma";
@outputFilename = "C:\\opt\\media\\changjin";
for($i=1,$j=1;$i<=1000;$i+=10,$j++)
{
    system("C:\\software\\ffmpeg.exe -i @inputFilename -ac 1 -acodec amr_nb -t 10 -ss $i @outputFilename.$j.\".amr\"");
}</pre>
</div>
就把一个大文件拆分成许多小amr了，每一个amr文件只有17KB。
Qinjiwy said：“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">提一个优化的小建议
如果分段太小,播放的间断太多的话,用户感觉上和系统开销都不是很合适.
可以考虑多开几个线程, 另外,每个文件不一定要一样大,可以考虑
文件逐渐增大,从目前移动网速计算,
压缩比高的amr语音文件播放的时间要比下载的时间长.在第一次下载后开始播放的这段时间中,就
可以下载比第一次下载大的文件了,这样能减少网络开销</td>
</tr>
</tbody></table>
</div>
”
Cleverpig said“
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Quote:</div>
<table border="0" cellspacing="0" cellpadding="6" width="100%">
<tbody>
<tr>
<td class="alt2" style="border: 1px inset;">to qinjiwy：这个边收听边下载的方法可以作为一个应用程序选项，因为并不是每个人都需要不间断的听，也许只想听第一段试试看，如果好的话再继续听下去。而且有些人还可能直接从中间的部分收听，如果这时文件变大的话，可能等待时间更长。</td>
</tr>
</tbody></table>
</div>
”
