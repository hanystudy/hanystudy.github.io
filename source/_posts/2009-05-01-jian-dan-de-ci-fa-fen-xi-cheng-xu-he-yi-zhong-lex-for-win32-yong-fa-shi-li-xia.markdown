---
layout: post
status: publish
published: true
title: 简单的词法分析程序和一种lex for win32用法示例（下）
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 156
wordpress_url: http://www.hanyi.name/blog/?p=156
date: 2009-05-01 20:21:18.000000000 +08:00
categories:
- 编译技术
tags:
- 编译原理
- 词法分析
- lex
- yacc
comments:
- id: 7638
  author: what does tramadol hcl do
  author_email: siqyekiq@mkrpymmy.com
  author_url: http://ozgirl.tv/profile/Tramadolwithoutrx
  date: !binary |-
    MjAxMC0wNi0wNCAwNzoyMzo1MSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMzoyMzo1MSArMDgwMA==
  content: <a href="http://medecinesupply.socialgo.com/magazine/read/viagra-with-no-prescription_14.html"
    rel="nofollow">how to take viagra</a> , [url="http://medecinesupply.socialgo.com/magazine/read/viagra-with-no-prescription_14.html"]how
    to take viagra[/url] , http://medecinesupply.socialgo.com/magazine/read/viagra-with-no-prescription_14.html
    how to take viagra ,
- id: 7710
  author: tweet-attacks-review
  author_email: Mohar@gmail.com
  author_url: http://tweetattacksreviews.info
  date: !binary |-
    MjAxMC0wOC0wNSAxNzo1NjozMiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNSAwOTo1NjozMiArMDgwMA==
  content: nice  article   thank you
- id: 8649
  author: tramadol prescribed by vet
  author_email: pmoxxmgi@azslboky.com
  author_url: http://connect.lehighvalleylive.com/user/tramadolu138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMjo1NToxNyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxODo1NToxNyArMDgwMA==
  content: Very interesting site. Hope it will always be alive!
---
 前面介绍了如何使用lex写一个词法分析器，为了证明上述程序的正确性，需生成与它对应的c语言代码，并编译成可执行程序。unix下使用这些工具非常简单（以至于两行commands就可以完成工作），在此就不做详细说明，接下来我们来面对ms留给我们的烂摊子......

 为了证明在win32下利用lex生成词法分析器有多复杂，我们选用parser generator 2和vc++6.0组合向大家展示ms强大的化神奇为腐朽的力量（依然不推荐win32用户使用vs平台进行此项工作）。

 parser generator可以在以下网址免费获取：<a href="http://www.bumblebeesoftware.com/downloads.htm">http://www.bumblebeesoftware.com/downloads.htm</a>

 vc++6.0？别告诉我你不知道怎么获取......

 首先启动pg2,要进行针对vc++6.0的配置工作:

 选择menu下的project-&gt;LibBuilder,Complers选则Visual C++(32-bit),并点击compiler properties,这时在Options内作如下设置:

Version:6

 Unicode:true

 treat wchar_t as Built-in type:False

 Compiler Bin directory: C:\PROGRAM FILES\MICROSOFT VISUAL STUDIO\VC98\BIN

 Compiler Bin directory(2):C:\PROGRAM FILES\MICROSOFT VISUAL STUDIO\COMMON\MSDEV98\BIN

 Compiler Include directory: C:\PROGRAM FILES\MICROSOFT VISUAL STUDIO\VC98\INCLUDE

 Compiler Include directory(2):C:\PROGRAM FILES\MICROSOFT VISUAL STUDIO\VC98\MFC\INCLUDE

 Compiler Library directory:C:\PROGRAM FILES\MICROSOFT VISUAL STUDIO\VC98\LIB

 Compiler Library directory(2):C:\PROGRAM FILES\MICROSOFT VISUAL STUDIO\VC98\MFC\LIB

 (注: 请读者按vc实际安装目录而定)选择完毕后点击OK,在父窗口中选择Build生成供vc使用的库文件.

 ......

 经过漫长等待库文件终于生成了...现在回到主界面,选择project-&gt;parserWizard,填入自己的lex工程名,选择vc++6.0(32-bit),进入下一步.Files选lex file only,其它均保持默认即可.填入lex源文件名后即可选择complete.

 这时程序会自动生成lex文件的框架,如果在前面的设置中还选择了create main function,文本中还会有几行简单的main函数.但是该文本对我们没有任何意义...直接将前文所述的源代码覆盖至此即可.

 现在选择project-&gt;compile file,build后就会生成lex源码对应的c语言source和include文件了.接下来启动vc++6.0进行相关配置.

 首先点击Tools-&gt;Options,切换至directory选项卡,分别在show directories for中的Include、Library和Source选项中添加如下信息（每项一条）：

C:\PROGRAM FILES\PARSER GENERATOR 2\CPP\INCLUDE

C:\PROGRAM FILES\PARSER GENERATOR 2\CPP\LIB\MSVC32

C:\PROGRAM FILES\PARSER GENERATOR 2\CPP\SOURCE

（注：应按pg的实际安装路径而定）现在新建win32 console application工程，并点击Project-&gt;Settings选项，Settings for先选择win32 debug。然后在C/C++选项卡的Proprocessor Definitions选项中添入,YYDEBUG，切换至Link选项卡在Object/library modules中添入yld.lib ylmtd.lib ylmtrd.lib。

 将Settings for切换至win32 Release重复上述操作，就完成了整个前期的所有配置工作......

 在工程中添加前面生成的source和include文件，直接build即可。现在，我们就可以按照PL/0的基本关键词写出一些简单的程序并加以词法分析了（事实证明这时的编译功能还十分微弱，但却是向前迈出一大步了）。
