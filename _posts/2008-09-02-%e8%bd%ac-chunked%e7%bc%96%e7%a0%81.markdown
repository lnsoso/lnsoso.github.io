---
layout: post
title: ! '[转] Chunked编码'
author: gavinkwoe
date: !binary |-
  MjAwOC0wOS0wMiAyMTo1NzowMiArMDgwMA==
date_gmt: !binary |-
  MjAwOC0wOS0wMiAxMzo1NzowMiArMDgwMA==
---
转至: <a title="Chunked编码" href="http://www.21andy.com/blog/20071109/660.html">http://www.21andy.com/blog/20071109/660.html</a>
有时候，Web服务器生成HTTP Response是无法在Header就确定消息大小的，这时一般来说服务器将不会提供Content-Length的头信息，而采用Chunked编码动态的提供body内容的长度。
进行Chunked编码传输的HTTP Response会在消息头部设置：
<div class="hl-surround">
<div class="hl-main">Transfer-Encoding: chunked</div>
</div>
表示Content Body将用Chunked编码传输内容。
Chunked编码使用若干个Chunk串连而成，由一个标明长度为0的chunk标示结束。每个Chunk分为头部和正文两部分，头部内容指定下一段正文的字符总数（十六进制的数字）和数量单位（一般不写），正文部分就是指定长度的实际内容，两部分之间用回车换行(CRLF)隔开。在最后一个长度为0的Chunk中的内容是称为footer的内容，是一些附加的Header信息（通常可以直接忽略）。具体的Chunk编码格式如下：
<div class="hl-surround">
<div class="hl-main">　　Chunked-Body = *chunk
　　　　　　　　　"0" CRLF
　　　　　　　　　footer
　　　　　　　　　CRLF
　　chunk = chunk-size [ chunk-ext ] CRLF
　　　　　　chunk-data CRLF
　　hex-no-zero = <HEX excluding "0">
　　chunk-size = hex-no-zero *HEX
　　chunk-ext = *( ";" chunk-ext-name [ "=" chunk-ext-value ] )
　　chunk-ext-name = token
　　chunk-ext-val = token | quoted-string
　　chunk-data = chunk-size(OCTET)
　　footer = *entity-header</div>
</div>
RFC文档中的Chunked解码过程如下：
<div class="hl-surround">
<div class="hl-main">　　length := 0
　　read chunk-size, chunk-ext (if any) and CRLF
　　while (chunk-size > 0) {
　　read chunk-data and CRLF
　　append chunk-data to entity-body
　　length := length + chunk-size
　　read chunk-size and CRLF
　　}
　　read entity-header
　　while (entity-header not empty) {
　　append entity-header to existing header fields
　　read entity-header
　　}
　　Content-Length := length
　　Remove "chunked" from Transfer-Encoding</div>
</div>
最后提供一段PHP版本的chunked解码代码：
<div class="hl-surround">
<div class="hl-main"><span style="color: #00008b;">$chunk_size</span><span style="color: gray;"> = </span><span style="color: olive;">(</span><span style="color: blue;">integer</span><span style="color: olive;">)</span><span style="color: blue;">hexdec</span><span style="color: olive;">(</span><span style="color: blue;">fgets</span><span style="color: olive;">(</span><span style="color: gray;"> </span><span style="color: #00008b;">$socket_fd</span><span style="color: gray;">, </span><span style="color: maroon;">4096</span><span style="color: gray;"> </span><span style="color: olive;">)</span><span style="color: gray;"> </span><span style="color: olive;">)</span><span style="color: gray;">;
</span><span style="color: green;">while</span><span style="color: olive;">(</span><span style="color: gray;">!</span><span style="color: blue;">feof</span><span style="color: olive;">(</span><span style="color: #00008b;">$socket_fd</span><span style="color: olive;">)</span><span style="color: gray;"> && </span><span style="color: #00008b;">$chunk_size</span><span style="color: gray;"> > </span><span style="color: maroon;">0</span><span style="color: olive;">)</span><span style="color: gray;"> </span><span style="color: olive;">{</span><span style="color: gray;">
    </span><span style="color: #00008b;">$bodyContent</span><span style="color: gray;"> .= </span><span style="color: blue;">fread</span><span style="color: olive;">(</span><span style="color: gray;"> </span><span style="color: #00008b;">$socket_fd</span><span style="color: gray;">, </span><span style="color: #00008b;">$chunk_size</span><span style="color: gray;"> </span><span style="color: olive;">)</span><span style="color: gray;">;
    </span><span style="color: blue;">fread</span><span style="color: olive;">(</span><span style="color: gray;"> </span><span style="color: #00008b;">$socket_fd</span><span style="color: gray;">, </span><span style="color: maroon;">2</span><span style="color: gray;"> </span><span style="color: olive;">)</span><span style="color: gray;">; </span><span style="color: #ffa500;">// skip \r\n</span><span style="color: gray;">
    </span><span style="color: #00008b;">$chunk_size</span><span style="color: gray;"> = </span><span style="color: olive;">(</span><span style="color: blue;">integer</span><span style="color: olive;">)</span><span style="color: blue;">hexdec</span><span style="color: olive;">(</span><span style="color: blue;">fgets</span><span style="color: olive;">(</span><span style="color: gray;"> </span><span style="color: #00008b;">$socket_fd</span><span style="color: gray;">, </span><span style="color: maroon;">4096</span><span style="color: gray;"> </span><span style="color: olive;">)</span><span style="color: gray;"> </span><span style="color: olive;">)</span><span style="color: gray;">;
</span><span style="color: olive;">}</span></div>
</div>
