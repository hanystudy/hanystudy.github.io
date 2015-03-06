---
layout: post
status: publish
published: true
title: Visual C++动态链接库编程（五）
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 31
wordpress_url: http://www.hanyi.name/blog/?p=31
date: 2008-04-05 15:14:54.000000000 +08:00
categories:
- 转载
tags:
- dll
- vc++
comments:
- id: 7608
  author: viva viagra campaign
  author_email: thshsyah@iofbjvnx.com
  author_url: http://technorati.com/people/viagrax126m
  date: !binary |-
    MjAxMC0wNi0wNCAwNjozNjoyMSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMjozNjoyMSArMDgwMA==
  content: <a href="http://www.myoutdoorchannel.com/phentermineyl124" rel="nofollow">phentermine
    for weigh loss</a> , [url="http://www.myoutdoorchannel.com/phentermineyl124"]phentermine
    for weigh loss[/url] , http://www.myoutdoorchannel.com/phentermineyl124 phentermine
    for weigh loss ,
- id: 7942
  author: does valium show up on a drug test
  author_email: dphsgtrt@pzuhyqdl.com
  author_url: http://connect.al.com/user/valiumn138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMTowODoxNSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxMzowODoxNSArMDgwMA==
  content: Excellent site. It was pleasant to me.
- id: 8208
  author: physicians assistant adipex
  author_email: itbtahxb@sgyixzuy.com
  author_url: http://connect.nola.com/user/Adipex-Online-k138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMzoxOToxNCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNToxOToxNCArMDgwMA==
  content: If you have to do it, you might as well do it right.
- id: 8556
  author: phentermine and water pills
  author_email: twkgmfxk@ujvucrcn.com
  author_url: http://connect.al.com/user/phentermine7138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMjoxMTo1NSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxODoxMTo1NSArMDgwMA==
  content: I bookmarked this link. Thank you for good job!
- id: 8740
  author: buy us xanax online
  author_email: lclrnyhm@vvfmbbzi.com
  author_url: http://connect.al.com/user/cialisp138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMzozODoxMyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxOTozODoxMyArMDgwMA==
  content: Great site. Keep doing.
- id: 8866
  author: soma intelligence llc
  author_email: rpnbvtmx@vucdfqbb.com
  author_url: http://www.jamesbondwiki.com/account/Soma-Online-d158m
  date: !binary |-
    MjAxMC0xMC0yNyAyMDowMDoyOSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0xMC0yNyAxMjowMDoyOSArMDgwMA==
  content: Very interesting site. Hope it will always be alive!
---
　　前文我们对非MFC DLL和MFC规则DLL进行了介绍，现在开始详细分析DLL的最后一种类型――MFC扩展DLL。

　　<strong>6.1概论</strong>

　　MFC扩展DLL与MFC规则DLL的相同点在于在两种DLL的内部都可以使用MFC类库，其不同点在于MFC扩展DLL与应用<a target="_blank" href="http://dev.yesky.com/" class="bluekey"><font color="#003399">程序</font></a>的接口可以是MFC的。MFC扩展DLL的含义在于它是MFC的扩展，其主要功能是实现从现有MFC库类中派生出可重用的类。MFC扩展DLL使用MFC 动态链接库版本，因此只有用共享MFC 版本生成的MFC 可执行文件（应用程序或规则DLL）才能使用MFC扩展DLL。
<p class="guanggao">　　从前文可知，MFC规则DLL被MFC向导自动添加了一个CWinApp的对象，而MFC扩展DLL则不包含该对象，它只是被自动添加了DllMain 函数。对于MFC扩展DLL，开发人员必须在DLL的DllMain函数中添加初始化和结束代码。</p>
　　从下表我们可以看出三种DLL对DllMain入口函数的不同处理方式：

<p class="guanggao">
<table border="1" align="center" width="90%" cellPadding="2" cellSpacing="0">
<tr>
<td>DLL类型</td>
<td>入口函数</td>
</tr>
<tr>
<td>非 MFC DLL</td>
<td>编程者提供DllMain函数</td>
</tr>
<tr>
<td>MFC规则 DLL</td>
<td>CWinApp对象的InitInstance 和 ExitInstance</td>
</tr>
<tr>
<td>MFC扩展 DLL</td>
<td>MFC DLL向导生成DllMain 函数</td>
</tr>
</table>
<p class="guanggao">　　对于MFC扩展DLL，系统会自动在工程中添加如下表所示的宏，这些宏为DLL和应用程序的编写提供了方便。像AFX_EXT_CLASS、AFX_EXT_API、AFX_EXT_DATA这样的宏，在DLL和应用程序中将具有不同的定义，这取决于_AFXEXT宏是否被定义。这使得在DLL和应用程序中，使用统一的一个宏就可以表示出输出和输入的不同意思。在DLL中，表示输出（因为_AFXEXT被定义，通常是在编译器的标识参数中指定/D_AFXEXT）；在应用程序中，则表示输入（_AFXEXT没有定义）。</p>

<p class="guanggao">
<table border="1" align="center" width="90%" cellPadding="2" cellSpacing="0">
<tr>
<td>宏</td>
<td>定义</td>
</tr>
<tr>
<td>AFX_CLASS_IMPORT</td>
<td>__declspec(dllexport)</td>
</tr>
<tr>
<td>AFX_API_IMPORT</td>
<td>__declspec(dllexport)</td>
</tr>
<tr>
<td>AFX_DATA_IMPORT</td>
<td>__declspec(dllexport)</td>
</tr>
<tr>
<td>AFX_CLASS_EXPORT</td>
<td>__declspec(dllexport)</td>
</tr>
<tr>
<td>AFX_API_EXPORT</td>
<td>__declspec(dllexport)</td>
</tr>
<tr>
<td>AFX_DATA_EXPORT</td>
<td>__declspec(dllexport)</td>
</tr>
<tr>
<td>AFX_EXT_CLASS</td>
<td>#ifdef _AFXEXT
　AFX_CLASS_EXPORT
#else
　AFX_CLASS_IMPORT</td>
</tr>
<tr>
<td>AFX_EXT_API</td>
<td>#ifdef _AFXEXT
　AFX_API_EXPORT
#else
　AFX_API_IMPORT</td>
</tr>
<tr>
<td>AFX_EXT_DATA</td>
<td>#ifdef _AFXEXT
　AFX_DATA_EXPORT
#else
　AFX_DATA_IMPORT</td>
</tr>
</table>
<p class="guanggao">　　<strong>6.2 MFC扩展DLL导出MFC派生类</strong></p>
　　在这个例子中，我们将产生一个名为“ExtDll”的MFC扩展DLL工程，在这个DLL中导出一个对话框类，这个对话框类派生自MFC类CDialog。

　　使用MFC向导生成MFC扩展DLL时，系统会自动添加如下代码：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>static AFX_EXTENSION_MODULE ExtDllDLL = { NULL, NULL };
extern "C" int APIENTRYDllMain( HINSTANCE hInstance, DWORD dwReason, LPVOID lpReserved )
{
　// Remove this if you use lpReserved

　UNREFERENCED_PARAMETER( lpReserved );

　//说明：lpReserved是一个被系统所保留的参数，对于隐式链接是一个非零值，对于显式链接值是零

　if (dwReason == DLL_PROCESS_ATTACH)
　{
　　TRACE0( "EXTDLL.DLL Initializing!\n" );
　　// Extension DLL one-time initialization
　　if ( !AfxInitExtensionModule( ExtDllDLL, hInstance ))
　　　return 0;
　　　// Insert this DLL into the resource chain
　　new CDynLinkLibrary( ExtDllDLL );
　}
　else if (dwReason == DLL_PROCESS_DETACH)
　{
　　TRACE0( "EXTDLL.DLL Terminating!\n" );
　　// Terminate the library before destructors are called
　　AfxTermExtensionModule( ExtDllDLL );
　}
　return 1; // ok
}</td>
</tr>
</table>
<p class="guanggao">　　这一段代码含义晦涩，我们需要对其进行解读：</p>
　　（1）上述代码完成MFC扩展DLL的初始化和终止处理；

　　（2）初始化期间所创建的 CDynLinkLibrary 对象使MFC扩展 DLL 可以将 DLL中的CRuntimeClass 对象或资源导出到应用程序；

　　（3）AfxInitExtensionModule函数捕获模块的CRuntimeClass 结构和在创建 CDynLinkLibrary 对象时使用的对象工厂（COleObjectFactory 对象）；

　　（4）AfxTermExtensionModule函数使 MFC 得以在每个进程与扩展 DLL 分离时（进程退出或使用AfxFreeLibrary卸载DLL时）清除扩展 DLL；

　　（5）第一条语句static AFX_EXTENSION_MODULE ExtDllDLL = { NULL, NULL };定义了一个AFX_EXTENSION_MODULE类的静态全局对象，AFX_EXTENSION_MODULE的定义如下：

<p class="guanggao">
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>struct AFX_EXTENSION_MODULE
{
　BOOL bInitialized;
　HMODULE hModule;
　HMODULE hResource;
　CRuntimeClass* pFirstSharedClass;
　COleObjectFactory* pFirstSharedFactory;
};</td>
</tr>
</table>
<p class="guanggao">　　由AFX_EXTENSION_MODULE的定义我们可以更好的理解（2）、（3）、（4）点。</p>
　　在资源编辑器中添加一个如图15所示的对话框，并使用MFC类向导为其添加一个对应的类CExtDialog，系统自动添加了ExtDialog.h和ExtDialog.cpp两个头文件。
<table border="0" align="center" width="90%">
<tr>
<td>
<p align="center"><img border="0" width="376" src="http://dev.yesky.com/imagelist/05/10/z44bxd805tf3.jpg" height="219" /><img border="0" width="376" src="http://dev.yesky.com/imagelist/05/10/z44bxd805tf3.jpg" height="219" />
图15 MFC扩展DLL中的对话框</td>
</tr>
</table>
<p class="guanggao">　　修改ExtDialog.h中CExtDialog类的声明为：</p>

<p class="guanggao">
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>class AFX_EXT_CLASS CExtDialog : public CDialog
{
　public:
　　CExtDialog( CWnd* pParent = NULL );
　　enum { IDD = IDD_DLL_DIALOG };
　protected:
　　virtual void DoDataExchange( CDataExchange* pDX );
　　DECLARE_MESSAGE_MAP()
};</td>
</tr>
</table>
<p class="guanggao">　　这其中最主要的改变是我们在class AFX_EXT_CLASS CExtDialog语句中添加了“AFX_EXT_CLASS”宏，则使得DLL中的CExtDialog类被导出。</p>
<p class="guanggao">　　<strong>6.3 MFC扩展DLL的加载</strong></p>
　　6.3.1 隐式加载

　　我们在6.2工程所在的工作区中添加一个LoadExtDllDlg工程，用于演示MFC扩展DLL的加载。在LoadExtDllDlg工程中添加一个如图16所示的对话框，这个对话框上包括一个“调用DLL”按钮。
<table border="0" align="center" width="90%">
<tr>
<td>
<p align="center"><img border="0" width="394" src="http://dev.yesky.com/imagelist/05/10/4o3b052d7377.jpg" height="138" /><img border="0" width="394" src="http://dev.yesky.com/imagelist/05/10/4o3b052d7377.jpg" height="138" />
图16 MFC扩展DLL调用工程中的对话框</td>
</tr>
</table>
　　在与图16对应对话框类实现文件的头部添加：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>// LoadExtDllDlg.cpp : implementation file
//#include "..\ExtDialog.h"
#pragma comment( lib, "ExtDll.lib" )

而“调用DLL”按钮的单击事件的消息处理函数为：

void CLoadExtDllDlg::OnDllcallButton()
{
　CExtDialog extDialog;
　extDialog.DoModal();
}</td>
</tr>
</table>
　　当我们单击“调用DLL”的时候，弹出了如图15的对话框。

　　为提供给用户隐式加载（MFC扩展DLL一般使用隐式加载，具体原因见下节），MFC扩展DLL需要提供三个文件：

　　（1）描述DLL中扩展类的头文件；

　　（2）与动态链接库对应的.LIB文件；

　　（3）动态链接库.DLL文件本身。

　　有了这三个文件，应用<a target="_blank" href="http://dev.yesky.com/" class="bluekey"><font color="#003399">程序</font></a>的开发者才可充分利用MFC扩展DLL。

　　6.3.2 显示加载

　　显示加载MFC扩展DLL应使用MFC全局函数AfxLoadLibrary而不是WIN32 API中的LoadLibrary。AfxLoadLibrary 最终也调用了 LoadLibrary这个API，但是在调用之前进行了线程同步的处理。

　　AfxLoadLibrary 的函数原型与 LoadLibrary完全相同，为：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>HINSTANCE AFXAPI AfxLoadLibrary( LPCTSTR lpszModuleName );</td>
</tr>
</table>
　　与之相对应的是，MFC 应用程序应使用AfxFreeLibrary 而非FreeLibrary 卸载MFC扩展DLL。AfxFreeLibrary的函数原型也与 FreeLibrary完全相同，为：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>BOOL AFXAPI AfxFreeLibrary( HINSTANCE hInstLib );</td>
</tr>
</table>
　　如果我们把上例中的“调用DLL”按钮单击事件的消息处理函数改为：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>void CLoadExtDllDlg::OnDllcallButton()
{
　HINSTANCE hDll = AfxLoadLibrary( "ExtDll.dll" );
　if(NULL == hDll)
　{
　　AfxMessageBox( "MFC扩展DLL动态加载失败" );
　　return;
　}　CExtDialog extDialog;
　extDialog.DoModal();
　AfxFreeLibrary(hDll);
}</td>
</tr>
</table>
　　则工程会出现link错误：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>LoadExtDllDlg.obj : error LNK2001: unresolved external symbol "__declspec(dllimport) public: virtual __thiscall CExtDialog::~CExtDialog(void)" (__imp_??1CExtDialog@@UAE@XZ)LoadExtDllDlg.obj : error LNK2001: unresolved external symbol "__declspec(dllimport) public: __thiscall CExtDialog::CExtDialog(class CWnd *)" (__imp_??0CExtDialog@@QAE@PAVCWnd@@@Z)</td>
</tr>
</table>
　　提示CExtDialog的构造函数和析构函数均无法找到！是的，对于派生MFC类的MFC扩展DLL，当我们要在应用程序中使用DLL中定义的派生类时，我们不宜使用动态加载DLL的方法。

　　<strong>6.4 MFC扩展DLL加载MFC扩展DLL</strong>

　　我们可以在MFC扩展DLL中再次使用MFC扩展DLL，但是，由于在两个DLL中对于AFX_EXT_CLASS、AFX_EXT_API、AFX_EXT_DATA宏的定义都是输出，这会导致调用的时候出现问题。

　　我们将会在调用MFC扩展DLL的DLL中看到link错误：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>error LNK2001: unresolved external symbol ….......</td>
</tr>
</table>
　　因此，在调用MFC扩展DLL的MFC扩展DLL中，在包含被调用DLL的头文件之前，需要临时重新定义AFX_EXT_CLASS的值。下面的例子显示了如何实现：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//临时改变宏的含义“输出”为“输入”#undef AFX_EXT_CLASS
#undef AFX_EXT_API
#undef AFX_EXT_DATA
#define AFX_EXT_CLASS AFX_CLASS_IMPORT
#define AFX_EXT_API AFX_API_IMPORT
#define AFX_EXT_DATA AFX_DATA_IMPORT

//包含被调用MFC扩展DLL的头文件

#include "CalledDLL.h"

//恢复宏的含义为输出

#undef AFX_EXT_CLASS
#undef AFX_EXT_API
#undef AFX_EXT_DATA
#define AFX_EXT_CLASS AFX_CLASS_EXPORT
#define AFX_EXT_API AFX_API_EXPORT
#define AFX_EXT_DATA AFX_DATA_EXPORT</td>
</tr>
</table>
　　<strong>6.5 MFC扩展DLL导出函数和变量</strong>

　　MFC扩展DLL导出函数和变量的方法也十分简单，下面我们给出一个简单的例子。

　　我们在MFC向导生成的MFC扩展DLL工程中添加gobal.h和global.cpp两个文件：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//global.h:MFC扩展DLL导出变量和函数的声明extern "C"
{
　int AFX_EXT_DATA total; //导出变量
　int AFX_EXT_API add( int x, int y ); //导出函数
}

//global.cpp:MFC扩展DLL导出变量和函数定义

#include "StdAfx.h"
#include "global.h"

extern "C" int total;
int add(int x,int y)
{
　total = x + y;
　return total;
}</td>
</tr>
</table>
　　编写一个简单的控制台<a target="_blank" href="http://dev.yesky.com/" class="bluekey"><font color="#003399">程序</font></a>来调用这个MFC扩展DLL：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>#include &lt;iostream.h&gt;
#include &lt;afxver_.h&gt;//AFX_EXT_DATA、AFX_EXT_API宏的定义在afxver_.h头文件中

#pragma comment ( lib, "ExtDll.lib" )
#include "..\global.h"

int main(int argc, char* argv[])
{
　cout &lt;&lt; add(2,3) &lt;&lt; endl;
　cout &lt;&lt; total;
　return 0;
}</td>
</tr>
</table>
　　运行程序，在控制台上看到：

　　5

　　5

　　另外，在Visual C++下建立MFC扩展DLL时，MFC DLL向导会自动生成.def文件。因此，对于函数和变量，我们除了可以利用AFX_EXT_DATA、AFX_EXT_API宏导出以外，在.def文件中定义导出也是一个很好的办法。与之相比，在.def文件中导出类却较麻烦。通常需要从工程生成的.map文件中获得类的所有成员函数被C++编译器更改过的标识符，并且在.def文件中导出这些“奇怪”的标识符。因此，MFC扩展DLL通常以AFX_EXT_CLASS宏直接声明导出类。

　　<strong>6.6 MFC扩展DLL的应用</strong>

　　上述各小节所举MFC扩展DLL的例子均只是为了说明某方面的问题，没有真实地体现“MFC扩展” 的内涵，譬如6.2派生自CDialog的类也不具备比CDialog更强的功能。MFC扩展DLL的真实内涵体现在它提供的类虽然派生自MFC类，但是提供了比MFC类更强大的功能、更丰富的接口。下面我们来看一个具体的例子（<a target="_blank" href="http://www.mydown.com/code/244/244724.html"><font color="#dd2222">单击此处下载本工程</font></a>）。

　　我们知道static控件所对应的CStatic类不具备设置背景和文本颜色的接口，这使得我们不能在对话框或其它用户界面上自由灵活地修改static控件的颜色风格，因此我们需要一个提供了SetBackColor和SetTextColor接口的CStatic派生类CMultiColorStatic。
　
　　这个类的声明如下：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>class AFX_EXT_CLASS CMultiColorStatic : public CStatic
{
　// Construction　public:
　　CMultiColorStatic();
　　virtual ~CMultiColorStatic();
　　// Attributes
　protected:
　　CString m_strCaption;
　　COLORREF m_BackColor;
　　COLORREF m_TextColor;
　　// Operations
　public:
　　void SetTextColor( COLORREF TextColor );
　　void SetBackColor( COLORREF BackColor );
　　void SetCaption( CString strCaption );

　　// Generated message map functions
　protected:
　　afx_msg void OnPaint();
　　DECLARE_MESSAGE_MAP()
};</td>
</tr>
</table>
　　在这个类的实现文件中，我们需要为它提供WM_PAINT消息的处理函数（这是因为颜色的设置依赖于WM_PAINT消息）：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>BEGIN_MESSAGE_MAP(CMultiColorStatic, CStatic)//{ {AFX_MSG_MAP(CMultiColorStatic)
　ON_WM_PAINT() //为这个类定义WM_PAINT消息处理函数
//}}AFX_MSG_MAP
END_MESSAGE_MAP()</td>
</tr>
</table>
　　下面是这个类中的重要成员函数：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//为CMultiColorStatic类添加“设置文本颜色”接口void CMultiColorStatic::SetTextColor( COLORREF TextColor )
{
　m_TextColor = TextColor; //设置文字颜色
}

//为CMultiColorStatic类添加“设置背景颜色”接口

void CMultiColorStatic::SetBackColor( COLORREF BackColor )
{
　m_BackColor = BackColor; //设置背景颜色
}

//为CMultiColorStatic类添加“设置标题”接口

void CMultiColorStatic::SetCaption( CString strCaption )
{
　m_strCaption = strCaption;
}

//重画Static，颜色和标题的设置都依赖于这个函数

void CMultiColorStatic::OnPaint()
{
　CPaintDC dc(this); // device context for painting
　CRect rect;
　GetClientRect( &amp;rect );
　dc.SetBkColor( m_BackColor );
　dc.SetBkMode( TRANSPARENT );
　CFont *pFont = GetParent()-&gt;GetFont();//得到父窗体的字体
　CFont *pOldFont;
　pOldFont = dc.SelectObject( pFont );//选用父窗体的字体
　dc.SetTextColor( m_TextColor );//设置文本颜色
　dc.DrawText( m_strCaption, &amp;rect, DT_CENTER );//文本在Static中央
　dc.SelectObject( pOldFont );
}</td>
</tr>
</table>
　　为了验证CMultiColorStatic类，我们制作一个基于对话框的应用程序，它包含一个如图17所示的对话框。该对话框上包括一个static控件和三个按钮，这三个按钮可分别把static控件设置为“红色”、“蓝色”和“绿色”。
<table border="0" align="center" width="90%">
<tr>
<td>
<p align="center"><img border="0" width="333" src="http://dev.yesky.com/imagelist/05/10/ui4t07vro853.jpg" height="166" /><img border="0" width="333" src="http://dev.yesky.com/imagelist/05/10/ui4t07vro853.jpg" height="166" />
图17 扩展的CStatic类调用演示</td>
</tr>
</table>
　　下面看看应如何编写与这个对话框对应的类。

　　包含这种Static的对话框类的声明如下：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>#include "..\MultiColorStatic.h"
#pragma comment ( lib, "ColorStatic.lib" )// CCallDllDlg dialog

class CCallDllDlg : public CDialog
{
　public:
　　CCallDllDlg(CWnd* pParent = NULL); // standard constructor
　　enum { IDD = IDD_CALLDLL_DIALOG };
　　CMultiColorStatic m_colorstatic; //包含一个CMultiColorStatic的实例
　protected:
　　virtual void DoDataExchange(CDataExchange* pDX);//DDX/DDV support
　　HICON m_hIcon;

　// Generated message map functions
　//{ {AFX_MSG(CCallDllDlg)

　virtual BOOL OnInitDialog();
　afx_msg void OnSysCommand(UINT nID, LPARAM lParam);
　afx_msg void OnPaint();
　afx_msg HCURSOR OnQueryDragIcon();
　afx_msg void OnRedButton();
　afx_msg void OnBlueButton();
　afx_msg void OnGreenButton();
//}}AFX_MSG
DECLARE_MESSAGE_MAP()
};</td>
</tr>
</table>
　　下面是这个类中与使用CMultiColorStatic相关的主要成员函数：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>void CCallDllDlg::DoDataExchange(CDataExchange* pDX)
{
　CDialog::DoDataExchange(pDX);
　//{ {AFX_DATA_MAP(CCallDllDlg)
　　DDX_Control(pDX, IDC_COLOR_STATIC, m_colorstatic);
　//使m_colorstatic与IDC_COLOR_STATIC控件关联
　//}}AFX_DATA_MAP
}BOOL CCallDllDlg::OnInitDialog()
{
　…
　// TODO: Add extra initialization here

　// 初始static控件的显示

　m_colorstatic.SetCaption("最开始为黑色");
　m_colorstatic.SetTextColor(RGB(0,0,0));
　return TRUE; // return TRUE unless you set the focus to a control
}

//设置static控件文本颜色为红色

void CCallDllDlg::OnRedButton()
{
　m_colorstatic.SetCaption( "改变为红色" );
　m_colorstatic.SetTextColor( RGB( 255, 0, 0 ) );
　Invalidate( TRUE ); //导致发出WM_PAINT消息
}

//设置static控件文本颜色为蓝色

void CCallDllDlg::OnBlueButton()
{
　m_colorstatic.SetCaption( "改变为蓝色" );
　m_colorstatic.SetTextColor( RGB( 0, 0, 255 ) );
　Invalidate( TRUE ); //导致发出WM_PAINT消息
}

//设置static控件文本颜色为绿色

void CCallDllDlg::OnGreenButton()
{
　m_colorstatic.SetCaption( "改变为绿色" );
　m_colorstatic.SetTextColor( RGB(0,255,0) );
　Invalidate( TRUE ); //导致发出WM_PAINT消息
}</td>
</tr>
</table>
　　至此，我们已经讲解完成了所有类型的动态链接库，即非MFC DLL、MFC规则DLL和MFC扩展DLL。下一节将给出DLL的三个工程实例，与读者朋友们共同体会DLL的应用范围和使用方法。
