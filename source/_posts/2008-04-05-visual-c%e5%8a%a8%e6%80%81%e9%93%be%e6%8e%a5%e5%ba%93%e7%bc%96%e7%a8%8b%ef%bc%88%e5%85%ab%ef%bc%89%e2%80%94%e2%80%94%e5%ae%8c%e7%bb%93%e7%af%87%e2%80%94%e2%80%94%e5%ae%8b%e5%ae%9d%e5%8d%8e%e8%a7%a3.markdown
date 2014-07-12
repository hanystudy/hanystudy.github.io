---
layout: post
status: publish
published: true
title: Visual C++动态链接库编程（八）——完结篇——宋宝华解疑
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 34
wordpress_url: http://www.hanyi.name/blog/?p=34
date: 2008-04-05 15:23:32.000000000 +08:00
categories:
- 转载
tags:
- dll
- vc++
comments:
- id: 7543
  author: phentermine buy phentermine online no
  author_email: amrsgmqo@ujkvrjje.com
  author_url: http://www.azcentral.com/members/Blog/medecinesupply/84980
  date: !binary |-
    MjAxMC0wNi0wNCAwNTozNzo0OSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMTozNzo0OSArMDgwMA==
  content: <a href="http://community.miragespeakers.com/members/Soma-without-rx.aspx"
    rel="nofollow">soma greek</a> , [url="http://community.miragespeakers.com/members/Soma-without-rx.aspx"]soma
    greek[/url] , http://community.miragespeakers.com/members/Soma-without-rx.aspx
    soma greek ,
- id: 7651
  author: order phentermine high quality pharmacy index
  author_email: vgphiizi@pqodrnav.com
  author_url: http://www.esnips.com/user/jadenmariahy
  date: !binary |-
    MjAxMC0wNi0wNCAwNzo0MDozNiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMzo0MDozNiArMDgwMA==
  content: <a href="http://trailfire.com/generic_Xanax" rel="nofollow">what is xanax
    bars</a> , [url="http://trailfire.com/generic_Xanax"]what is xanax bars[/url]
    , http://trailfire.com/generic_Xanax what is xanax bars ,
- id: 7688
  author: ayurvedic doctor soma
  author_email: zugnsgmo@pzelqydu.com
  author_url: http://connect.gulflive.com/user/nathanielariannay/index.html
  date: !binary |-
    MjAxMC0wNi0zMCAyMjoxNTozNiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0zMCAxNDoxNTozNiArMDgwMA==
  content: It is the coolest site, keep so!
- id: 7890
  author: adipex diet pill resources
  author_email: tztkhdqn@phthmhkt.com
  author_url: http://connect.lehighvalleylive.com/user/adipexw138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMDo0MToxNyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxMjo0MToxNyArMDgwMA==
  content: I bookmarked this link. Thank you for good job!
- id: 8852
  author: cheap phentermine 37.5mg without a prescription
  author_email: qydwepjj@ovyaxhvj.com
  author_url: http://www.freerider-tracks.com/account/phentermine-5156m
  date: !binary |-
    MjAxMC0xMC0xNiAyMTo1NzozNCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0xMC0xNiAxMzo1NzozNCArMDgwMA==
  content: Great site. Good info.
---
　1.关于文章的获取

　　许多读者发来e-mail询问本系列文章的相关事宜，如：

　　（1） 是否已出版？

　　（2） 哪里可以<a target="_blank" href="http://www.mydown.com/" class="bluekey"><font color="#003399">下载</font></a>打包版？

　　（3） 哪里可以下载笔者的其它文章？
<p class="guanggao">　　还有一些读者对日前笔者在天极网发表的《<a target="_blank" href="http://soft.yesky.com/lesson/188/2023188.shtml"><font color="#1111ee">C语言嵌入式系统编程修炼之道</font></a>》非常喜爱，给予了热情洋溢的赞扬，询问笔者能否继续创作嵌入式编程方面的文章。</p>
　　对于这些问题，统一作答如下：

　　（1）本系列文章暂时尚未出版；

　　（2）您可以在天极网<a target="_blank" href="http://product.yesky.com/software/" class="bluekey"><font color="#003399">软件</font></a>频道下载笔者的多数拙作。另外，我也将不定期将这些文章上传到我的博客（ http://blog.donews.com/21cnbao/）。所有文章中的例程源代码均经过亲手调试，验证无误；

　　（3）就嵌入式系统开发，笔者将继续进行此方面的创作，新近将推出《基于嵌入式实时OS VxWorks的多任务<a target="_blank" href="http://dev.yesky.com/" class="bluekey"><font color="#003399">程序</font></a>设计》及《领悟：从Windows多线程到VxWorks的多任务》。

　　非常感谢读者朋友对这些文章的喜爱，在下将竭尽所能地为您提供更多的好文章。

　　2.关于DLL的疑问

　　你好，看了你写的"VC++ DLL编程深入浅出"，特别有收获。 只是有个地方我老搞不明白，就是用DLL导出全局变量时，指定了.lib的路径（#pragma comment(lib,"dllTest.lib")），那么.dll的文件的路径呢，我尝试着把.dll文件移到别的地方程序就无法正常运行了，请问.dll在这里怎么指定。

　　希望您能在百忙中抽空给我解答一下，不胜感激！

　　一位编程爱好者

　　回答：

　　Windows按下列顺序搜索DLL：

　　（1）当前进程的可执行模块所在的目录；

　　（2）当前目录；

　　（3）Windows 系统目录，通过GetSystemDirectory 函数可获得此目录的路径；

　　（4）Windows 目录，通过GetWindowsDirectory 函数可获得此目录的路径；

　　（5）PATH 环境变量中列出的目录。

　　因此，隐式链接时，DLL文件的路径不需要指定也不能指定，系统指定按照1～5的步骤寻找DLL，但是对应的.lib文件却需要指定路径；如果使用Windows API函数LoadLibrary动态加载DLL，则可以指定DLL的路径。

　　你好,我是一位C++初学者,我在PCONLINE看了教学之后,受益不浅。我想问一下能否在DLL里使用多线程?MSDN上用#using &lt;mscorlib.dll&gt;这个指令之后实现了多线程,不过好象不支持DLL..

　　请问有什么办法支持制作多线程DLL??能否给一个源码来?

　　回答：

　　在DLL中可以处理多线程，WIN32对于多线程的支持是<a target="_blank" href="http://os.yesky.com/" class="bluekey"><font color="#003399">操作系统</font></a>本身提供的一种能力，并不在于用户编写的是哪一类程序。即便是一个控制台程序，我们都可以使用多线程：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>#include &lt;stdio.h&gt;
#include &lt;windows.h&gt;
void ThreadFun(void)
{
　while(1)
　{
　　printf( "this is new thread\n" );
　　Sleep( 1000 );
　}
}
int main()
{
　DWORD threadID;
　CreateThread( NULL, 0, (LPTHREAD_START_ROUTINE)ThreadFun, NULL, 0, &amp;threadID );
　while(1)
　{
　　printf( "this is main thread\n" );
　　Sleep( 1000 );
　}
}</td>
</tr>
</table>
　　观察程序运行的结果为在控制台窗口上交替输出this is main thread、this is new thread。

　　我们来看下面的一个多线程DLL的例子。

　　DLL程序提供一个接口函数SendInit，在此接口中启动发送线程SendThreadFunc，在这个线程的对应工作函数中我们使用原始套接字socket发送报文。参考微软出版的经典书籍《Windows核心编程》，我们发现，不宜在DLL被加载的时候（即进程绑定时）启动一个新的线程。

　　这个线程等待一个CEvent事件（用于线程间通信），应用程序调用DLL中的接口函数SendMsg( InterDataPkt sendData )可以释放此事件。下面是相关的源代码：

　　（1）发送报文线程入口函数
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>///////////////////////////////////////////////////////////////////////////
//函数名：SendThreadFunc
//函数功能：发送报文工作线程入口函数，使用UDP协议
////////////////////////////////////////////////////////////////////////////
DWORD WINAPI SendThreadFunc( LPVOID lpvThreadParm )
//提示：对于线程函数应使用WINAPI声明，WINAPI被宏定义为__stdcall
{
　/* 创建socket */
　sendSock = socket ( AF_INET, SOCK_DGRAM, 0 );
　if ( sendSock == INVALID_SOCKET )
　{
　　AfxMessageBox ( "Socket创建失败" );
　　closesocket ( recvSock );
　}　/* 获得目标节点端口与地址 */
　struct sockaddr_in desAddr;
　desAddr.sin_family=AF_INET;
　desAddr.sin_port=htons( DES_RECV_PORT ); //目标节点接收端口
　desAddr.sin_addr.s_addr = inet_addr( DES_IP );

　/* 发送数据 */
　while(1)
　{
　　WaitForSingleObject( hSendEvent, 0xffffffffL );//无限等待事件发生
　　ResetEvent( hSendEvent );

　　sendto( sendSock, (char *)sendSockData.data, sendSockData.len, 0, (struct sockaddr*)&amp;desAddr, sizeof(desAddr) );
　}
　return -1;
}</td>
</tr>
</table>
　　（2）MFC规则DLL的InitInstance函数
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>/////////////////////////////////////////////////////////////////////////////
// CMultiThreadDllApp initialization
BOOL CMultiThreadDllApp::InitInstance()
{
　if ( !AfxSocketInit() ) //初始化socket
　{
　　AfxMessageBox( IDP_SOCKETS_INIT_FAILED );
　　return FALSE;
　}
　return TRUE;
}</td>
</tr>
</table>
　　（3）启动发送线程
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>////////////////////////////////////////////////////////////////////////////////
//函数名：SendInit
//函数功能：DLL提供给应用程序调用接口，用于启动发送线程
/////////////////////////////////////////////////////////////////////////////
void SendInit(void)
{
　hSendThread = CreateThread( NULL, 1000, SendThreadFunc, this, 1, &amp;uSendThreadID );
}</td>
</tr>
</table>
　　（4）SendMsg函数
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>////////////////////////////////////////////////////////////////////////////////
//函数名：SendMsg
//函数功能：DLL提供给应用程序调用接口，用于发送报文
/////////////////////////////////////////////////////////////////////////////
extern "C" void WINAPI SendMsg( InterDataPkt sendData )
{
　sendSockData = sendData;
　SetEvent( hSendEvent ); //释放发送事件
}</td>
</tr>
</table>
　　以上程序仅仅是一个简单的例子，其实在许多工程应用中，我们经常看到这样的处理方式。这个DLL对用户而言仅仅使一个简单的接口函数SendMsg，对调用它的应用程序屏蔽了多线程的技术细节。与之类似，MFC提供的CSocket类在底层自己采用了多线程机制，所以使我们免去了对多线程的使用。

　　您好,看了您的DLL文章，发现导出函数可以直接用_declspec(dllexport)声明或在.def文件中定义，变量的导出也一样。我想知道类是否也可以在.def文件中导出？您的文章中只讲了在类前添加_declspec(dllexport)导出类的方法。请您指教！

　　回答：

　　一般我们不采用.def文件导出类，但是这并不意味着类不能用.def文件导出类。

　　使用Depends查看连载2的"导出类"例程生成的DLL，我们发现其导出了如图21的众多"怪"symbol，这些symbol都是经过编译器处理的。因此，为了以.def文件导出类，我们必须把这些"怪"symbol全部导出，实在是不划算啊！所以对于类，我们最好直接以_declspec(dllexport)导出。
<table border="0" align="center" width="90%">
<tr>
<td>
<p align="center"><img border="0" width="483" src="http://dev.yesky.com/imagelist/05/10/t5u84cjh02yz.jpg" height="160" /><img border="0" width="483" src="http://dev.yesky.com/imagelist/05/10/t5u84cjh02yz.jpg" height="160" />
图1 导出类时导出的symbol</td>
</tr>
</table>
　　您好,看了您的DLL文章，知道怎么创建DLL了，但是面对一个具体的工程，我还是不知道究竟应该把什么做成DLL？您能给一些这方面的经验吗？

　　回答：

　　DLL一般用于软件模块中较固定、较通用的可以被复用的模块，这里有一个非常好的例子，就是豪杰超级解霸。梁肇新大师把处理视频和音频的算法模块专门做成了两个DLL，供超级解霸的用户界面GUI程序调用，实在是DLL<a target="_blank" href="http://design.yesky.com/" class="bluekey"><font color="#003399">设计</font></a>的模范教程。所谓"万变不离其宗"，超级解霸的界面再cool，用到的还是那几个DLL！具体请参考《编程高手箴言》一书。

　　您好,您的DLL文章讲的都是Windows的，请问Linux操作系统上可以制作DLL吗？如果能，和Windows有什么不一样？谢谢！

　　回答：

　　在Linux操作系统中，也可以采用动态链接技术进行软件设计，但与Windows下DLL的创建和调用方式有些不同。

　　Linux操作系统中的共享对象技术（Shared Object）与Windows里的DLL相对应，但名称不一样，其共享对象文件以.so作为后缀。与Linux共享对象技术相关的一些函数如下：

　　(1)打开共享对象，函数原型：
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//打开名为filename共享对象，并返回操作句柄；
void *dlopen (const char *filename, int flag);</td>
</tr>
</table>
　　(2)取函数地址，函数原型:
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//获得接口函数地址
void *dlsym(void *handle, char *symbol);</td>
</tr>
</table>
　　(3)关闭共享对象，函数原型:
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//关闭指定句柄的共享对象
int dlclose (void *handle);</td>
</tr>
</table>
　　(4)动态库错误函数，函数原型:
<table border="1" bgColor="#e3e3e3" align="center" width="90%" borderColor="#cccccc">
<tr>
<td>//共享对象操作函数执行失败时，返回出错信息
const char *dlerror(void);</td>
</tr>
</table>
　　从这里我们分明看到Windows API――LoadLibrary、FreeLibrary和GetProcAddress的影子！又一个"万变不离其宗"！

　　本系列文章的连载暂时告一段落，您可以继续给笔者发送email（mailto：21cnbao@21cn.com）讨论DLL的编程问题。对于文中的错误和纰漏，也热诚欢迎您指正。
