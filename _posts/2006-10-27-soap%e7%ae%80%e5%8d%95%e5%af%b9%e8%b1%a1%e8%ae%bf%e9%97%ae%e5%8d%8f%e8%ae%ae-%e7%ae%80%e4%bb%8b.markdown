---
layout: post
title: SOAP(简单对象访问协议) 简介
author: gavinkwoe
date: !binary |-
  MjAwNi0xMC0yNyAxMjo0ODozMiArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yNyAwNDo0ODozMiArMDgwMA==
---
<span style="FONT-SIZE: 13px">SOAP(Simple Object Access Protocal，简单对象访问协议) 技术有助于实现大量异构程序和平台之间的互操作性，从而使存在的应用能够被广泛的用户所访问。SOAP是把成熟的基于HTTP的WEB技术与XML的灵活性和可扩展性组合在了一起。</span>
<span style="FONT-SIZE: 13px">SOAP由MS和IBM共同制定
用于规范WEB服务标准 实现异构程序与平台间的数据交换
它是基于XML的协议，包括三个部分： 封套(envelope)定义了消息内容和处理的框架、一套编码规则用来表达应用定义数据类型的实例以及表达远程过程调用和响应的协定。
与已定义的中间件不同 SOAP只是定义了一种基于XML的文本格式 而没有定义什么ORB代理或是SOAP API 因此用户可以方便的开发自己的应用而不必担心兼容性(corba与dcom间的兼容性在soap中不会再出现)
下面是一篇简介 更多介绍在动网先锋中可以找到
http://www.aspsky.net/article/show.aspx?id=2001
简单对象协议（SOAP）简介
作者：何杭军

简单对象访问协议-CNXML标准教程 
2000-9-25 作者：何杭军
"SOAP是在非集中、分布环境中交换信息的轻量级协议。它是基于XML的协议，包括三个部分： 封套(envelope)定义了消息内容和处理的框架、一套编码规则用来表达应用定义数据类型的实例以及表达远程过程调用和响应的协定。"
&mdash;&mdash;SOAP 1.1规范
第一节 SOAP简介
SOAP(Simple Object Access Protocal，简单对象访问协议) 技术有助于实现大量异构程序和平台之间的互操作性，从而使存在的应用能够被广泛的用户所访问。SOAP是把成熟的基于HTTP的WEB技术与XML的灵活性和可扩展性组合在了一起。
SOAP的一个主要目标是使存在的应用能被更广泛的用户所使用。为了实现这个目的，没有任何SOAP API或SOAP 对象请求代理（SOAP ORB），SOAP是假设你将使用尽可能多的存在的技术。几个主要的CORBA厂商已经承诺在他们的ORB产品中支持SOAP协议。微软也承诺在将来的 COM版本中支持SOAP。DevelopMentor已经开发了参考实现，它使得在任何平台上的任何Java或Perl程序员都可以使用SOAP。而且 IBM和Sun也陆续支持了SOAP协议，和MS合作共同开发SOAP规范和应用。目前SOAP已经成为了W3C和IETF的参考标准之一。
SOAP的指导理念是“它是第一个没有发明任何新技术的技术”。它采用了已经广泛使用的两个协议：HTTP和XML。HTTP用于实现SOAP的RPC风格的传输，而XML是它的编码模式。采用几行代码和一个XML解析器，HTTP服务器（如MS的IIS或Apache）立刻成为了SOAP的ORBs。因为目前超过一半的Web服务器采用IIS或Apache, SOAP将会从这两个产品的广泛而可靠的使用中获取利益。这并不意味着所有的SOAP请求必须通过Web服务器来路由，传统的Web 服务器只是分派SOAP请求的一种方式。因此Web服务如IIS或Apache对建立SOAP性能的应用是充分的，但决不是必要的。
SOAP把XML的使用代码化为请求和响应参数编码模式，并用HTTP作传输。这似乎有点抽象。具体地讲，一个SOAP方法可以简单地看作遵循SOAP编码规则的HTTP请求和响应。一个SOAP终端则可以看作一个基于HTTP的URL，它用来识别方法调用的目标。象CORBA/IIOP一样，SOAP不需要具体的对象被绑定到一个给定的终端，而是由具体实现程序来决定怎样把对象终端标识符映射到服务器端的对象。
SOAP请求是一个HTTP POST请求。SOAP请求的content-type必须用text/xml。而且它必须包含一个请求-URI。服务器怎样解释这个请求-URI是与实现相关的，但是许多实现中可能用它来映射到一个类或者一个对象。一个SOAP请求也必须用SOAPMethodName HTTP头来指明将被调用的方法。简单地讲，SOAPMethodName头是被URI指定范围的应用相关的方法名，它是用#符作为分隔符将方法名与 URI分割开：
SOAPMethodName: urn:strings-com:IString#reverse 
这个头表明方法名是reverse，范围URI是urn:strings-com:Istring。 在SOAP中，规定方法名范围的名域URI在功能上等同于在DCOM 或 IIOP中规定方法名范围的接口ID。
简单的说，一个SOAP请求的HTTP体是一个XML文档，它包含方法中[in]和[in,out]参数的值。这些值被编码成为一个显著的调用元素的子元素，这个调用元素具有SOAPMethodName HTTP头的方法名和名域URI。调用元素必须出现在标准的SOAP <Envelope>;和<Body>;元素内（后面会更多讨论这两个元素）。下面是一个最简单的SOAP方法请求：
POST /string_server/Object17 HTTP/1.1
Host: 209.110.197.2
Content-Type: text/xml
Content-Length: 152
SOAPMethodName: urn:strings-com:IString#reverse 
<Envelope>;
<Body>;
<m:reverse xmlns:m=''urn:strings-com:IString''>;
<theString>;Hello, World</theString>;
</m:reverse>;
</Body>;
</Envelope>;
SOAPMethodName头必须与<Body>;下的第一个子元素相匹配，否则调用将被拒绝。这允许防火墙管理员在不解析XML的情况下有效地过滤对一个具体方法的调用。
SOAP响应的格式类似于请求格式。响应体包含方法的[out]和 [in,out]参数，这个方法被编码为一个显著的响应元素的子元素。这个元素的名字与请求的调用元素的名字相同，但以Response后缀来连接。下面是对前面的SOAP请求的SOAP响应：
200 OK Content-Type: text/xml 
Content-Length: 162 
<Envelope>; 
<Body>; 
<m:reverseResponse xmlns:m=''urn:strings-com:IString''>;
<result>;dlroW ,olleH</result>;
</m:reverseResponse>;
</Body>;
</Envelope>; 
这里响应元素被命名为reverseResponse，它是方法名紧跟Response后缀。要注意的是这里是没有SOAPMethodName HTTP头的。这个头只在请求消息中需要，在响应消息中并不需要。
第二节 SOAP体的核心
SOAP的XML特性是为把数据类型的实例序列化成XML的编码模式。为了达到这个目的，SOAP不要求使用传统的RPC风格的代理。而是一个SOAP方法调用包含至少两个数据类型：请求和响应。考虑这下面个COM IDL代码：

[ uuid(DEADF00D-BEAD-BEAD-BEAD-BAABAABAABAA) ]
interface IBank : IUnknown {
HRESULT withdraw([in] long account, 
[out] float *newBalance,
[in, out] float *amount
[out, retval] VARIANT_BOOL *overdrawn);
}
在任何RPC协议下，account和amount参数的值将出现在请求消息中，newBalance、overdrawn参数的值，还有amount参数的更新值将出现在响应消息中。
SOAP把方法请求和方法响应提升到了一流状态。在SOAP中，请求和响应实际上类型的实例。为了理解一个方法比如IBank::withdraw怎样映射一个SOAP请求和响应类型，考虑下列的数据类型：
struct withdraw {
long account;
float amount;
};
这时所有的请求参数被打包成为单一的结构类型。同样下面的数据表示打包所有响应参数到单一的数据类型。 
struct withdrawResponse {
float newBalance;
float amount;
VARIANT_BOOL overdrawn;
};
再给出下面的简单的Visual Basic程序，它使用了以前定义的Ibank接口：
Dim bank as IBank
Dim amount as Single
Dim newBal as Single
Dim overdrawn as Boolean
amount = 100
Set bank = GetObject("soap:http://bofsoap.com/am"<img src="http://bbs.chinaunix.net/images/smilies/icon_wink.gif" align="middle" border="0" alt="" />
overdrawn = bank.withdraw(3512, amount, newBal)

这里，在发送请求消息之前，参数被序列化成为一个请求对象。同样被响应消息接收到的响应对象被反序列化为参数。一个类似的转变同样发生在调用的服务器端。
当通过SOAP调用方法时，请求对象和响应对象被序列化成一种已知的格式。每个SOAP体是一个XML文档，它具有一个显著的称为< Envelope>;的根元素。标记名<Envelope>;由SOAP URI (urn:schemas-xmlsoap-org:soap.v1)来划定范围，所有SOAP专用的元素和属性都是由这个URI来划定范围的。SOAP Envelope包含一个可选的<Header>;元素，紧跟一个必须的<Body>;元素。<Body>;元素也有一个显著的根元素，它或者是一个请求对象或者是一个响应对象。下面是一个IBank::withdraw请求的编码：
<soap:Envelope xmlns:soap=''urn:schemas-xmlsoap-org:soap.v1''>;
<soap:Body>;
<IBank:withdraw xmlns:IBank=''urn:uuid<img src="http://bbs.chinaunix.net/images/smilies/icon_biggrin.gif" align="middle" border="0" alt="" />EADF00D-BEAD-BEAD-BEAD-BAABAABAABAA''>;
<account>;3512</account>;
<amount>;100</amount>;
</IBank:withdraw>;
</soap:Body>;
</soap:Envelope>;
下列响应消息被编码为： 
<soap:Envelope xmlns:soap=''urn:schemas-xmlsoap-org:soap.v1''>;
<soap:Body>;
<IBank:withdrawResponse xmlns:IBank=''urn:uuid<img src="http://bbs.chinaunix.net/images/smilies/icon_biggrin.gif" align="middle" border="0" alt="" />EADF00D-BEAD-BEAD-BEAD-BAABAABAABAA''>;
<newBalance>;0</newBalance>;
<amount>;5</amount>; 
<overdrawn>;true</overdrawn>;
</IBank:withdrawResponse>;
</soap:Body>;
</soap:Envelope>;
注意[in, out]参数出现在两个消息中。在检查了请求和响应对象的格式后，你可能已经注意到序列化格式通常是： 
<t:typename xmlns:t=''namespaceuri''>;
<fieldname1>;field1value</fieldname1>;
<fieldname2>;field2value</fieldname2>;
......
</t:typename>; 
在请求的情况下，类型是隐式的C风格的结构，它由对应方法中的[in]和[in, out]参数组成。对响应来说，类型也是隐式的C风格的结构，它由对应方法中的[out]和[in, out]参数组成。这种每个域对应一个子元素的风格有时被称为元素正规格式(ENF)。一般情况下，SOAP只用XML特性来传达描述包含在元素内容中信息的注释。
象DCOM和IIOP一样，SOAP支持协议头扩展。SOAP用可选的<Header>;元素来传载被协议扩展所使用的信息。如果客户端的 SOAP软件包含要发送头信息，原始的请求将可能如图9所示。在这种情况下命名causality的头将与请求一起序列化。收到请求后，服务器端软件能查看头的名域URI，并处理它识别出的头扩展。这个头扩展被http://comstuff.com URI识别，并期待一个如下的对象：
struct causality { 
UUID id; 
}; 
在这种情况下的请求，如果头元素的URI不能被识别，头元素可以被安全地忽略。
但你不能安全的忽略所有的SOAP体中的头元素。如果一个特定的SOAP头对正确处理消息是很关键的，这个头元素能被用SOAP属性 mustUnderstand=&rsquo;true&rsquo;标记为必须的。这个属性告诉接收者头元素必须被识别并被处理以确保正确的使用。为了强迫前面 causality头成为一个必须的头，消息将被写成如下形式：
<soap:Envelope xmlns:soap=''urn:schemas-xmlsoap-org:soap.v1''>;
<soap:Header>;
<causality soap:mustUnderstand=''true''xmlns="http://comstuff.com">;
<id>;362099cc-aa46-bae2-5110-99aac9823bff</id>;
</causality>; 
</soap:Header>;
</soap:Envelope>;
SOAP软件遇到不能识别必须的头元素情况时，必须拒绝这个消息并出示一个错误。如果服务器在一个SOAP请求中发现一个不能识别的必须的头元素，它必须返回一个错误响应并且不发送任何调用到目标对象。如果客户端在一个SOAP请求中发现一个不能识别出的必须的头元素，它必须向调用者返回一个运行时错误。在COM情况下，这将映射为一个明显的HRESULT。

第三节 SOAP数据类型
在SOAP消息中，每个元素可能是一个SOAP结构元素、根元素、存取元素或一个独立的元素。在SOAP中，soap:Envelope、soap:Body和soap:Header是唯一的组成元素。它们的基本关系由下列XML Schema所描述: 
<schema targetNamespace=''urn:schemas-xmlsoap-org:soap.v1''>;
<element name=''Envelope''>;
<type>;
<element name=''Header'' type=''Header'' minOccurs=''0'' />;
<element name=''Body'' type=''Body''minOccurs=''1'' />;
</type>;
</element>;
</schema>;
在SOAP元素的四种类型中，除了结构元素外都被用作表达类型的实例或对一个类型实例的引用。
根元素是显著的元素，它是soap:Body 或是 soap:Header的直接的子元素。其中soap: Body只有一个根元素，它表达调用、响应或错误对象。这个根元素必须是soap:Body的第一个子元素，它的标记名和域名URI必须与HTTP SOAPMethodName头或在错误消息情况下的soap:Fault相对应。而soap:Header元素有多个根元素，与消息相联系的每个头扩展对应一个。这些根元素必须是soap:Header的直接子元素，它们的标记名和名域URI表示当前存在扩展数据的类型。
存取元素被用作表达类型的域、属性或数据成员。一个给定类型的域在它的SOAP表达将只有一个存取元素。存取元素的标记名对应于类型的域名。考虑下列Java 类定义：
package com.bofsoap.IBank; 
public class adjustment { 
public int account ;
public float amount ;
}
在一个SOAP消息中被序列化的实例如下所示：
<t:adjustment xmlns:t=''urn:develop-com:java:com.bofsoap.IBank''>;
<account>;3514</account>;
<amount>;100.0</amount>;
</t:adjustment>;
在这个例子中，存取元素account和amount被称着简单存取元素。对引用简单类型的存取元素，元素值被简单地编码为直接在存取元素下的字符数据，如上所示。对引用组合类型的存取元素（就是那些自身用子存取元素来构造的存取元素），有两个技术来对存取元素进行编码。最简单的方法是把被结构化的值直接嵌入在存取元素下。考虑下面的Java类定义：
package com.bofsoap.IBank;
public class transfer {
public adjustment from;
public adjustment to; 
}
如果用嵌入值编码存取元素，在SOAP中一个序列化的transfer对象如下所示：
<t:transfer xmlns:t=''urn:develop-com:java:com.bofsoap.IBank''>;
<from>;
<account>;3514</account>;
<amount>;-100.0</amount>;
</from>;
<to>;
<account>;3518</account>;
<amount>;100.0</amount>;
</to>;
</t:transfer>;
在这种情况下，adjustment对象的值被直接编码在它们的存取元素下。在考虑组合存取元素时，需要说明几个问题。先考虑上面的transfer类。类的from和to的域是对象引用，它可能为空。SOAP用XML Schemas的null属性来表示空值或引用。下面例子表示一个序列化的transfer对象，它的from域是空的：
<t:transfer xmlns:t=''urn:develop-com:java:com.bofsoap.IBank'' 
xmlns<img src="http://bbs.chinaunix.net/images/smilies/icon_mad.gif" align="middle" border="0" alt="" />sd=''http://www.w3.org/1999/XMLSchema/instance''>;
<from xsd:null=''true'' />;
<to>;
<account>;3518</account>;
<amount>;100.0</amount>; 
</to>; 
</t:transfer>;
在不存在的情况下， xsd:null属性的隐含值是false。给定元素的能否为空的属性是由XML Schema定义来控制的。例如下列XML Schema将只允许from存取元素为空：
<type name=''transfer'' >;
<element name=''from'' type=''adjustment'' nullable=''true'' />;
<element name=''to'' type=''adjustment'' nullable=''false''/>;
</type>;
在一个元素的Schema声明中如果没有nullable属性，就意味着在一个XML文档中的元素是不能为空的。Null存取元素的精确格式当前还在修订中&#0;要了解用更多信息参考最新版本的SOAP规范。
与存取元素相关的另一个问题是由于类型关系引起的可代换性。由于前面的adjustment类不是一个final类型的类，transfer对象的 from和to域实际引用继承类型的实例是可能的。为了支持这种类型兼容的替换，SOAP使用一个名域限定的类型属性的XML Schema约定。这种类型属性的值是一个对元素具体的类型的限制的名字。考虑下面的adjustment扩展类：
package com.bofsoap.IBank;
public class auditedadjustment extends adjustment {
public int auditlevel;
}
给出下面Java语言：
transfer xfer = new transfer();
xfer.from = new auditedadjustment();
xfer.from.account = 3514; 
xfer.from.amount = -100;
xfer.from.auditlevel = 3;
xfer.to = new adjustment();
xfer.to.account = 3518; 
xfer.from.amount = 100;
在SOAP中transfer对象的序列化形式如下所示：
<t:transfer xmlns<img src="http://bbs.chinaunix.net/images/smilies/icon_mad.gif" align="middle" border="0" alt="" />sd=''http://www.w3.org/1999/XMLSchema''
xmlns:t=''urn:develop-com:java:com.bofsoap.IBank''>;
<from xsd:type=''t:auditedadjustment'' >;
<account>;3514</account>;
<amount>;-100.0</amount>;
<auditlevel>;3</auditlevel >;
</from>;
<to>;
<account>;3518</account>;
<amount>;100.0</amount>;
</to>;
</t:transfer>;
在这里xsd:type属性引用一个名域限定的类型名，它能被反序列化程序用于实例化对象的正确类型。因为to存取元素引用到一个被预料的类型的实例（而不是一个可代替的继承类型），xsd:type属性是不需要的。
刚才的transfer类设法回避了一个关键问题。如果正被序列化的transfer对象用下面这种方式初始化将会发生什么情况：
transfer xfer = new transfer();
xfer.from = new adjustment();
xfer.from.account = 3514; xfer.from.amount = -100;
xfer.to = xfer.from;
基于以前的议论，在SOAP 中transfer对象的序列化形式如下所示：
<t:transfer xmlns:t=''urn:develop-com:java:com.bofsoap.IBank''>;
<from>;
<account>;3514</account>;
<amount>;-100.0</amount>;
</from>;
<to>;
<account>;3514</account>;
<amount>;-100.0</amount>;
</to>;
</t:transfer>;
这个表达有两个问题。首先最容易理解的问题是同样的信息被发送了两次,这导致了一个比实际所需要消息的更大的消息。一个更微妙的但是更重要的问题是由于反序列化程序不能分辨两个带有同样值的adjustment对象与在两个地方被引用的一个单一的adjustment对象的区别，两个存取元素间的身份关系就被丢失。如果这个消息接收者已经在结果对象上执行了下面的测试，(xfer.to == xfer.from)将不会返回true。
void processTransfer(transfer xfer) {
if (xfer.to == xfer.from)
handleDoubleAdjustment(xfer.to);
else 
handleAdjustments(xfer.to, xfer.from);
}
为了支持必须保持身份关系的类型的序列化，SOAP支持多引用存取元素。目前我们接触到的存取元素是单引用存取元素，也就是说，元素值是嵌入在存取元素下面的，而且其它存取元素被允许引用那个值（这很类似于在NDR中的[unique]的概念）。多引用存取元素总是被编码为只包含已知的soap:href 属性的空元素。soap:href属性总是包含一个代码片段标识符，它对应于存取元素引用到的实例。如果to和from存取元素已经被编码为多引用存取元素，序列化的transfer对象如下所示：
<t:transfer xmlns:t=''urn:develop-com:java:com.bofsoap.IBank''>; 
<from soap:href=''#id1'' />; 
<to soap:href=''#id1'' />; 
</t:transfer>;
这个编码假设与adjustment类兼容的一个类型的实例已经在envelope中的其它地方被序列化，而且这个实例已经被用soap:id属性标记，如下所示：
<t:adjustment soap:id=''id1''xmlns:t=''urn:develop-com:java:com.bofsoap.IBank''>;
<account>;3514</account>;
<amount>;-100.0</amount>;
</t:adjustment>;

第四节 结语
一个遗留的HTTP问题还需要进一步阐明。SOAP支持(但不需要)HTTP扩展框架约定来指定必须的HTTP头扩展。这些约定主要有两个目的。首先，它们允许任意的URI被用于限定给定的HTTP头的范围(类似XML名域)。第二,这些约定允许把必须的头与可选的头区分开来(象soap: mustUnderstand)。下面是一个使用HTTP扩展框架来把SOAPMethodName头定义成为一个必须的头扩展：
M-POST /foobar HTTP/1.1 
Host: 209.110.197.2 
Man: "urn:schemas-xmlsoap-org:soap.v1; ns=42" 
42-SOAPMethodName: urn:bobnsid:IFoo#DoIt 
Man头映射SOAP URI到前缀为42的头，并表示没有认出SOAP的服务器必须返回一个HTTP错误，状态代码为501 (没有被实现) 或 510 (没有被扩展)。HTTP方法必须是M-POST，表明目前是必须的头扩展。SOAP是一个被类型化的序列化格式，它恰巧用HTTP 作为请求/响应消息传输协议。SOAP被设计为与正将出现的XML Schema规范密切配合，并支持在Internet的任何地方运行的COM、CORBA、Perl、Tcl、和Java、C、Python或 PHP等程序间的互操作性。</span>
