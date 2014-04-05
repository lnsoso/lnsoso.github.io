---
layout: post
title: SOA and Web services 新手入门
author: Alvin
date: !binary |-
  MjAwNi0xMC0yNCAxOToyOToyNCArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMC0yNCAxMToyOToyNCArMDgwMA==
---
最近因为工作需要，需要具体了解一些 SOA、SOAP 等相关概念。先看 SOA 吧，一步步来，转自 IBM。
<table cellspacing="0" cellpadding="0" width="100%" border="0">
<tbody>
<tr valign="top">
<td width="16"><img height="16" alt="" src="http://www.ibm.com/i/v14/icons/d_bold.gif" width="16" /></td>
<td width="100%"><a class="fbox" href="http://www-128.ibm.com/developerworks/cn/webservices/newto/index.html#1"><font color="#5c81a7">什么是 SOA？</font></a></td>        </tr>
<tr valign="top">
<td width="16"><font color="#5c81a7"><img height="16" alt="" src="http://www.ibm.com/i/v14/icons/d_bold.gif" width="16" /></font></td>
<td width="100%"><a class="fbox" href="http://www-128.ibm.com/developerworks/cn/webservices/newto/index.html#2"><font color="#5c81a7">体系结构</font></a></td>        </tr>
<tr valign="top">
<td width="16"><font color="#5c81a7"><img height="16" alt="" src="http://www.ibm.com/i/v14/icons/d_bold.gif" width="16" /></font></td>
<td width="100%"><a class="fbox" href="http://www-128.ibm.com/developerworks/cn/webservices/newto/index.html#3"><font color="#5c81a7">SOA 生命周期</font></a></td>        </tr>
<tr valign="top">
<td width="16"><font color="#5c81a7"><img height="16" alt="" src="http://www.ibm.com/i/v14/icons/d_bold.gif" width="16" /></font></td>
<td width="100%"><a class="fbox" href="http://www-128.ibm.com/developerworks/cn/webservices/newto/index.html#4"><font color="#5c81a7">SOA 采用阶段：您可能已经开始了</font></a></td>        </tr>
<tr valign="top">
<td width="16"><font color="#5c81a7"><img height="16" alt="" src="http://www.ibm.com/i/v14/icons/d_bold.gif" width="16" /></font></td>
<td width="100%"><a class="fbox" href="http://www-128.ibm.com/developerworks/cn/webservices/newto/index.html#5"><font color="#5c81a7">辅助工具</font></a></td>        </tr>    </tbody></table><font color="#5c81a7"><img height="5" alt="" src="http://www.ibm.com/i/c.gif" width="1" />
<!-- <-->
<img height="1" alt="" src="http://www.ibm.com/i/c.gif" width="443" />
<!-- ANCHOR_LINK_LIST_END --><!-- Spacer -->
<!-- INTRODUCTION BEGIN --></font>
<table cellspacing="0" cellpadding="1" width="100%" border="0">
<tbody>
<tr>
<td class="v14-header-2"><a name="">引言：使 IT 满足您的业务需求</a></td>        </tr>
<tr>
<td>
或许已经有人告诉您，您公司的新 IT 策略将要涉及到创建一个基于面向服务的体系结构的系统。也许您已经听到了大量的长篇大论，正想知道面向服务的体系结构（Service-Oriented Architecture，SOA）是否适合您的业务。或许您正在经历一场集成噩梦，尝试寻找让很多不同的系统彼此进行通信的方法。不管是何种情况，您都可能希望找到让 IT 基础设施为业务服务的方法，而不是其他。无论您是刚刚接触面向服务的体系结构这一概念，还是已经涉足其中，您肯定都希望找到方法来提高实现的效率，“developerWorks SOA 新手入门” 部分将为您提供了解和着手使用 SOA 所需的资源。            </td>        </tr>
<tr valign="top">
<td><img height="16" alt="" src="http://www-128.ibm.com/developerworks/i/c.gif" width="8" /></td>        </tr>    </tbody></table><!-- INTRODUCTION END --><!-- Spacer --><!-- BACK_LINK -->
<!-- BACK_LINK_END --><!-- LINK_#1_BEGIN-->
<table cellspacing="0" cellpadding="0" width="100%" border="0">
<tbody>
<tr>
<td class="v14-header-2"><a name="1">什么是 SOA？</a></td>        </tr>
<tr>
<td>
我们可能应该回答的第一个问题也是最基本的问题。什么是面向服务的体系结构(Service-Oriented Architecture, SOA)？这个问题的答案实际上涉及与开发相关的若干不同方面。
SOA 是一种 IT 体系结构样式，支持将您的业务作为链接服务或可重复业务任务进行集成，可在需要时通过网络访问这些服务和任务。这个网络可能完全包含在您的公司总部内，也可能分散于各地且采用不同的技术，通过对来自纽约、伦敦和香港的服务进行组合，可让最终用户感觉似乎这些服务就安装在本地桌面上一样。需要时，这些服务可以将自己组装为按需应用程序&mdash;&mdash;即相互连接的服务提供者和使用者集合，彼此结合以完成特定业务任务，使您的业务能够适应不断变化的情况和需求（在有些情况下，甚至不需要人工干预）。
这些服务是自包含的，具有定义良好的接口，允许这些服务的用户&mdash;&mdash;称为客户机或使用者&mdash;&mdash;了解如何与其进行交互。从技术角度而言，SOA 带来了“松散耦合”的应用程序组件，在此类组件中，代码不一定绑定到某个特定的数据库（甚至不一定绑定到特定的基础设施）。正是得益于这个松散耦合特性，才使得能够将服务组合为各种应用程序。这样还大幅度提高了代码重用率，可以在增加功能的同时减少工作量。由于服务和访问服务的客户机并未彼此绑定，因此可以完全替换用于处理订单的服务，下订单的客户机-服务将永远不会知道这个更改。所有交互都是基于“服务契约”进行的；服务契约用于定义服务提供者和客户机之间的交互。通常，您将通过创建“基于消息的”系统来实现此目标。
从业务的角度来说，面向服务的体系结构的重点在于开发能帮助您完成业务任务的技术，而不是通过技术约束来规定您的行动。例如，销售过程（制造、运输和收到货款）可能会涉及数十个步骤和若干不同的数据库和计算机系统。但就其实质而言，此过程包含一系列人工活动，例如：
<ul>
<li>销售人员找到潜在客户 </li>
<li>客户订购产品 </li>
<li>生产部门制造产品 </li>
<li>生产部门发出产品 </li>
<li>收款部门开具产品帐单 </li>
<li>客户支付产品货款 </li>            </ul>
面向服务的体系结构基于这些实际活动或业务服务进行组织，而不是形成公司所维护的不同的信息竖井 (Silo)。
通过实现 SOA，可以带来大量好处，包括以下各个方面：
<ul>
<li>更高的业务和 IT 一致性 </li>
<li>基于组件的系统 </li>
<li>松散耦合的组件和系统 </li>
<li>基于网络的基础设施，允许分散于各地且采用不同技术的资源协同工作 </li>
<li>动态构建的按需应用程序 </li>
<li>更高的代码重用率 </li>
<li>更好地标准化整个企业内的流程 </li>
<li>更易于集中企业控制 </li>            </ul>
下面提供了一些有用的资源，可帮助您了解面向服务的体系结构的总体概念：
<ul>
<li><a href="http://www.ibm.com/soa/"><font color="#5c81a7">IBM vision of Service-Oriented Architecture</font></a>&mdash;&mdash;了解 SOA 如何帮助您应对当今不断变化的业务环境中固有的业务挑战和 IT 需求 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-migratesoa/"><font color="#5c81a7">迁移到面向服务的体系结构，第 1 部分</font></a> 和 <a href="http://www.ibm.com/developerworks/cn/webservices/ws-migratesoa2/"><font color="#5c81a7">第 2 部分</font></a>&mdash;&mdash;制订实际计划，评估当前的基础设施，并将其迁移到真正的 SOA </li>            </ul>            </td>        </tr>    </tbody></table><!-- LINK_#1_END--><!-- BACK_LINK -->
<!-- BACK_LINK_END --><!-- LINK_#2_BEGIN-->
<table cellspacing="0" cellpadding="0" width="100%" border="0">
<tbody>
<tr>
<td class="v14-header-2"><a name="2">体系结构</a></td>        </tr>
<tr>
<td>
Web 服务是用于实现 SOA 的最常见技术标准。不过，这并不是可以用于开发 SOA 的各个部分的唯一技术。很多 SOA&mdash;&mdash;实际上是大部分&mdash;&mdash;都涉及到集成遗留数据，此类数据包含在使用 <a href="http://www-306.ibm.com/software/integration/wmq/"><font color="#5c81a7">MQSeries</font></a> 和 <a href="http://www.omg.org/gettingstarted/corbafaq.htm"><font color="#5c81a7">Common Object Request Broker Architecture</font></a> (CORBA) 等技术的系统中。其中的许多技术都已针对 SOA 进行了调整，不管有无 Web 服务包装均可使用。事实上，可以仅使用 MQSeries、CORBA 甚至<a href="http://www.xmlrpc.com/"><font color="#5c81a7">远程过程调用</font></a>（Remote Procedure Call，RPC）技术来实现 SOA。但 Web 服务正迅速成为用于支持 SOA 的事实标准。
下面提供了一些有用的资源，可帮助您很好地理解用于实现 SOA 的一些可能方法。            <strong>Web 服务</strong>
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/views/webservices/articles.jsp?view_by=search&search_by=Web+%E6%9C%8D%E5%8A%A1%E6%A6%82%E5%BF%B5%E6%80%A7%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84"><font color="#5c81a7">Web Services Conceptual Architecture</font></a>&mdash;&mdash;解释了 Web 服务背后的技术思想及其工作方式。 （<font color="#5c81a7">英文原文</font>） </li>
<li><a href="http://www.ibm.com/developerworks/webservices/standards/"><font color="#5c81a7">标准发展路线</font></a>&mdash;&mdash;了解标准和规范对于 SOA 和 Web 服务开发工作的影响和重要性。 </li>
<li><a href="http://www.ibm.com/developerworks/views/webservices/libraryview.jsp?type_by=Standards"><font color="#5c81a7">Web 服务标准和规范</font></a>&mdash;&mdash;了解所有 Web 服务规范和协议标准 </li>            </ul>            <strong>CORBA</strong>
<ul>
<li><a href="http://www.ibm.com/developerworks/webservices/library/ws-exploring-corba/"><font color="#5c81a7">Exploring the range of CORBA technology</font></a>&mdash;&mdash;了解如何使用 CORBA 技术创建简单的分布式应用程序 </li>            </ul>            <strong>MQSeries</strong>
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/websphere/library/techarticles/0202_balani/balani.html"><font color="#5c81a7">用 VisualAge For Java 进行 MQSeries 编程</font></a>&mdash;&mdash;此教程可帮助您了解基本的 MQSeries 消息传递与触发概念 </li>            </ul>            <strong>RPC</strong>
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-xpc2/"><font color="#5c81a7">用 XML-RPC 开发 Web 服务: XML-RPC 中间件</font></a>&mdash;&mdash;此教程可帮助您了解如何使用 XML-RPC 来在分布式环境中实现远程过程调用操作。 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-tip-j2eenet1/index.html"><font color="#5c81a7">Web 服务编程技巧与窍门: 提高 J2EE 技术和 .NET 之间的互操作性</font></a>&mdash;&mdash;此教程讨论了在将 RPC 样式的交互构建到 SOA 基础设施中时需要考虑的一些问题。 </li>            </ul>            <strong>其他技术</strong>
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-odbp11/index.html"><font color="#5c81a7">随需应变的业务流程生命周期，第 11 部分</font></a>&mdash;&mdash;将业务流程与 CICS 事务服务器集成 </li>
<li><a href="http://www.ibm.com/developerworks/cn/db2/library/techarticles/dm-0507klein/index.html"><font color="#5c81a7">IMS 随需应变面向服务体系架构的工具和解决方案</font></a>&mdash;&mdash;将现有资产扩展到按需体系结构中 </li>            </ul>            </td>        </tr>    </tbody></table><!-- LINK_#2_END--><!-- BACK_LINK -->
<!-- BACK_LINK_END --><!-- LINK_#3_BEGIN-->
<table cellspacing="0" cellpadding="0" width="100%" border="0">
<tbody>
<tr>
<td class="v14-header-2"><a name="3">SOA 生命周期</a></td>        </tr>
<tr>
<td>
由于 SOA 涉及到业务的诸多方面，因此需要从一开始就对 SOA 项目进行细心的规划和设计。您需要考虑项目的整个生命周期，从最初的阶段到第一个实现，再一直到可能的修订和重用。
现在让我们看看 SOA 生命周期，如图 1 中所示。此部分概略说明了在生命周期的各个阶段发生的事项，并详细介绍了实现生命周期的各个步骤。
<figure></figure>            <heading refname="" type="figure">            </heading>            <strong>图 1. SOA 生命周期</strong> <img height="291" alt="图 1. SOA 生命周期" src="http://www-128.ibm.com/developerworks/cn/webservices/newto/figure1-0506.jpg" width="400" /> <!--            <heading alttoc="" refname="" type="minor">            Model            </heading>            -->
            
            <strong>建模</strong>
面向服务的体系结构项目的第一步几乎和技术没有任何关系，所有事项都与您的业务相关。请记住，面向服务的方法将业务所执行的活动视为服务，因此第一步是要确定这些业务活动或流程实际是什么。对您的业务体系结构进行记录，这些记录不仅可以用于规划 SOA，还可以用于对实际业务流程进行优化。通过在编写代码前模拟或建模业务流程，您可以更深入地了解这些流程，从而有利于构建帮助执行这些流程的软件。
建模业务流程的程度将依赖于预期实现的深度。另外，这个程度还依赖于您在开发团队中担任的角色。如果您是企业架构师，您将会对实际的业务服务进行建模。如果您是软件开发人员，您将可能对单个服务进行建模。下面提供了一些有用的资源，可帮助您更有效地对业务和应用程序进行建模。
<ul>
<li><a href="http://www.ibm.com/soa/assessment"><font color="#5c81a7">IBM SOA Self-Assessment</font></a>&mdash;&mdash;这个在线评估工具可帮助您在开始着手时确定哪些项目所带来的好处最多。 </li>
<li><a href="http://www.ibm.com/developerworks/cn/rational/r-mda/index.html"><font color="#5c81a7">模型驱动体系架构介绍 &mdash; 第一部分: MDA 和当今的系统 </font></a>&mdash;&mdash;熟悉这种软件开发理念。 </li>
<li><a href="http://www-306.ibm.com/software/rational/mda/"><font color="#5c81a7">The Model Driven Architecture Information Center</font></a>&mdash;&mdash;详细了解 IBM 提供的用于业务应用程序建模和 MDA 支持的产品，并获得相关的学习资源。 </li>
<li><font color="#5c81a7">标准建模工具</font>&mdash;&mdash;了解什么工具适合您手边正在进行的任务。 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-uml2bpel/"><font color="#5c81a7">从UML到BPEL</font></a>&mdash;&mdash;Web 服务世界中的模型驱动的体系结构 </li>            </ul>            <!--            <heading alttoc="" refname="" type="minor">            Assemble            </heading>            -->
            <strong>组装</strong>
对业务流程进行了建模和优化后，开发人员可以开始构建新的服务和/或重用现有的服务，然后对其进行组装以形成组合应用程序，从而实现这些流程。在“建模”步骤中，您已经确定了需要何种类型的服务以及它们将访问何种类型的数据。已经存在某种形式的实现这些服务或访问该类数据所需的一些软件。“组装”步骤将要找到已经存在的功能，并为其添加服务支持。另外，还涉及到创建提供功能和访问数据源所需的新服务，以便满足您的 SOA 涉及的业务流程范围内的需求。
下面提供了一些有用的资源，可帮助您进行此步骤。
<ul>
<li><a href="http://www.ibm.com/developerworks/websphere/library/demos/0310_WSdemo.html"><font color="#5c81a7">Web Services Demos with WebSphere Studio</font></a>&mdash;&mdash;对 Web 服务和 SOA 的相关概念和技术结构进行了很好的介绍 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-sdoarch/index.html"><font color="#5c81a7">利用服务数据对象体系结构简化和统一数据</font></a>&mdash;&mdash;了解服务数据对象（Service Data Object，SDO）体系结构的主要概念及其提供的强大功能和灵活性 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-buildebay/"><font color="#5c81a7">利用 eBay SDK 和 Web 服务构建网上市场，第 1 部分</font></a> </li>
<li><a href="http://www.ibm.com/developerworks/cn/websphere/library/techarticles/0512_lee/0512_lee.html"><font color="#5c81a7">将 iSeries Web 服务导入 WebSphere Integration Developer</font></a>&mdash;&mdash;了解如何将 WebSphere Development Studio Client for iSeries 生成的 iSeries Web 服务导入到 WebSphere Integration Developer 服务组件中 </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Deploy            </heading>            --><strong>部署</strong>
进行了建模和组装后，要将组成 SOA 的资产部署到安全的集成环境中。此环境本身提供专门化的服务，用于集成业务中涉及的人员、流程和信息。这种级别的集成可帮助确保将公司的所有主要元素连接到一起协同工作。此外，部署工作还需要满足业务的性能和可用性需求，并提供足够的灵活性，以便吸纳新服务（并使旧服务退役），而不会对整个系统造成大的影响。
下面提供了一些有用的资源，可帮助您了解如何进行此步骤。
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-migrate2soa/index.html"><font color="#5c81a7">开发从遗留的企业 IT 基础架构到基于 SOA 的企业架构的移植策略</font></a> </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-progmodel7/index.html"><font color="#5c81a7">用于实现 Web 服务的 SOA 编程模型，第 7 部分: 保护面向服务的应用程序</font></a> </li>
<li><a href="http://www.ibm.com/developerworks/cn/websphere/library/techarticles/0512_flurry/0512_flurry.html"><font color="#5c81a7">将非 SOAP HTTP 请求程序和提供程序连接到 WebSphere Application Server V6 企业服务总线</font></a>&mdash;&mdash;让请求者和提供者利用企业服务总线提供的集成功能 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-enter6/index.html"><font color="#5c81a7">在企业级 SOA 中使用 Web 服务，第 6 部分</font></a>&mdash;&mdash;使用 WebSphere Application Server 对 Web 服务应用程序的负载进行平衡 </li>
<li><a href="http://www-306.ibm.com/software/tivoli/features/it-serv-mgmt/"><font color="#5c81a7">IT Services Management</font></a>&mdash;&mdash;一种更好地管理 IT 的业务的方法 </li>            </ul>            <!--            <heading alttoc="" refname="" type="minor">            Manage            </heading>            -->
            <strong>管理</strong>
系统就位，一切都正常运行。 现在您可以对一切放手不管了，对吗？不对。部署后，需要从 IT 和业务两个角度对您的系统进行管理和监视。在“管理”步骤中收集的信息用于帮助实时地了解业务流程，从而能更好地进行业务决策，并将信息反馈回生命周期，以进行持续的流程改进工作。您将需要处理服务质量、安全、一般系统管理之类的问题。 
在本步骤中，您将监视和优化系统，发现和纠正效率低下的情况和存在的问题。由于 SOA 是一个迭代过程，因此，在此步骤中，您不仅要找出技术体系结构中有待改进之处，而且还要找出业务体系结构中有待改进之处。
完成此步骤后就要开始新的“建模”步骤了。在“管理”步骤中收集的数据将用于重复整个 SOA 生命周期，再次进行整个过程。
下面提供了一些有用的资源，可帮助您进行 SOA 开发的“管理”步骤：
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-enter6/index.html"><font color="#5c81a7">在企业级 SOA 中使用 Web 服务，第 6 部分</font></a>&mdash;&mdash;使用 WebSphere Application Server 对 Web 服务应用程序的负载进行平衡 </li>
<li><a href="http://www.ibm.com/developerworks/tivoli/library/t-swstam/index.html"><font color="#5c81a7">Securing Web Services with Tivoli Access Manager</font></a>&mdash;&mdash;可保证用于内部和 B2B 应用程序集成的 Web 服务安全的解决方案 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-sla4/"><font color="#5c81a7">在 Web 服务上下文中使用 SLA，第 4 部分: 利用 SLA 来保护多个 Web 服务</font></a>&mdash;&mdash;测试异类 SOA 中的 ACL </li>
<li><a href="http://www.ibm.com/software/integration/wbimonitor/"><font color="#5c81a7">IBM WebSphere Business Monitor</font></a>&mdash;&mdash;实时监视业务流程 </li>
<li><a href="http://www.ibm.com/software/tivoli/products/composite-application-mgr-soa/"><font color="#5c81a7">IBM Tivoli Composite Application Manager for SOA</font></a>&mdash;&mdash;管理和控制 IT 体系结构的 Web 服务层 </li>            </ul>            <!--            <heading alttoc="" refname="" type="minor">            Governance            </heading>            -->
            <strong>控制</strong>
SOA 是一种集中系统；其中可以包含来自组织的不同部门的服务，甚至还能包含来自组织外的服务。如果没有恰当的控制，这种系统很容易失控。 
控制对所有生命周期阶段起到巩固支撑作用，为整个 SOA 系统提供指导，并有助于了解系统全貌。它提供指导和控制，帮助服务提供者和使用者避免遇到意外情况。下面提供了一些有用的资源，可帮助您了解如何控制和建立自己的控制方案。
<ul>
<li><a href="http://www.ibm.com/software/solutions/soa/gov/lifecycle/"><font color="#5c81a7">IBM SOA Governance</font></a>&mdash;&mdash;其中概略讨论了如何在整个生命周期中应用控制，介绍了各种控制方法，并帮助您应对在建立自己的控制的过程中可能遇到的挑战。其中还介绍了可为您提供帮助的产品和服务。 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ar-itio5/"><font color="#5c81a7">什么是 IT 管理，为什么应该对其加以注意？</font></a>&mdash;&mdash;在“观点与展望”系列的这篇文章里，IT 技术带头人将告诉您为什么 IT 控制非常重要，以及如何开始实现您自己的相关计划。 </li>
<li><a href="http://www-128.ibm.com/developerworks/cn/webservices/ar-servgov/"><font color="#5c81a7">SOA 管理介绍</font></a>&mdash;&mdash;介绍了 IBM 对 SOA 控制的正式定义，还说明了其为何重要的原因。 </li>            </ul>            </td>        </tr>    </tbody></table><!-- LINK_#3_END--><!-- BACK_LINK -->
<!-- BACK_LINK_END --><!-- LINK_#4_BEGIN-->
<table cellspacing="0" cellpadding="0" width="100%" border="0">
<tbody>
<tr>
<td class="v14-header-2"><a name="4">SOA 采用阶段：您可能已经开始了</a></td>        </tr>
<tr>
<td>
已经向您介绍了面向服务的体系结构和 SOA 开发的步骤，您现在可能已经确信应该开始构建自己的 SOA 了。如果您已经构建了基于 Web 的软件服务，则已经达到了 SOA 采用的第一个阶段。在此部分，我们将分析各个采用阶段（从偶然构建服务到基于面向服务的体系结构原则对业务进行全面转换），从而帮助您了解自己目前所处的位置以及确定需要实现的目标。             <!--            <heading alttoc="" refname="" type="minor">            The phases of SOA maturity            </heading>            --><strong>SOA 成熟阶段</strong>
您不大可能立即基于 SOA 进行全面的转换。事实上，孤注一掷的方法会增加失败的风险。应该转而采用迭代的方法逐步通过各个采用阶段，首先开发少数试点项目服务，然后逐步将您的 IT 系统更新为在 SOA 内工作的服务。我们将讨论以下 SOA 采用阶段：
<ul>
<li>构建服务 </li>
<li>集成 </li>
<li>转换 IT </li>
<li>转换业务 </li>            </ul>            <!--            <heading alttoc="" refname="" type="minor">            Build services:  As needed services with ad hoc linkage            </heading>            -->
            <strong>构建服务：具有特殊连接的根据需要提供的服务</strong>
在 SOA 采用第一个阶段，公司通常会很偶然地着手构建 SOA 服务。也就是说，由于需要解决特定的问题，他们选择了面向服务的方法，而不使用传统方法。在此阶段，服务构建将更多地关注解决特定的问题，而不是对企业现有系统进行转换。IT 部门将构建一些新服务，或许会将一些现有应用程序转换为一组基于 Web 的服务。它们之间的链接将根据需要提供，而不是源自整个体系结构的要求。
下面提供了一些有用的资源，可帮助您更有效地设计和构建服务。
<ul>
<li><font color="#5c81a7">Level 1 SOA Adoptions</font>&mdash;&mdash;实现各个 Web 服务 </li>
<li><font color="#5c81a7">在不使用 IDE 的情况下开发 Web 服务，第 1 部分: 以服务器为中心</font>&mdash;&mdash;在命令行创建 Web 服务提供程序 </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Integrate: Systematic standardized service interfaces with robust connectivity            </heading>            --><strong>集成：具有可靠连接的系统标准化服务接口</strong>
发现了松散耦合体系结构的优势、方便性和易维护性后，下一步就是利用这种灵活性通过组合服务来创建新的组合应用程序。例如，员工状态服务可以与经理审批服务组合，以形成请假服务。这个过程可以采用自顶向下的方法，将重点放在最终结果和查找组件组成部分上。或者，可以采用自底向上的方法，将重点放在各个组成部分上，看看可以基于这些组成部分构建何种服务。他们之间的链接是预先计划的且定义良好。
下面提供了一些有用的资源，可帮助您到达这个 SOA 采用阶段。
<ul>
<li><font color="#5c81a7">Level 2 SOA Adoption</font>&mdash;&mdash;面向服务的集成 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-enter1/"><font color="#5c81a7">在企业级 SOA 中使用 Web 服务，第 1 部分</font></a>&mdash;&mdash;弥合多 SOA 之间的企业系统差距 </li>
<li><a href="http://www.ibm.com/developerworks/webservices/library/ws-tip-altdesign4/"><font color="#5c81a7">Web 服务编程度技巧和窍门: 学习简单、实用的 Web 服务设计模式，第四部分</font></a>&mdash;&mdash;了解和实现消息总线模式 </li>
<li><font color="#5c81a7">用于实现 Web 服务的 SOA 编程模型，第 4 部分</font>&mdash;&mdash;IBM 企业服务总线 (Enterprise Service Bus, ESB) 介绍 </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Transform IT: Composite assemblies of reusable services drawing on functionality from multiple sources            </heading>            --><strong>转换 IT：组合可重用服务，利用多个来源的功能</strong>
这个阶段涉及到对您的信息技术基础设施进行转换，以便充分利用 SOA 的优势。在此阶段，所有系统将转换为基于服务的应用程序，松散耦合是其中的规范做法，而不是例外。系统的所有组件都将根据 SOA 进行集成和连接，IT 系统的所有部分都在 SOA 内工作。
下面提供了一些有用的资源，可帮助您达到此阶段。
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-enter2/"><font color="#5c81a7">在企业级 SOA 中使用 Web 服务，第 2 部分</font></a>&mdash;&mdash;最大程度地提高外部 Web 服务的互操作性 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-enter3/index.html"><font color="#5c81a7">在企业级 SOA 中使用 Web 服务，第 3 部分</font></a>&mdash;&mdash;将您的 SOA 整合为三维集成中心，以提高速度和可靠性 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-soa-progmodel2.html"><font color="#5c81a7">用于实现 Web 服务的 SOA 编程模型，第 2 部分</font></a>&mdash;&mdash;使用服务数据对象简化数据访问 </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Transform the business: Dynamic, event-driven reconfiguration of services            </heading>            --><strong>转换业务：对服务进行动态的事件驱动的重新配置</strong>
在 SOA 成熟的最后一个阶段，业务与 SOA 完全集成，达到了这样一个程度：所有合适的业务活动都被视为服务，可以最终在技术体系结构中对其进行建模、分析和实例化。达到此阶段需要业务部门进行大量的工作和投入；不过，达到此阶段后，业务将从面向服务的体系结构获得最大的回报。
下面提供了一些有用的资源，可帮助您根据 SOA 原则对业务进行转换。
<ul>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-cei/index.html"><font color="#5c81a7">构建 CEI 应用程序用于测试事件选择器和事件群</font></a>&mdash;&mdash;了解如何构建基于公共事件基础设施（Common Event Infrastructure，CEI）的应用程序，以测试事件组和事件选择器 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-odbp8/index.html"><font color="#5c81a7">随需应变业务流程的生命周期，第 8 部分: 业务流程监控</font></a>&mdash;&mdash;创建关键性能指标 </li>
<li><a href="http://www.ibm.com/developerworks/cn/webservices/ws-bdd/index.html"><font color="#5c81a7">业务驱动的开发</font></a>&mdash;&mdash;提供优化业务流程的基础知识，以确保信息系统充分满足企业的业务需求 </li>            </ul>            </td>        </tr>    </tbody></table><!-- LINK_#4_END--><!-- BACK_LINK -->
<!-- BACK_LINK_END --><!-- LINK_#5_BEGIN-->
<table cellspacing="0" cellpadding="0" width="100%" border="0">
<tbody>
<tr>
<td class="v14-header-2"><a name="5">辅助工具</a></td>        </tr>
<tr>
<td>
迁移到面向服务的体系结构的好处诸多，包括提高应用程序易维护性和易开发性，让您能够更容易地使业务和 IT 紧密结合。IBM 提供了全套开发工具，无论您处在 SOA 生命周期和开发阶段中的任何位置，都可帮助您获得这些好处。下面列出了一些工具，可用于更方便地构建、集成和扩展 SOA。            
            <!--            <heading alttoc="" refname="" type="minor">            Model            </heading>            --><strong>建模</strong>
<ul>
<li><a href="http://www.ibm.com/software/integration/wbimodeler/advanced/"><font color="#5c81a7">IBM WebSphere Business Integration Modeler（提供试用版）</font></a> </li>
<li><a href="http://www.ibm.com/software/awdtools/architect/swarchitect/"><font color="#5c81a7">IBM Rational Software Architect（提供试用版）</font></a> </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Assemble            </heading>            --><strong>组装</strong>
<ul>
<li><a href="http://www.ibm.com/software/integration/wid/"><font color="#5c81a7">IBM WebSphere Integration Developer</font></a> </li>
<li><a href="http://www.ibm.com/developerworks/downloads/r/rad/"><font color="#5c81a7">IBM Rational Application Developer（提供试用版）</font></a> </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Deploy            </heading>            --><strong>部署</strong>
<ul>
<li><a href="http://www.ibm.com/software/integration/wps/"><font color="#5c81a7">IBM WebSphere Process Server</font></a> </li>
<li><a href="http://www.ibm.com/software/integration/wbimessagebroker/v6/"><font color="#5c81a7">IBM WebSphere Message Broker</font></a> </li>
<li><a href="http://www.ibm.com/software/integration/wspartnergateway/advanced/"><font color="#5c81a7">IBM WebSphere Partner Gateway</font></a> </li>
<li><a href="http://www.ibm.com/software/genservers/portal/enable/"><font color="#5c81a7">IBM WebSphere Portal</font></a> </li>
<li><a href="http://www.ibm.com/software/pervasive/ws_everyplace_deployment/v6/about/"><font color="#5c81a7">IBM WebSphere Everyplace Deployment</font></a> </li>
<li><a href="http://www.lotus.com/products/product5.nsf/wdocs/workplacehome"><font color="#5c81a7">IBM Workplace Collaboration Services</font></a> </li>
<li><font color="#5c81a7">IBM WebSphere Information Integrator（提供试用版）</font> </li>
<li><a href="http://www.ibm.com/software/webservers/appserv/was/"><font color="#5c81a7">IBM WebSphere Application Server（提供试用版）</font></a> </li>            </ul>            
            <!--            <heading alttoc="" refname="" type="minor">            Manage            </heading>            --><strong>管理</strong>
<ul>
<li><a href="http://www.ibm.com/software/integration/wbimonitor/"><font color="#5c81a7">IBM WebSphere Business Monitor</font></a> </li>
<li><a href="http://www.ibm.com/software/tivoli/products/composite-application-mgr-soa/"><font color="#5c81a7">IBM Tivoli Composite Application Manager for SOA</font></a> </li>
<li><a href="http://www.ibm.com/software/tivoli/products/identity-mgr/"><font color="#5c81a7">IBM Tivoli Identity Manager</font></a> </li>            </ul>            </td>        </tr>    </tbody></table>
