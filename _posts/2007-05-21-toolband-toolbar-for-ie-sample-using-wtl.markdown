---
layout: post
title: Toolband (Toolbar for IE) sample using WTL
author: Alvin
date: !binary |-
  MjAwNy0wNS0yMSAxMDoxMjo1OCArMDgwMA==
date_gmt: !binary |-
  MjAwNy0wNS0yMSAwMjoxMjo1OCArMDgwMA==
---
转至: <a href="http://www.codeproject.com">http://www.codeproject.com</a>
<ul class="download">
<li>Download source files - 40 Kb </li>
<li>Download demo project - 40 Kb </li></ul><!-- Article image -->
<!-- Add the rest of your HTML here -->
<h2>Credits and Acknowlegements</h2>
<p nd="1">This module is based on the <a href="http://www.codeproject.com/atl/rbdeskband.asp">ATL DeskBand Wizard article</a> that was created by <a href="mailto:erikt@radbytes.com">Erik Thompson</a>. My thanks goes to him for producing a great wizard that saves you the nity grity of creating DeskBands.
<p align="left" nd="2">Please note that this project does not use MFC, for all those MFC die hards that want to use MFC in their Tool Bands I suggest you down load the KKBar sample from microsofts MSDN site.
<p align="left">The KBBar Sample can be downloaded from here
<h2 align="left">Creating the ToolBand Module</h2>
<p align="left" nd="3">You will need to install the <a href="http://www.codeproject.com/atl/rbdeskband.asp">ATL DeskBand Wizard</a> in order to create ToolBands. Please follow the instruction in this article.
<p align="left" nd="4">The best way to kick start a Tool band project is to use the ATL COM App Wizard. The COM Object needs to be in-proc therefore choose 'DLL' as the Server Type. The rest of the options can be kept in there default state. (Note this project does not use MFC, as the project is based on ATL and WTL)
<p align="left" nd="5">Once you have a ATL COM Project, You can use the <a href="http://www.codeproject.com/atl/rbdeskband.asp">ATL DeskBand Wizard</a> to create the Initial Tool Band.
<h2 align="left">Adding Support for WTL</h2>
<p align="left" nd="6">You will require the WTL Libraries, these can be downloaded from the microsoft site
<p align="left" nd="7">See <a href="http://www.codeproject.com/wtl/wtlinst.asp">Installation of WTL</a>
<p align="left" nd="8">The following header files needs to be added to stdafx.h file
<pre nd="9">atlapp.hatlwin.hatlctrls.hatlmisc.h</pre>
<h2 align="left">Create the ToolBar</h2>
<p nd="10">You can create your <a class="iAs" style="FONT-WEIGHT: normal; FONT-SIZE: 100%; PADDING-BOTTOM: 1px; COLOR: darkgreen; BORDER-BOTTOM: darkgreen 0.07em solid; BACKGROUND-COLOR: transparent; TEXT-DECORATION: underline" target="_blank" itxtdid="3922941" href="http://www.codeproject.com/wtl/toolband.asp#"><font color="#006400">toolbar</font></a> in the usual way via the resource editor, Once you have your toolbar you need to create the toolbar dynamically. The <code nd="11">CBandToolBarCtrl</code> is inherited from CToolBarCtrl and is used to create the toolbar dynamically.
<pre nd="12">DWORD dStyle = WS_CHILD | WS_VISIBLE | WS_CLIPCHILDREN | WS_CLIPSIBLINGS |
                CCS_NODIVIDER | CCS_NORESIZE | CCS_NOPARENTALIGN |
                TBSTYLE_TOOLTIPS | TBSTYLE_FLAT;
    HWND hWnd = m_wndToolBar.CreateSimpleToolBarCtrl(hWndChild, IDR_TOOLBAR_TEST,
                                                  FALSE, dStyle);
 </pre>
<p align="left" nd="13">This is done in the RegisterAndCreateWindow function that is called from SetSite method.
<h2 align="left">Message Reflection</h2>
<p align="left" nd="14">The best way to handle the messages of the toolbar is to let the control handle its own messages via "Message Reflection". The best way to reflect the messages is to create an invisible control that acts as the parent for the toolbar. The invisible control will reflect the toolbar messages back to itself. See <code nd="15">CBandToolBarReflectorCtrl</code> class for the invisible control that will be used.
<p align="left" nd="16">The following code shows the parent child relationship that is used to achieve Message Reflection.
<pre id="pre2" style="MARGIN-TOP: 0px" nd="18">BOOL CToolBandObj::RegisterAndCreateWindow()
{
    RECT rectClientParent;
    ::GetClientRect(m_hWndParent, &rectClientParent);
    <span class="cpp-comment">// We need to create an Invisible Child Window using the Parent Window,
 </span>    <span class="cpp-comment">// this will also be used to reflect Command
</span>    <span class="cpp-comment">// messages from the rebar
</span>    HWND hWndChild = m_wndInvisibleChildWnd.Create(m_hWndParent, 
                                                   rectClientParent,
                                                    NULL, WS_CHILD);
        <span class="cpp-comment">// Now we can create the Tool Bar, using the Invisible Child
</span>    DWORD dStyle = WS_CHILD | WS_VISIBLE | WS_CLIPCHILDREN | WS_CLIPSIBLINGS |
                    CCS_NODIVIDER | CCS_NORESIZE | CCS_NOPARENTALIGN |
                    TBSTYLE_TOOLTIPS | TBSTYLE_FLAT;
        HWND hWnd = m_wndToolBar.CreateSimpleToolBarCtrl(hWndChild,
                                                     IDR_TOOLBAR_TEST,
                                                      FALSE, dStyle);
     m_wndToolBar.SetExtendedStyle(TBSTYLE_EX_DRAWDDARROWS);
              m_wndToolBar.m_ctlBandEdit.m_pBand = <span class="cpp-keyword">this</span>;
    <span class="cpp-keyword">return</span> ::IsWindow(m_wndToolBar.m_hWnd);
}</pre>
<p align="left" nd="19">The following Macros are used to identify the reflected messages from the ordinary messages. e.g WM_COMMAND reflected comes back as OCM_COMMAND.
<pre nd="21"><span class="cpp-preprocessor" nd="20">#define OCM_COMMAND_CODE_HANDLER(code, func) \
</span><span class="cpp-keyword">if</span>(uMsg == OCM_COMMAND && code == HIWORD(wParam)) \
{ \
    bHandled = TRUE; \
    lResult = func(HIWORD(wParam), LOWORD(wParam), (HWND)lParam, bHandled); \
    <span class="cpp-keyword">if</span>(bHandled) \
    <span class="cpp-keyword">return</span> TRUE; \
}
<span class="cpp-preprocessor" nd="22">#define OCM_COMMAND_ID_HANDLER(id, func) \
</span><span class="cpp-keyword">if</span>(uMsg == OCM_COMMAND && id == LOWORD(wParam)) \
{ \
    bHandled = TRUE; \
    lResult = func(HIWORD(wParam), LOWORD(wParam), (HWND)lParam, bHandled); \
    <span class="cpp-keyword">if</span>(bHandled) \
    <span class="cpp-keyword">return</span> TRUE; \
}
<span class="cpp-preprocessor" nd="23">#define OCM_NOTIFY_CODE_HANDLER(cd, func) \
</span><span class="cpp-keyword">if</span>(uMsg == OCM_NOTIFY && cd == ((LPNMHDR)lParam)->code) \
{ \
    bHandled = TRUE; \
    lResult = func((<span class="cpp-keyword">int</span>)wParam, (LPNMHDR)lParam, bHandled); \
    <span class="cpp-keyword">if</span>(bHandled) \
    <span class="cpp-keyword">return</span> TRUE; \
}</pre>
<h2 align="left">Browser Navigation</h2>
<p align="left" nd="24">In order to Navigate on the <a class="iAs" style="FONT-WEIGHT: normal; FONT-SIZE: 100%; PADDING-BOTTOM: 1px; COLOR: darkgreen; BORDER-BOTTOM: darkgreen 0.07em solid; BACKGROUND-COLOR: transparent; TEXT-DECORATION: underline" target="_blank" itxtdid="3924683" href="http://www.codeproject.com/wtl/toolband.asp#"><font color="#006400">browser</font></a> you need to instantiate the IWebBrowser2 COM Object. This is usually done on the SetSite Method e.g.
<pre nd="25">IServiceProviderPtr pServiceProvider = pUnkSite;
<span class="cpp-keyword">if</span> (_Module.m_pWebBrowser)
    _Module.m_pWebBrowser = NULL;
 <span class="cpp-keyword">if</span>(FAILED(pServiceProvider->QueryService(SID_SWebBrowserApp, IID_IWebBrowser,
                                             (<span class="cpp-keyword">void</span>**)&_Module.m_pWebBrowser)))
<span class="cpp-keyword">return</span> E_FAIL;</pre>
<p align="left" nd="26">Once you have the COM Object Instantiated, you can move to your URL using the navigate method
<pre nd="27">	_variant_t varURL = _bstr_t(<span class="cpp-string" nd="28"><a href="http://www.codeproject.com">www.codeproject.com</a></span>);
 _variant_t varEmpty;
_Module.m_pWebBrowser->Navigate2(&varURL, &varEmpty, &varEmpty,
                                     &varEmpty, &varEmpty);</pre>
<h2>Drag and Drop Edit and ComboBox Control</h2>
<p nd="29">The CBandEditCtrl class is inherited from a WTL CEdit control that has drag and drop facility. i.e. you can drag text from the browser straight to the CEdit Control.
<p nd="30">The CBandComboBoxCtrl class is inherited from a WTL CComboBox control that has drag and drop facility. i.e. you can drag text from the browser straight to the CComboBox Control.
The Edit control in this sample module allows you to drag a URL from Explorer or any other Dragable enabled container. Once you have dropped the URL the toolband will go to that site. 
<h2>Configurable Toolbar Button Styles</h2>
<p nd="32">The CBandToolBarCtrl class allows you to have the follwoing button styles on the toolbar
<ul>
<li nd="33">Image and Text on the right </li>
<li nd="34">Image and Text on the bottom </li>
<li nd="35">Image only</li></ul>


<h2>Pop-up Menu Tracking</h2>
<p nd="36">A pop up menu is used to get to the configuration options

<h2>ToolTips</h2>
<p nd="37">This has been taken from MSDN to explain why you need to handle the tooltips on the toolbar your self.
<em>Tool tips are automatically displayed for buttons and other controls contained in a parent window derived from CFrameWnd. This is because CFrameWnd has a default handler for the TTN_GETDISPINFO notification, which handles TTN_NEEDTEXT notifications from tool tip controls associated with controls.</em>
<em>However, this default handler is not called when the TTN_NEEDTEXT notification is sent from a tool tip control associated with a control in a window that is not a CFrameWnd, such as a control on a dialog box or a form view. Therefore, it is necessary for you to provide a handler function for the TTN_NEEDTEXT notification message in order to display tool tips for child controls.</em> 
<p nd="38">This can be easily done in WTL by using the following Message Handler in the overriden ToolBar Control
<pre nd="39">NOTIFY_CODE_HANDLER(TTN_NEEDTEXT, OnToolbarNeedText)</pre>
<p nd="40">The following is the code that loads the tool tips from the resources and sets the tool tip text. 
<pre nd="41">LRESULT CBandToolBarCtrl::OnToolbarNeedText(<span class="cpp-keyword">int</span> <span class="cpp-comment">/*idCtrl*/</span>, LPNMHDR pnmh, BOOL&
                                             bHandled)	{
    CString sToolTip;
        <span class="cpp-comment">//-- make sure this 1is not a seperator
</span>    <span class="cpp-keyword">if</span> (idCtrl != <span class="cpp-literal">0</span>)
     {
        <span class="cpp-keyword">if</span> (!sToolTip.LoadString(idCtrl))
        {
            bHandled = FALSE;
            <span class="cpp-keyword">return</span> <span class="cpp-literal">0;
</span>        }
    }
    LPNMTTDISPINFO pttdi = <span class="cpp-keyword">reinterpret_cast</span><LPNMTTDISPINFO>
	    (pnmh);
pttdi->lpszText = MAKEINTRESOURCE(idCtrl);
    pttdi->hinst = _Module.GetResourceInstance();
    pttdi->uFlags = TTF_DI_SETITEM;
    <span class="cpp-comment">//-- message processed
</span>    <span class="cpp-keyword">return</span> <span class="cpp-literal">0</span>;
}</pre>
<h2>Update the Status Bar</h2>
<p nd="42">You can update the status bar using the browser method put_StatusText, this method can be used typically in the WM_MENUSELECT event
<p nd="43">The following is the code that loads up the menu text from the resources and displays it on the browser status bar.
<div class="precollapse" id="premain8" style="WIDTH: 100%"><img id="preimg8" style="CURSOR: hand" height="9" alt="" width="9" preid="8" src="http://www.codeproject.com/images/minus.gif" /><span id="precollapse8" style="MARGIN-BOTTOM: 0px; CURSOR: hand" nd="44" preid="8"> Collapse</span></div>
<pre id="pre8" style="MARGIN-TOP: 0px" nd="45">LRESULT CBandToolBarCtrl::OnMenuSelect(UINT <span class="cpp-comment">/*uMsg*/</span>, 
                                       WPARAM wParam, LPARAM lParam,
                                        BOOL& bHandled)
 {
    WORD nID = LOWORD(wParam);
    WORD wFlags = HIWORD(wParam);
        <span class="cpp-comment">//-- make sure this is not a seperator
</span>    CString sStatusBarDesc;
    <span class="cpp-keyword">if</span> ( !(wFlags & MF_POPUP) )
    {
        <span class="cpp-keyword">if</span> (nID != <span class="cpp-literal">0</span>)
         {
            <span class="cpp-keyword">if</span> (!sStatusBarDesc.LoadString(nID))
            {
	        bHandled = FALSE;
                <span class="cpp-keyword">return</span> <span class="cpp-literal">0</span>;
            }
            <span class="cpp-keyword">int</span> nPos = sStatusBarDesc.Find(_T(<span class="cpp-string" nd="46">"\n"</span>));
            <span class="cpp-keyword">if</span> (nPos != -<span class="cpp-literal">1</span>)
            {
                sStatusBarDesc =
                 sStatusBarDesc.Left(nPos+<span class="cpp-literal">1</span>);
                _Module.m_pWebBrowser->
                put_StatusText(_bstr_t(sStatusBarDesc));
                <span class="cpp-keyword">return</span> <span class="cpp-literal">0</span>;
            }
        }
    }
    <span class="cpp-keyword">return</span> <span class="cpp-literal">0</span>;
}</pre>
<h2>How to add a Chevron to your toolband</h2>
<p nd="47">In order to add a chevron to your toolband you need to add the DBIMF_USECHEVRON flags to your GetBandInfo method in the DBIM_MODEFLAGS mask.
<pre nd="48"> ...
 <span class="cpp-keyword">if</span>(pdbi->dwMask == DBIM_MODEFLAGS)
     {
         <span class="cpp-comment">//AddChevron
</span>        pdbi->dwModeFlags = DBIMF_NORMAL | DBIMF_VARIABLEHEIGHT |
                                   DBIMF_USECHEVRON | DBIMF_BREAK;
    }
...</pre>
<p nd="49">This basically add the ability to show the chevron, if you want to see it appear on your toolband, then you must make sure the pdbi->ptMinSize.x value is less then your pdbi->ptActual.x value (Again this values can be set in the GetBandInfo method.
<p nd="50">In order to handle the events of the button in the chevron menu, you must subclass the rebar control which is hosting your toolbar. The following steps have been used to sublass the rebar control
<p nd="51">1. Find the rebar control - This can be achieved by getting the browsers window handle and searching for all its child windows, until you find the rebar control.
<p nd="52">2. Once you have found the rebar control you can simply subclass it using an ATL CContainedWindow.
<pre nd="53">    BOOL CBandToolBarCtrl::SetBandRebar()
    {
         HWND hWnd(NULL);
        _Module.m_pWebBrowser->get_HWND((<span class="cpp-keyword">long</span>*)&hWnd);
         <span class="cpp-keyword">if</span> (hWnd == NULL)
 	    <span class="cpp-keyword">return</span> FALSE;
	m_ctlRebar.m_hWnd = FindRebar(hWnd);
	<span class="cpp-keyword">if</span> (m_ctlRebar.m_hWnd == NULL)
 	    <span class="cpp-keyword">return</span> FALSE;
	m_RebarContainer.SubclassWindow(m_ctlRebar);
	<span class="cpp-keyword">return</span> TRUE;
    }</pre>
<p nd="54">Once you have subclass the window, the events should reach the toolbar class.
<h2>
<h2>Append to the Browser Context Menu</h2></h2>
<p nd="55">I tried to duplicate the google and codeproject toolband right mouse browser context menu searches without any luck, until I looked at the binary resources, I found that you can extend the internet explorer menu by adding the option in the registry. This can easily be achieved by adding an entry in your .rgs file.
<pre nd="56">HKCU{
    NoRemove Software
    {
        NoRemove Microsoft
        {
            NoRemove 'Internet Explorer'
            {
                NoRemove MenuExt
                {
                    ForceRemove '&Sample Toolband Serach' = s'res:<span class="cpp-comment">//%MODULE%/MENUSEARCH.HTM'
</span>                    {
                        val Contexts = b '<span class="cpp-literal">10</span>'
                    }
                }
            }
        }
    }
}</pre>
<p nd="57">Note the value of the registry item (a html script that exist in the resources of the module).
<p nd="58">see MENUSERACH.HTM under HTML in the module resource to see what the script is doing
