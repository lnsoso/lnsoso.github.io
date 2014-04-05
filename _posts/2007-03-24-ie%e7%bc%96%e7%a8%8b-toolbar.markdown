---
layout: post
title: IE编程 - ToolBar
author: Alvin
date: !binary |-
  MjAwNy0wMy0yNCAyMTozNjoyNSArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wMy0yNCAxMzozNjoyNSArMDgwMA==
---
<strong>关键字</strong>：Band，Desk Band，Explorer Band，Tool Band，浏览器栏，工具栏，桌面工具栏
<strong>一、引言</strong>
　　最近，由于工作的要求，我需要在 IE 上做一些开发工作。于是在 MSDN 上翻阅了一些资料，根据 MSDN 上的说明我用 ATL 胜利完成了“资本家老板”分配的任务。
（并且在白天睡觉的过程中梦到了老板给我加工资啦......）
现在，我把 MSDN 上的原文资料，经过翻译整理并把一个 ATL 的实现奉贤给 VCKBASE 上的朋友们。
<ul>
<li><a href="http://www.vckbase.com/document/viewdoc/?id=1457#二、概念"><font color="#800080">概念</font></a> </li>
<li><a href="http://www.vckbase.com/document/viewdoc/?id=1457#三、原理"><font color="#800080">原理</font></a>
    <a href="http://www.vckbase.com/document/viewdoc/?id=1457#3.1　基本_band_对象"><font color="#800080">基本band 对象</font></a>
    <a href="http://www.vckbase.com/document/viewdoc/?id=1457#3.2　必须实现的_COM_接口"><font color="#800080">必须实现的 COM 接口</font></a>
        <a href="http://www.vckbase.com/document/viewdoc/?id=1457#IPersistStream"><font color="#800080">IPersistStream</font></a>
        <a href="http://www.vckbase.com/document/viewdoc/?id=1457#IObjectWithSite"><font color="#800080">IObjectWithSite</font></a>
        <a href="http://www.vckbase.com/document/viewdoc/?id=1457#IDeskBand"><font color="#800080">IDeskBand、IDockingWindow、IOleWindow</font></a>
    <a href="http://www.vckbase.com/document/viewdoc/?id=1457#3.3　选择实现的_COM_接口"><font color="#800080">选择实现的 COM 接口</font></a>
    <a href="http://www.vckbase.com/document/viewdoc/?id=1457#3.4　Band_对象注册"><font color="#800080">Band 对象注册</font></a> </li>
<li><a href="http://www.vckbase.com/document/viewdoc/?id=1457#四、_ATL_实现"><font color="#800080">ATL 实现</font></a> </li></ul>
<strong><a name="二、概念"><font color="#000000">二、概念</font></a></strong>
　　在翻译的过程中，有两个词汇非常不好理解。第一个词是 Band 对象，词典中翻译为“镶边、裙子边、带子、乐队......”我的英文水平有限，实在不知道应该翻译为什么词汇更合适。于是我毅然决然地决定：在如下的论述中，依然使用 band 这个词！（什么？没听明白？我的意思就是说，我不翻译这个词了）但到底 Band 对象应该如何理解那？请看图一：
<img height="399" src="http://www.vckbase.com/document/journal/vckbase42/images/yfbands1.jpg" width="613" border="0" alt="" />
图一
　　图一中画红圈的地方，分别称作“垂直的浏览器栏”、“水平的浏览器栏”、“工具栏”和“桌面工具栏”。这些“栏”，都可以在 IE 的“查看”菜单中或鼠标右键的上下文快捷方式菜单中显示或隐藏起来。这些界面窗口的实现，其实就是实现一种 COM 接口对象，而这个对象叫 band。这个概念实在是只能意会而无法言传的，我总不能在文章中把它翻译为“总是靠在 IE 主窗口边上的对象”吧？^_^
　　另外，还有一个词叫 site。这个很好翻译，叫“站点”！。呵呵，我敢打包票，如果你要能理解这个翻译在计算机类文章中的含义，那就只能恭喜你了，你的智慧太高了。（都是学计算机软件的人，做人的差距咋就这么大呢？）在本篇文章中，site 可以这样理解：IE 的主框架四周，就好比是“汽车站”，那些 band 对象，就好比是“汽车”。band 汽车总是可以停靠在“汽车站”上。所以，site 就是“站点”，它也是 COM 接口的对象（IObjectWithSite、IInputObjectSite）。
<strong><a name="三、原理"><font color="#000000">三、原理</font></a></strong>
<em><a name="3.1　基本_band_对象"><font color="#000000">3.1　基本 band 对象</font></a></em>
　　Band 对象，从 Shell 4.71(IE 5.0) 开始提供支持。Band 是一个 COM 对象，必须放在一个容器中去使用，当然使用它们就好象使用普通窗口是一样的。IE 就是一个容器，桌面 Shell 也是一个容器，它们提供不同的函数功能，但基本的实现是相似的。
　　Band 对象分三种类型，浏览器栏 band（Explorer bands）、工具栏 band（Tool Bands）和桌面工具栏(Desk bands)，而浏览器栏 band 又有两种表现形式：垂直和水平的。那么 IE 和 Shell 如何区分并加载这些 bands 对象呢？方法是：你要对不同的 band 对象，在注册表中注册不同的组件类型（CATID）。

<table cellspacing="1" width="83%" border="1">
<tbody>
<tr>
<td width="22%">
<p align="center"><strong><span lang="en-us">Band </span>样式</strong>            </td>
<td width="25%">
<p align="center"><strong>组件类型</strong>            </td>
<td width="51%">
<p align="center"><strong>CATID</strong>            </td>        </tr>
<tr>
<td align="center" width="22%">垂直的浏览器栏</td>
<td align="center" width="25%"><font face="宋体" size="3"><span lang="en-us">CATID_InfoBand</span></font></td>
<td align="center" width="51%">00021493-0000-0000-C000-000000000046</td>        </tr>
<tr>
<td align="center" width="22%">水平的浏览器栏</td>
<td align="center" width="25%"><font face="宋体" size="3"><span lang="en-us">CATID_CommBand</span></font></td>
<td align="center" width="51%">00021494-0000-0000-C000-000000000046</td>        </tr>
<tr>
<td align="center" width="22%">桌面的工具栏</td>
<td align="center" width="25%"><font face="宋体" size="3"><span lang="en-us">CATID_DeskBand</span></font></td>
<td align="center" width="51%">00021492-0000-0000-C000-000000000046</td>        </tr>    </tbody></table>
　　IE 工具栏不使用组件类型注册，而是使用在注册进行 CLSID 的登记方式。详细情况见 3.3。
　　在例子程序中，实现了全部四个类型的 band 对象，垂直浏览器栏(CVerticalBar)显示了一个 HTML 文件，并且实现了对 IE 主窗口浏览网页的导航等功能；水平的浏览器栏(CHorizontalBar)是一个编辑窗，它同步显示当前网页的 BODY 源文件内容；IE 工具栏(CToolBar)最简单，只是添加了一个空的工具栏；桌面工具栏(CDeskBar)实现了一个单行编辑窗口，你可以在上面输入命令行或文件名称，回车后它会执行 Shell 的打开动作。
<em><a name="3.2　必须实现的_COM_接口"><font color="#000000">3.2　必须实现的 COM 接口</font></a></em>
　　Band 对象是 IE 或 Shell 的进程内服务器，所以它被包装在 DLL 中。而作为 COM 对象，它必须要实现 IUnknown 和 IClassFactory 接口。（大家可以不同操心，因为我们用 ATL 写程序，这两个接口是不用我们自己写代码的。）另外，Band 对象还必须实现 IDeskBand、IObjectWithSite 和 IPersistStream 三个接口：
　　<a name="IPersistStream"><font color="#000000">IPersistStream</font></a> 是持续性接口的一种。当 IE 加载 band 对象的时候，它通过这个接口的 Load 方法传递属性值给对象，让其进行初始化；而当卸载前，IE 则调用这个接口的 Save 方法保存对象的属性。用 ATL 实现这个接口很简单： 
<pre>class ATL_NO_VTABLE Cxxx : 	......	public IPersistStreamInitImpl, // 添加继承	......{public:	BOOL m_bRequiresSave; // IPersistStreamInitImpl 所必须的变量......BEGIN_COM_MAP(CVerticalBar)	......	COM_INTERFACE_ENTRY2(IPersist, IPersistStreamInit)	COM_INTERFACE_ENTRY2(IPersistStream, IPersistStreamInit)	COM_INTERFACE_ENTRY(IPersistStreamInit)	......END_COM_MAP()BEGIN_PROP_MAP(Cxxx)...... // 添加需要持续性的属性END_PROP_MAP()		</pre>　　上面的代码，其实实现的是 IPersistStreamInit 接口，不过没有关系，因为 IPersistStreamInit 派生自 IPersistStream，实例化了派生类，自然就实例化了基类。在例子程序中，我只在桌面工具栏对象中添加了持续性属性，用来保存和初始化“命令行”。另外 COM_INTERFACE_ENTRY2(A，B)表示的含义是：如果想查询A接口的指针，则提供B接口指针来代替。为什么可以这样那？因为B接口派生自A接口，那么B接口的前几个函数必然就是A接口的函数了，自然B接口的地址其实和A接口的地址是一样的了。
　　<a name="IObjectWithSite"><font color="#000000">IObjectWithSite</font></a> 是 IE 用来对插件进行管理和通讯用的一个接口。必须要实现这个接口的2个函数：SetSite() 和 GetSite()。当 IE 加载 band 对象和释放 band 对象的时候，都要调用 SetSite()函数，那么在这个函数里正好是写初始化和释放操作代码的地方：
<pre>STDMETHODIMP Cxxx::SetSite(IUnknown *pUnkSite){	if( NULL == pUnkSite )	// 释放 band 的时候	{		// 如果加载的时候，保存了一些接口		// 那么现在：释放它	}	else	// 加载 band 的时候	{		m_hwndParent = NULL;	// 装载 band 的父窗口(就是带有标题的那个框架窗口)		// 这个窗口的句柄，是调用 IUnknown::QueryInterface() 得到 IOleWindow		// 然后调用 IOleWindow::GetWindow() 而获得的。		CComQIPtr< IOleWindow, &IID_IOleWindow > spOleWindow(pUnkSite);		if( spOleWindow )	spOleWindow->GetWindow(&m_hwndParent);		if( !m_hwndParent )	return E_FAIL;				// 现在，正好是建立子窗口的时机。		// 注意，子窗口建立的时候，不要使用 WS_VISIBLE 属性		... ...		// 在例子程序中，用 CAxWindow 实现了一个能包容ActiveX的容器窗口(垂直浏览器栏)		// 在例子程序中，用 WIN API 函数 CreateWindow 实现了标准窗口(水平浏览器栏、工具栏)		// 在例子程序中，用 CWindowImpl 实现了一个包容窗口(桌面工具栏)		/*********************************************************/		   以下部分，根据 band 对象特有的功能，是可以选择实现的		**********************************************************/				// 如果子窗口实现了用户输入，那么必须实现 IInputObject 接口，		// 而该接口是被 IE 的 IInputObjectSite 调用的，因此在你的对象		// 中，应该保存 IInputObjectSite 的接口指针。		// 在类的头文件中，定义：		// CComQIPtr< IInputObjectSite, &IID_IInputObjectSite > m_spSite;		m_spSite = pUnkSite;	// 保存 IInputObjectSite 指针		if( !m_spSite )		return E_FAIL;		// 你需要控制 IE 的主框架吗？		// 那么在类的头文件中，定义：		// CComQIPtr< IWebBrowser2, &IID_IWebBrowser2 > m_spFrameWB;		// 然后，先取得 IServiceProvider,再取得 IWebBrowser2		CComQIPtr < IServiceProvider, &IID_IServiceProvider> spSP(pUnkSite);		if( !spSP )	return E_FAIL;		spSP->QueryService( SID_SWebBrowserApp, &m_spFrameWB );		if( !m_spFrameWB)	return E_FAIL;		// 如果你取得了 IE 主框架的 IWebBrowser2 指针		// 那么，当它发生了什么事情，你难道不想知道吗？		// 定义：CComPtr m_spCP;		CComQIPtr< IConnectionPointContainer,			&IID_IConnectionPointContainer> spCPC( m_spFrameWB );		if( spCPC )		{			spCPC->FindConnectionPoint( DIID_DWebBrowserEvents2, &m_spCP );			if( m_spCP )			{				m_spCP->Advise( reinterpret_cast< IDispatch * >( this ), &m_dwCookie );			}		}		// 咳~~~ 不说了，看源码去吧。这里能干的事情太多了... ...	}	return S_OK;}		</pre><a name="IDeskBand"><font color="#000000">IDeskBand</font></a> 是一个特殊的 band 对象接口，有一个方法函数：GetBarInfo()；
IDockingWindow 是 IDeskBank 的基类，有3个方法函数：ShowDW()、CloseDW()、ResizeBorderDW()；
IOleWindow 又是 IDockingWindow 的基类，有2个方法函数：GetWindow()、ContextSensitiveHelp()； 
　　首先声明 IDeskBand ,然后要实现 IDeskBand 接口的共6个函数，这些函数比较简单，不同类型的 band 对象，其实现方法也都基本一致：
<pre>class ATL_NO_VTABLE Cxxx : 	......	public IDeskBand,	......{......BEGIN_COM_MAP(Cxxx)	......	COM_INTERFACE_ENTRY_IID(IID_IDeskBand, IDeskBand)	......END_COM_MAP()// IOleWindowSTDMETHODIMP Cxxx::GetWindow(HWND * phwnd){	// 取得 band 对象的窗口句柄	// m_hWnd 是建立窗口时候保存的	*phwnd = m_hWnd;		return S_OK;}STDMETHODIMP Cxxx::ContextSensitiveHelp(BOOL fEnterMode){	// 上下文帮助，参考 IContextMenu 接口	return E_NOTIMPL;}// IDockingWindowSTDMETHODIMP CVerticalBar::ShowDW(BOOL bShow){	// 显示或隐藏 band 窗口	if( m_hWnd )		::ShowWindow( m_hWnd, bShow ? SW_SHOW : SW_HIDE);	return S_OK;}STDMETHODIMP CVerticalBar::CloseDW(DWORD dwReserved){	// 销毁 band 窗口	if( ::IsWindow( m_hWnd ) )		::DestroyWindow( m_hWnd );	m_hWnd = NULL;    return S_OK;}STDMETHODIMP CVerticalBar::ResizeBorderDW(LPCRECT prcBorder, IUnknown* punkToolbarSite, BOOL fReserved){	// 当框架窗口的边框大小改变时	return E_NOTIMPL;}// IDeskBandSTDMETHODIMP CVerticalBar::GetBandInfo(DWORD dwBandID, DWORD dwViewMode,  DESKBANDINFO* pdbi){	         // 取得 band 的基本信息，你需要填写 pdbi 参数作为返回	if( NULL == pdbi )		return E_INVALIDARG;	// 如果将来需要调用 IOleCommandTarget::Exec() 则需要保存这2个参数	m_dwBandID = dwBandID;	m_dwViewMode = dwViewMode;	if(pdbi->dwMask & DBIM_MINSIZE)	{	// 最小尺寸		pdbi->ptMinSize.x = 10;		pdbi->ptMinSize.y = 10;	}	if(pdbi->dwMask & DBIM_MAXSIZE)	{	// 最大尺寸 (-1 表示 4G)		pdbi->ptMaxSize.x = -1;		pdbi->ptMaxSize.y = -1;	}	if(pdbi->dwMask & DBIM_INTEGRAL)	{		pdbi->ptIntegral.x = 1;		pdbi->ptIntegral.y = 1;	}	if(pdbi->dwMask & DBIM_ACTUAL)	{		pdbi->ptActual.x = 0;		pdbi->ptActual.y = 0;	}	if(pdbi->dwMask & DBIM_TITLE)	{	// 窗口标题		wcscpy(pdbi->wszTitle,L"窗口标题");	}	if(pdbi->dwMask & DBIM_MODEFLAGS)	{		pdbi->dwModeFlags = DBIMF_VARIABLEHEIGHT;	}	if(pdbi->dwMask & DBIM_BKCOLOR)	{	// 如果使用默认的背景色，则移除该标志		pdbi->dwMask &= ~DBIM_BKCOLOR;	}	return S_OK;}		</pre><em><a name="3.3　选择实现的_COM_接口"><font color="#000000">3.3　选择实现的 COM 接口</font></a></em>
　　有两个接口不是必须实现的，但也许很有用：IInputObject 和 IContextMenu。如果 band 对象需要接收用户的输入，那么必须实现 IInputObject 接口。IE 实现了 IInputObjectSite 接口，当容器中有多个输入窗口时，它调用 IInputObject 接口方法去负责管理用户的输入焦点。
在浏览器栏中需要实现3个函数：UIActivateIO()、HasFocusIO()、TranslateAcceleratorIO()。
当浏览器栏激活或失去活性的时候，IE 调用 UIActivateIO 函数，当激活的时候，浏览器栏一般调用 SetFocus 去设置它自己窗口的焦点。当 IE 需要判断哪个窗口有焦点的时候，它调用 HasFocusIO 。当浏览器栏的窗口或其子窗口有输入焦点时，则应返回 S_OK，否则返回 S_FALSE。TranslateAcceleratorIO 允许对象处理加速键，例子程序中没有实现，所以直接返回 S_FALSE。
<pre>STDMETHODIMP CExplorerBar::UIActivateIO(BOOL fActivate, LPMSG pMsg){    if(fActivate)        SetFocus(m_hWnd);    return S_OK;}STDMETHODIMP CExplorerBar::HasFocusIO(void){    if(m_bFocus)        return S_OK;    return S_FALSE;}STDMETHODIMP CExplorerBar::TranslateAcceleratorIO(LPMSG pMsg){    return S_FALSE;}      </pre>　　Band 对象能够通过包容器的 IOleCommandTarget::Exec() 调用执行命令。而 IOleCommandTarget 接口指针，则可以通过调用包容器的 IInputOjbectSite::QueryInterface（IID_IOleCommandTarget,...） 函数得到。CGID_DeskBand 是命令组，当一个 band 对象的 GetBandInfo 被调用的时候，包容器通过 dwBandID 参数指定一个 ID 给 band 对象，对象要保存住这个ID，以便调用 IOleCommandTarget::Exec()的时候使用。ID 的命令有：
<ul>
<li>DBID_BANDINFOCHANGED
    Band 的信息变化。设置参数 pvaIn 为 band ID， 该 ID 就是最近一次调用 GetBandInfo 所得到的值，容器会调用 band 对象的 GetBandInfo 函数来更新请求信息。 </li>
<li>DBID_MAXIMIZEBAND 
    最大化 band。设置参数 pvaIn 为 band ID，该 ID 就是最近一次调用 ?GetBandInfo ?所得到的值。 </li>
<li>DBID_SHOWONLY 
    打开或关闭容器中其它的 bands。 设置参数 pvaIn 为VT_UNKNOWN 类型，它可以是如下的值：
    　
<table class="clsStd" width="727" border="1">
<tbody>
<tr>
<th width="62"><font face="宋体" size="3">值</font></th>
<th width="650"><font face="宋体" size="3">描述</font></th>            </tr>
<tr>
<td align="center" width="62"><font face="宋体" size="3">pUnk</font></td>
<td width="650"><font face="宋体" size="3"><span lang="en-us">band </span>对象的 <strong>IUnknown</strong> 指针，其它的桌面<span lang="en-us"> bands </span>将被隐藏</font></td>            </tr>
<tr>
<td align="center" width="62"><font face="宋体" size="3">0</font></td>
<td width="650"><font face="宋体" size="3">隐藏所有的桌面<span lang="en-us"> bands</span></font></td>            </tr>
<tr>
<td align="center" width="62"><font face="宋体" size="3">1</font></td>
<td width="650"><font face="宋体" size="3">显示所有的桌面 <span lang="en-us">bands</span></font></td>            </tr>        </tbody>    </table>    
    </li>
<li>DBID_PUSHCHEVRON
    在菜单项左边显示“v”的选择标志。容器发送一个 RB_PUSHCHEVRON 消息，当 band 对象接收到通知消息 RBN_CHEVRONPUSHED 提示它显示一个"v"的标志。设置 IOleCommandTarget::Exec 函数中 nCmdExecOpt 参数为 band ID，该 ID 是最近一次调用 GetBandInfo ?所得到的值，设置 IOleCommandTarget::Exec 函数中 pvaIn 参数为 VT_I4 类型，这是应用程序定义的一个值，它通过通知消息 RBN_CHEVRONPUSHED 中lAppValue 回传给 band 对象。 </li></ul>
<em><a name="3.4　Band_对象注册"><font color="#000000">3.4　Band 对象注册</font></a></em>
　　Band 对象必须注册为一个 OLE 进程内的服务器，并且支持 apartment 线程公寓。注册表中默认键的值是表示菜单的文字。对于浏览器栏，它加到 IE 菜单的“查看\浏览器栏”中；对于工具栏 band ，它加到 IE 菜单的“查看\工具栏”中；对于桌面 band， 它加到系统任务栏的快捷菜单中。在菜单资源中，可以使用“&”指明加速键。
通常，一个基本的 band 对象的注册表项目是：
<strong>HKEY_CLASSES_ROOT 
CLSID 
<em>{你的 band 对象的 CLSID}</em></strong> 
　　(Default) = 菜单的文字 
　　InProcServer32 
　　　(Default) = DLL 的全路径文件名 
　　　ThreadingModel= Apartment
工具栏 bands 还必须把它们的 CLSID 注册到 IE 的注册表中。
在 <strong>HKEY_LOCAL_MACHINE\Software\Microsoft\Internet Explorer\Toolbar</strong> 下给出 CLSID 作为键名，而其键值是被忽略的。
<strong>HKEY_LOCAL_MACHINE 
Software 
Microsoft 
Internet Explorer 
Toolbar </strong>
　　{你的 band 对象的 CLSID}
　　还有几个可选的注册表项目(例子程序并不是这样实现的)。比如，你想让浏览器栏显示 HTML 的话，必须要如下设置注册表： 
<strong>HKEY_CLASSES_ROOT 
CLSID 
<em>{你的 Band 对象的 CLSID} </em>
Instance 
CLSID 
　　</strong>(Default) = {4D5C8C2A-D075-11D0-B416-00C04FB90376}
同时，如果要指定一个本地的 HTML 文件，那么要如下设置： 
<strong>HKEY_CLASSES_ROOT 
CLSID 
<em>{你的 Band 对象的 CLSID}</em> 
Instance 
InitPropertyBag 
　　</strong>Url
　　另外，还可以指定浏览器栏的宽和高，当然，它是依赖于这个栏是纵向还是横向的。其实这个项目无所谓，因为当用户调整了浏览器栏的大小后，会自动保存在注册表中的。
<strong>HKEY_CURRENT_USER 
Software 
Microsoft 
Internet Explorer 
Explorer Bars 
{你的 Band 对象的 CLSID} 
　　</strong>BarSize
　　BarSize 键的类型必须是 REG_BINARY 类型，它有8个字节。左起前4个字节，是用16进制表示的像素宽度或高度，后4个字节保留，你应该设置为0。下面是一个可以在浏览器栏上显示 HTML 文件的全部注册表项目的例子，默认宽度为291（0x123）个像素点： 
<strong>HKEY_CLASSES_ROOT 
CLSID 
<em>{你的 Band 对象的 CLSID} </em></strong>
　(Default) = 菜单文字 
　<strong>InProcServer32 </strong>
　　(Default) = DLL 的全路径文件名 
　　ThreadingModel= Apartment
<strong>Instance 
CLSID </strong>
　　(Default) = {4D5C8C2A-D075-11D0-B416-00C04FB90376}
<strong>InitPropertyBag</strong> 
　　Url= 你的 HTML 文件名
<strong>HKEY_CURRENT_USER 
Software 
Microsoft 
Internet Explorer 
Explorer Bars 
{你的 Band 对象的 CLSID} </strong>
　　BarSize= 23 01 00 00 00 00 00 00
　　对于注册表的设置，用 ATL 实现其实是异常简单的。打开工程的 xxx.rgs 文件，并手工编辑一下就可以了。 下面这个文件源码，是例子程序中 IE 工具栏的注册表样式，HKLM 是需要手工添加的，因为它不使用组件类型方式注册。而对于其它类型的 band 对象只要在类声明中添加： 
<pre>BEGIN_CATEGORY_MAP(Cxxx)			// 向注册表中注册 COM 类型	IMPLEMENTED_CATEGORY(CATID_InfoBand)	// 垂直样式的浏览器栏END_CATEGORY_MAP()		</pre>IE 工具栏类型 band 对象的“.rgs”文件
<pre>HKCR	// 这个项目是 ATL 帮你生成的，你只要手工修改“菜单上的文字”就可以了{	Bands.ToolBar.1 = s ''ToolBar Class''	{		CLSID = s ''{ 你的 CLSID }''	}	Bands.ToolBar = s ''ToolBar Class''	{		CLSID = s ''{ 你的 CLSID }''		CurVer = s ''Bands.ToolBar.1''	}	NoRemove CLSID	{		ForceRemove { 你的 CLSID } = s ''用在菜单上的文字(&T)''		{			ProgID = s ''Bands.ToolBar.1''			VersionIndependentProgID = s ''Bands.ToolBar''			ForceRemove ''Programmable''			InprocServer32 = s ''%MODULE%''			{				val ThreadingModel = s ''Apartment''			}			''TypeLib'' = s ''{xxxx-xxxx-xxxxxxxxxxxxxxx}''		}	}}HKLM	// 这个项目是手工添加的IE工具栏所特有的{	Software	{		Microsoft		{			''Internet Explorer''			{				NoRemove Toolbar				{					ForceRemove val { 你的 CLSID } = s ''随便给个说明性文字串''				}			}		}	}}		</pre><strong><a name="四、_ATL_实现"><font color="#000000">四、 ATL 实现</font></a></strong>
　　下载代码后(VC 6.0 工程)，请参照前面的说明仔细阅读，代码中也有一些关键点的注释。如果想运行，则可以用 regsvr32.exe 进行注册，然后打开 IE 浏览器或资源浏览器就可以看到效果了。如果想自己实践一下，可以按照如下的步骤构造工程：
4.1　建立一个 ATL DLL 工程
4.2　添加 New ATL Object...，选择 Internet Explorer Object，选这个类型的目的是让向导给我们添加 IObjectWithSite 的支持。如果你使用的是 .net 环境，则不要忘记选择支持这个接口。
<img height="257" src="http://www.vckbase.com/document/journal/vckbase42/images/yfbands2.jpg" width="413" border="0" alt="" />
4.3　输入对象名称，比如我想建立一个垂直的浏览器栏，不妨叫它 VerBar
<img height="260" src="http://www.vckbase.com/document/journal/vckbase42/images/yfbands3.jpg" width="421" border="0" alt="" />
4.4　线程模型必须选择 Apartment，接口类型的选择无所谓，看你想不想支持 IDispatch 接口功能了。在例子程序中的垂直浏览器栏中，由于想更简单的操纵 IE 和从 IE 中接受事件（连接点），选择 Dual 是必要的。聚合选项，你只要别选择 Only 就可以了。
<img height="260" src="http://www.vckbase.com/document/journal/vckbase42/images/yfbands4.jpg" width="421" border="0" alt="" />
4.5　展现你无穷的智慧，开始输入程序吧。如果是 Debug 方式编译，可能会出现一个连接错误，报告找不到_AtlAxCreateControl，那么你要在菜单 Project\Settings...\Link 中增加对 Atl.lib 的连接。或者使用 #pragma comment ( lib, "atl" )加入连接库。
4.6　如果想调试代码，在菜单 Project\Settings...\Debug 中输入 IE 的路径名称，比如：“C:\Program Files\Internet Explorer\IEXPLORE.EXE”，然后就可以跟踪断点调试了。 编译和调试桌面工具栏的 band 对象，是非常麻烦的，因为计算机启动时自动运行 Shell，而 Shell 就会加载活动的桌面对象。
<strong>五、结束语</strong>
好了，到这里，就到这里了。祝大家学习快乐^_^
