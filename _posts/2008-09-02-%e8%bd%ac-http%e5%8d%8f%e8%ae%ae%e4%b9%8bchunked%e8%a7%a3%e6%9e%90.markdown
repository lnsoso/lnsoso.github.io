---
layout: post
title: ! '[转] HTTP协议之Chunked解析'
author: gavinkwoe
date: !binary |-
  MjAwOC0wOS0wMiAyMTo1OTowMCArMDgwMA==
date_gmt: !binary |-
  MjAwOC0wOS0wMiAxMzo1OTowMCArMDgwMA==
---
转至: <a href="http://hi.baidu.com/zkheartboy/blog/item/9216a0fd05591e1508244d74.html">http://hi.baidu.com/zkheartboy/blog/item/9216a0fd05591e1508244d74.html</a>
在网上找了好一会，始终没发现有解析Chunked编码的文章，那就自己写一个吧，呵呵。
网上使用Chunked编码的网站似乎并不是很多，除了那些使用GZip压缩的网站，例：google.com，还有就是大部分打开GZip压缩的PHP论坛。
根据本人的理解，使用Chunked编码的主要好处就在于一些程序的运算出过程中，可以动态的输出内容。
例如，要在后台处理一个小时的运算，但又不希望用户等一个小时才能看到结果。这时就可采用Chunked编码将内容分块输出，用户随时都可以接收到最新的处理结果。
ASP关闭了缓存的输出模式，就是Chunked编码的。(Response.Buffer = false)
而每一次的Response.Write，都是一个Chunked，所以不要使用的太频繁哦，否则Chunk数量太多，额外的数据太浪费空间了。
若想了解Chunked的具体编码结构，用ASP关闭缓存调试蛮方便的。:)
我们先来看看RFC2616中对Chunked的定义：
Chunked-Body = *chunk
last-chunk
trailer
CRLF
chunk = chunk-size [ chunk-extension ] CRLF
chunk-data CRLF
chunk-size = 1*HEX
last-chunk = 1*("0") [ chunk-extension ] CRLF
chunk-extension= *( ";" chunk-ext-name [ "=" chunk-ext-val ] )
chunk-ext-name = token
chunk-ext-val = token | quoted-string
chunk-data = chunk-size(OCTET)
trailer = *(entity-header CRLF)
我们来模拟一下数据结构：
[Chunk大小][回车][Chunk数据体][回车][Chunk大小][回车][Chunk数据体][回车][0][回车]
注意chunk-size是以十六进制的ASCII码表示的，比如86AE（实际的十六进制应该是：38366165），计算成长度应该是：34478，表示从回车之后有连续的34478字节的数据。
跟踪了www.yahoo.com的返回数据，发现在chunk-size中，还会多一些空格。可能是固定长度为7个字节，不满7个字节的，就以空格补足，空格的ASCII码是0x20。
以下是解码过程的伪代码：
length := 0//用来记录解码后的数据体长度
read chunk-size, chunk-extension (if any) and CRLF//第一次读取块大小
while (chunk-size > 0) {//一直循环，直到读取的块大小为0
read chunk-data and CRLF//读取块数据体，以回车结束
append chunk-data to entity-body//添加块数据体到解码后实体数据
length := length + chunk-size//更新解码后的实体长度
read chunk-size and CRLF//读取新的块大小
}
read entity-header//以下代码读取全部的头标记
while (entity-header not empty) {
append entity-header to existing header fields
read entity-header
}
Content-Length := length//头标记中添加内容长度
Remove "chunked" from Transfer-Encoding//头标记中移除Transfer-Encoding
有空再研究一下GZip＋Chunked是如何编码的，估计是每个Chunk块进行一次GZip独立压缩。
使用了Chunked，自然会在性能上稍微打点折扣，因为比正常的数据体多出了一些额外的消耗。
但是有一些情况下，必需要使用分块输出，这也是不得已而为之～^_^
