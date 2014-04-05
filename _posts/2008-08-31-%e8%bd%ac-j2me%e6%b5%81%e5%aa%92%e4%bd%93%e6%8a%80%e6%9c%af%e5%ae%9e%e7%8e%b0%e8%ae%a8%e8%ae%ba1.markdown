---
layout: post
title: ! '[转] J2me流媒体技术实现讨论[1] '
author: gavinkwoe
date: !binary |-
  MjAwOC0wOC0zMSAxOToxMzowOCArMDgwMA==
date_gmt: !binary |-
  MjAwOC0wOC0zMSAxMToxMzowOCArMDgwMA==
---
看到很多很多人持续在问这个问题。
以前我也听说，好像kvm底层实现不太支持j2me来做streaming video/audio，但我不知道那人为什么这么说。
那么现在国外有一个人提出下面这种思路，并且号称在Nokia6260[相关数据：诺基亚 6260 Nokia62602.0 (3.0436.0) SymbianOS7.0s Series602.1 ProfileMIDP-2.0 ConfigurationCLDC-1.0]
上真实实现了(两种网络方式：蓝牙和GPRS都试验过)，但我怀疑他的前提条件是“你的手机必须允许同时实现player的多个实例进入prefetched状态（预读取声音流）”：
第一步：
声明两个Player；
第二步：
HttpConnection开始向服务器请求该audio文件的第一部分字节，我们定这次读取的字节数为18KB；
第三步：
等第一部分数据到位后，Player A开始realize和prefetch，并开始播放；
第四步：
在Player A播放同时，(18KB的amr数据可以播放10秒钟)，HttpConnection继续请求第二部分数据(假设GPRS每秒钟传输3KB，那么18KB需要传输6秒，算上前后通讯损失的时间，应该不会超过10秒钟)；
第五步：
第二部分数据到位后，假设Player A还没有播放完(这需要调整你的每一部份数据字节数来使得假设成立)，那么将数据喂给Player B让它realize和prefetch；
第六步：
Player A播放完后，得到事件通知，于是让Player B开始播放。
如此往复。
大家看看此种理论可否。
我自己在nokia 7610上测试了一下，我上面说的前提被证明是可行的：“你的手机必须允许同时实现player的多个实例进入prefetched状态（预读取声音流）”。真实Nokia手机确实可以如此：
两个线程中各自有一个Player，都开始做m_player.realize();和m_player.prefetch();，然后等候。
先播放线程1的Player，等她播放完后，
通过
<div style="margin: 5px 20px 20px;">
<div class="smallfont" style="margin-bottom: 2px;">Code:</div>
<pre class="alt2" style="border: 1px inset; margin: 0px; padding: 6px; overflow: auto; width: 620px; height: 242px; text-align: left;" dir="ltr">/*
   * 本类实现了PlayerListener接口。通过这个事件来告知媒体已经播放完毕
   */
  public void playerUpdate(Player player, String event, Object data){
    if(event == PlayerListener.END_OF_MEDIA){
     try{
     System.out.println("playerUpdate>>PlayerListener.END_OF_MEDIA");
     stopGauge();
     playForeground();
     }catch(Exception e){
     e.printStackTrace();
    }
   }
 }</pre>
</div>
来通知第二个线程的Player播放。
这样是可以的。
qinjiwy说“可以,不过前提是该音频文件允许分段播放,有些音频文件就是不允许的.”，你说得对。确实有很多格式的媒体文件不支持分段播放。我所知道的是wav可以，mp3也可以。
服务端每次只读取这两种媒体文件的某一部分，如果是mp3文件的话，我暂时不知道是否每次需要加上特殊的头信息。
但是如果是WAV文件，那么肯定每次都要加上WAV特定的头，要不然Player也无法播放。
这种形式肯定是可行的。因为以前我在VC++上写Text To Speech程序时，就是这么做的：WAV文件的前若干个字节肯定是头信息，这是一定的，随后跟的全是RAW DATA；我每一次读取WAV的RAW DATA若干字节后，传给我的播放线程，他需要给这段RAW DATA前加上一个WAV HEADER，然后就可以正常播放了。
