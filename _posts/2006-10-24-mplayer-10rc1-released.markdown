---
layout: post
title: MPlayer 1.0rc1 released
author: Alvin
date: !binary |-
  MjAwNi0xMC0yNCAwOTo0NToyNiArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yNCAwMTo0NToyNiArMDgwMA==
---
MPlayer是Linux 上的电影播放器(也能跑在许多其它Unices上,甚至非x86CPU上).他的win32版本一样强劲. 它能使用众多的本地的XAnim,RealPlayer和Win32 DLL编解码器播放大多数MPEG,VOB,AVI,OGG,VIVO,ASF/WMV,QT/MOV,FLI,RM,NuppelVideo,yuv4mpeg,FILM,RoQ文件.你还能观看VideoCD,SVCD,DVD,3ivx,RealMedia和DivX格式的电影(你根本不需要avifile库).

mplayer的另一个大的特色是广泛的输出设备支持。它可以在X11，Xv，DGA， OpenGL，SVGAlib，fbdev，AAlib，DirectFB下工作，而且你也能使用GGI和SDL(由此可以使用他们支持的各种驱动模式) 和一些低级的硬件相关的驱动模式(比如Matrox，3Dfx和Radeon，Mach64，Permedia3)！他们大多数支持软件或者硬件缩放，因此你能在全屏下观赏电影。MPlayer还支持通过硬件MPEG解码卡显示，诸如DVB 和DXR3与Hollywood+。可以使用European/ISO 8859-1,2(匈牙利语，英语，捷克语等等)，西里尔语，韩语的字体的清晰放大并且反锯齿的字幕(支持10种格式)，和on screen display(OSD)你又觉得如何？
这个播放器能够稳如泰山的播放被破坏的MPEG文件(对一些VCD有用)，而它能播放著名的windows media player 都打不开的的坏的AVI文件。甚至，没有索引部分的AVI文件可播放，你能暂时由重建他们的索引-idx选择，或者用MEncoder永久重建，使你能够在影片中搜索！如你所见，稳定和质量是最重要的事情，而且他的速度是也惊人的。 

<h3>MPlayer 1.0rc1: <em>"Codename intentionally left blank"</em></h3>
<h4>DOCS:</h4>
<ul>
<li>German documentation translation finished </li>
<li>Russian documentation translation synced and almost finished </li></ul>
<h4>Drivers:</h4>
<ul>
<li>IVTV hardware MPEG audio/video decoder output </li>
<li>ALSA audio output: AC3 passthrough now works even when the device name of the digital output port has been set by the user </li>
<li>bicubic OpenGL scaling works with ATI cards </li>
<li>md5sum switched to the libavutil MD5 implementation </li>
<li>support for libcaca 1.0 via compatibility layer </li></ul>
<h4>Decoders:</h4>
<ul>
<li>liba52 updated to 0.7.4 (slightly faster) </li>
<li>SSE optimizations for mp3lib </li>
<li>removed support for obsolete and non-free divx4 libraries </li></ul>
<h4>Demuxers:</h4>
<ul>
<li>audio stream switching in MPEG-TS/PS, Matroska and streams supported by libavformat </li>
<li>audio stream switching between streams with different codecs </li>
<li>libavformat demuxer now honors -alang </li>
<li>chapter seeking in Matroska files </li>
<li>fixed seeking to absolute and percent position for libavformat demuxer </li>
<li>NUT demuxer using libnut </li>
<li>Matroska SimpleBlock support </li></ul>
<h4>Inputs:</h4>
<ul>
<li>split of stream layer from libmpdemux to new stream library </li>
<li>PVR input for hardware MPEG encoder based cards, such as Hauppauge WinTV PVR-150/250/350/500 AKA IVTV but also pvrusb2 and cx88 (requires Linux >= 2.6.18 kernel, featuring native V4L2 MPEG API) </li>
<li>native RTSP input (handles MPEG-TS over RTP) for generic RTSP servers </li>
<li>support for seeking to chapters in dvd:// and dvdnav:// streams </li>
<li>radio support (radio://) </li></ul>
<h4>FFmpeg/libavcodec:</h4>
<ul>
<li>VC-1/WMV3/WMV9 video decoder </li>
<li>Vorbis decoding speedup, now default Vorbis decoder </li>
<li>VMware Video decoder </li>
<li>On2 VP50 and VP62 decoder </li>
<li>lossless audio decoders: WavPack, TTA, Shorten </li>
<li>CAVS decoder </li>
<li>GXF muxer/demuxer </li>
<li>MXF demuxer </li>
<li>much improved FLAC encoder </li>
<li>more H.264 decoding speed improvements, plus support for -lavdopts fast </li>
<li>Theora decoder fixes </li>
<li>preliminary Vorbis encoder </li>
<li>MTV demuxer </li></ul>
<h4>GUI:</h4>
<ul>
<li><strong>Windows version added </strong></li>
<li>drag-and-drop ignored last file </li>
<li>save and load cache setting correctly </li>
<li>working audio stream selection for Ogg and Matroska files </li>
<li>executable names like gmplayer_old etc. will now start GUI as well </li>
<li>-gui/-nogui options </li>
<li>xinerama fixes, now behaves similar to MPlayer without GUI </li></ul>
<h4>Filters:</h4>
<ul>
<li>MMX-optimizations for -vf yadif </li>
<li>MMX-optimizations for -vf zrmjpeg </li></ul>
<h4>MEncoder:</h4>
<ul>
<li>support of x264 encoding via libavcodec </li>
<li>rewrite -x264encopts option parser to use the 264 option parser; likely breaks 3rd party tools as the syntax of some options has changed </li>
<li>removed support for obsolete and non-free divx4 libraries </li></ul>
<h4>Ports:</h4>
<ul>
<li>partial Intel Mac support, --disable-loader --disable-mp3lib is needed </li>
<li>OpenGL can now create windows > screen size under Windows </li>
<li>allow filenames starting with for remote paths on Windows </li></ul>
<h4>Others:</h4>
<ul>
<li>SSA/ASS subtitle renderer </li>
<li>-endpos option for MPlayer </li>
<li>-correct-pts option </li>
<li>UTF-8 used for OSD and subtitles, some bitmap fonts will no longer work correctly and -subcp must be set for all non-UTF-8 subtitles </li>
<li>more audio-truncation fixes </li>
<li>libavutil mandatory for MPlayer compilation </li>
<li>more intuitive -edlout behaviour </li>
<li>-nortc is now default since -rtc has disadvantages with recent kernels </li></ul>
<div><strong>下载:</strong>
MPlayer 1.0rc1 can be downloaded from the following locations. Please be kind to our server and use one of our many mirrors. </div>
<ul>
<li>Switzerland <a href="http://www1.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">HTTP</a> <a href="ftp://ftp1.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">FTP</a> </li>
<li>Hungary <a href="http://www2.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">HTTP</a> <a href="ftp://ftp2.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">FTP</a> </li>
<li>USA <a href="http://www3.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">HTTP</a> </li>
<li>Serbia HTTP <a href="ftp://ftp4.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">FTP</a> </li>
<li>Korea <a href="http://www5.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">HTTP</a> <a href="ftp://ftp5.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">FTP</a> </li>
<li>Sweden HTTP <a href="ftp://ftp6.mplayerhq.hu/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">FTP</a> </li>
<li>Germany <a href="ftp://ftp.fu-berlin.de/unix/X11/multimedia/MPlayer/releases/MPlayer-1.0rc1.tar.bz2">FTP</a> </li></ul>
<div>MPlayer 1.0rc1 is also available on BitTorrent. </div>
<ul>
<li>BitTorrent torrent </li></ul>
<div>MD5SUM: <strong>18c05d88e22c3b815a43ca8d7152ccdc</strong>
SHA1SUM: <strong>a450c0b0749c343a8496ba7810363c9d46dfa73c</strong> 
</div>
<img src="http://www.cnbeta.com/pic/sour.gif" border="0" alt="" /><strong>访问:</strong><a href="http://www.mplayerhq.hu/design7/news.html" target="_blank">官方网站详细更新说明</a> 
<img src="http://www.cnbeta.com/pic/down.gif" border="0" alt="" /><strong>下载:</strong><a href="http://www1.mplayerhq.hu/MPlayer/releases/win32/MPlayer-1.0rc1-gui.zip" target="_blank">MPlayer 1.0rc1 Windows GUI</a>
