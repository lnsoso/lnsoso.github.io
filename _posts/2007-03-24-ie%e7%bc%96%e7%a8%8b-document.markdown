---
layout: post
title: IE编程 - Document
author: Alvin
date: !binary |-
  MjAwNy0wMy0yNCAyMTozODoxOCArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wMy0yNCAxMzozODoxOCArMDgwMA==
---
<strong>定制浏览器
</strong>作者：冯明德

<!-- DETAILS -->
浏览器控件是个典型的Active控件，提供了大量的接口及自动化对象，可以灵活的加以控制，需要的时候，可以通过这些接口控制浏览器的行为，或提供相应的出接口定制浏览器。 
一、概述 
浏览器对象CLSID: 
CLSID_WebBrowser 
提供的主要接口 
IWebBrowser2 浏览器的接口 
当文档建立后，可以得到相应的文档接口，文档中各标记元素的接口。 
在DHTML中，大量的对象和事件就是又这些接口提供和管理的。 
IHTMLDocument2 
IHTMLWindow2 
IHTMLEventObj 
IHTMLElement 
.... 
浏览器还将调用宿主提供的接口,以发出事件或给用户提供定制机会。 
出接口 
DIID_DWebBrowserEvents2 
DIID_HTMLDocumentEvents 
DIID_HTMLWindowEvents 
(ICustomDoc) 
IDocHostUIHandler 
二、事件的相应 
除了使用MFC缺省的事件响应机制外，也可以自建事件接受器，来响应事件 
也就是，在封装对象中提供DIID_DWebBrowserEvents2 接口，然后将此接口作为接受器连接到浏览器对象。 
一种做法是 
在派生类中，使用MFC建立接口方案提供一个DIID_DWebBrowserEvents2接口对象嵌套成员。 
<pre><span class="keyword">class</span> CFMDBrowser : <span class="keyword">public</span> CWebBrowser{	...	<span class="note">//事件接收器接口</span>	<span class="note">//DWebBrowserEvents</span>	<span class="note">//这是一个IDispatch分发接口</span>	BEGIN_INTERFACE_PART(BrowserEventSink,DWebBrowserEvents)		STDMETHOD(GetTypeInfoCount)(UINT *pctinfo);			STDMETHOD(GetTypeInfo)(UINT iTInfo,LCID lcid,ITypeInfo **ppTInfo);		STDMETHOD(GetIDsOfNames)(REFIID riid,				LPOLESTR *rgszNames,UINT cNames,				LCID lcid,DISPID *rgDispId);		STDMETHOD(Invoke)(DISPID dispIdMember,REFIID riid,LCID lcid,				WORD wFlags,DISPPARAMS *pDispParams,				VARIANT *pVarResult,EXCEPINFO *pExcepInfo,				UINT  *puArgErr);		END_INTERFACE_PART(BrowserEventSink)	DWORD m_dwEventSinkCookie;	...}</pre>
这是一个接收器接口，无需添入到对象的接口表中。 
(无需:BEGIN_INTERFACE_MAP、END_INTERFACE_MAP) 
这是一个以分发接口(IDispatch)作为出接口的典型例子。在接口函数的实现中。Invoke负责又分发ID调用不同的虚拟函数。(事件函数作为虚拟函数，供派生类重载) 
<pre>STDMETHODIMP CFMDBrowser::XBrowserEventSink::Invoke(DISPID dispIdMember,REFIID riid,LCID lcid,				  WORD wFlags,DISPPARAMS *pDispParams,				  VARIANT *pVarResult,EXCEPINFO *pExcepInfo,				  UINT  *puArgErr){	METHOD_PROLOGUE(CFMDBrowser,BrowserEventSink)	<span class="note">//将事件分发到各虚拟函数</span>	<span class="note">//分发ID的定义见 exdispid.h</span>	<span class="keyword">switch</span>(dispIdMember)	{	<span class="keyword">case</span> DISPID_BEFORENAVIGATE:		...		HRESULT hr=pThis->OnBeforeNavigate(..) <span class="note">//事件对应的虚拟函数</span>		...		<span class="keyword">break</span>;        <span class="keyword">case</span> DISPID_NAVIGATECOMPLETE:        		...	<span class="keyword">case</span> ...	<span class="keyword">case</span> ...}</pre>
建立与浏览器的连接 
得到IConnectionPointContainer接口，查找与DIID_DWebBrowserEvents对应的接收器，建立连接,记录连接的标号(m_dwEventSinkCookie); 
<pre>BOOL CFMDBrowser::Connect(){	IUnknown *p_Unk=GetControlUnknown();	<span class="keyword">if</span>(p_Unk==NULL)		<span class="keyword">return</span> FALSE;	BOOL bOK=FALSE;	<span class="note">//查找连接点对象</span>	IConnectionPointContainer *i_cpc=0;	HRESULT hr=p_Unk->QueryInterface(IID_IConnectionPointContainer,		(<span class="keyword">void</span> **)(&i_cpc));	<span class="keyword">if</span> (SUCCEEDED(hr))	{		IConnectionPoint *i_cp=0;		hr=i_cpc->FindConnectionPoint(DIID_DWebBrowserEvents,&i_cp);		<span class="keyword">if</span> (SUCCEEDED(hr))		{			hr=i_cp->Advise(&m_xBrowserEventSink,&m_dwEventSinkCookie);			i_cp->Release();			bOK=TRUE;		}		i_cpc->Release();	}		<span class="keyword">return</span> bOK;}</pre>
结束时，断开与浏览器的连接 
<pre>BOOL CFMDBrowser::DisConnect(){	IUnknown *p_Unk=GetControlUnknown();	<span class="keyword">if</span>(p_Unk==NULL)		<span class="keyword">return</span> FALSE;		BOOL bOK=FALSE;		<span class="note">//查找连接点对象</span>	IConnectionPointContainer *i_cpc=0;	HRESULT hr=p_Unk->QueryInterface(IID_IConnectionPointContainer,		(<span class="keyword">void</span> **)(&i_cpc));	<span class="keyword">if</span> (SUCCEEDED(hr))	{		IConnectionPoint *i_cp=0;		hr=i_cpc->FindConnectionPoint(DIID_DWebBrowserEvents,&i_cp);		<span class="keyword">if</span> (SUCCEEDED(hr))		{			hr=i_cp->Unadvise(m_dwEventSinkCookie);			i_cp->Release();			bOK=TRUE;		}		i_cpc->Release();	}		<span class="keyword">return</span> bOK;}</pre>
三、定制浏览器UI 
浏览器提供了IDocHostUIHandler出接口，向用户查询界面特性 
可以提供这个接口，与浏览器连接上,在其实现中，定制界面 
1.建立接口 
<pre><span class="keyword">class</span> CFMDBrowser : <span class="keyword">public</span> CWebBrowser{	...	<span class="note">//IDocHostUIHandler接口,控制浏览器界面</span>	BEGIN_INTERFACE_PART(UIHandlerSink,IDocHostUIHandler)		STDMETHOD(ShowContextMenu)(DWORD,POINT*,IUnknown*,IDispatch*);		STDMETHOD(GetHostInfo)(DOCHOSTUIINFO*);		STDMETHOD(ShowUI)(DWORD,			IOleInPlaceActiveObject*,			IOleCommandTarget*,			IOleInPlaceFrame*,			IOleInPlaceUIWindow*);		STDMETHOD(HideUI)();		STDMETHOD(UpdateUI)();		STDMETHOD(EnableModeless)(INT);		STDMETHOD(OnDocWindowActivate)(INT);		STDMETHOD(OnFrameWindowActivate)(INT);		STDMETHOD(ResizeBorder)(LPCRECT,IOleInPlaceUIWindow*,INT);		STDMETHOD(TranslateAccelerator)(LPMSG,<span class="keyword">const</span> GUID*,DWORD);		STDMETHOD(GetOptionKeyPath)(LPOLESTR*,DWORD);		STDMETHOD(GetDropTarget)(IDropTarget*,IDropTarget**);		STDMETHOD(GetExternal)(IDispatch**);		STDMETHOD(TranslateUrl)(DWORD,OLECHAR*,OLECHAR**);		STDMETHOD(FilterDataObject)(IDataObject*,IDataObject**);	END_INTERFACE_PART(UIHandlerSink)	...}</pre>
无需添加接口映射 
2.连接到浏览器 
需要在NavigateComplete时间发生后，得到 
ICustomDoc接口,由此接口的 
SetUIHandler成员设置UI接口。 
<pre><span class="note">//设置界面接口</span>IDispatch *i_dispatch=0;<span class="keyword">if</span> (SUCCEEDED(i_dispatch=pThis->GetDocument())){	IHTMLDocument2 *i_htmldoc2=0;	<span class="keyword">if</span> (SUCCEEDED(i_dispatch->QueryInterface(IID_IHTMLDocument2,			(<span class="keyword">void</span> **)(&i_htmldoc2))))	{			<span class="note">// force connection of IDocHostUIHandler</span>			ICustomDoc *i_customdoc=0;			<span class="keyword">if</span> (SUCCEEDED(i_htmldoc2->QueryInterface(						IID_ICustomDoc,						(<span class="keyword">void</span> **)(&i_customdoc))))			{				i_customdoc->SetUIHandler(					&(pThis->m_xUIHandlerSink));				i_customdoc->Release();			}	}	i_dispatch->Release();}</pre>
3.在接口的实现中，控制用户界面 
例如更改右键菜单 
在STDMETHOD(ShowContextMenu)(DWORD,POINT*,IUnknown*,IDispatch*); 
的实现中: 
<pre>HRESULT CFMDBrowser::ShowContextMenu(DWORD,POINT*,IUnknown*,IDispatch*){	..建立自己的菜单        <span class="keyword">return</span> S_OK;         }</pre>
