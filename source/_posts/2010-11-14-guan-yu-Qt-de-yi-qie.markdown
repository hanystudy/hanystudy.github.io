---
layout: post
status: publish
published: true
title: 关于Qt的一切
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 263
wordpress_url: http://www.hanyi.name/blog/?p=263
date: 2010-11-14 12:49:43.000000000 +08:00
categories:
- Qt
tags:
- qt
comments: []
---
 Qt在中国程序员人群中兴起较早，Trolltech亦对中国市场颇为重视。2005年，Trolltech在北京成立代表处，旨在向中国用户提供Qtopia相关的销售服务、培训以及技术支持。同年Qt官网也首次加入双语页面——英语和汉语两种版本（而非现在的多国语言）。

 不甚了解Qt的同学可能会对Qt的功能产生怀疑：Qt难道不是简单的GUI框架吗？为了能更好的解答这个问题，我特从ArsTechnica.com编译了一篇由Ryan Paul撰写的关于深度透视Qt4.4的文章，尽管Qt开发团队刚刚发布了4.7.1的新版本，我依然认为Qt今后的发展趋势将是沿着2005年夏天Qt4.0发布所带来革命性影响的延续，这也是软件工程理论与实践证明目前最好的技术框架之一。同时文后我们也将推荐其它几篇Qt阶段性介绍的文章，值得注意的是，作者原文较为冗长，我们将分两期刊出，以飨读者。

<strong> Troll精品:深度解析Qt4.4（原题:Troll treasure:an in-depth look at Qt4.4）</strong> 

 <strong>文/图 Ryan Paul 编译 mp77</strong>

 Trolltech刚刚宣布了Qt4.4的官方发布版（译注：这里是指2008年5月），这是对Trolltech出品的最受欢迎的、跨平台的应用程序框架的一次备受期待的更新。Qt多许可证分发的模式允许其即可用于开源项目，亦可进行专有的商业开发。她广泛应用于Linux平台，并为KDE桌面环境提供了基础工具包。同时，Qt亦用于许多商业软件如Skype,GoogleEarth以及Adobe Photoshop套件中。

 Qt4.4最受瞩目的更新内容主要包括了多媒体抽象层、基于WebKit的HTML渲染器、一种新的并发框架以及支持基于工具包内的drawing canvas技术渲染工具组件。这也是Qt首次包含对Windows CE和Windows Mobile的支持。

 去年在测试Qt4.4预释放版时我们曾经向您提供了一些早期的功能预览。在目前开发工作已经全部完成、功能较完备的情况下，我们进一步审视了Qt4.4。最终，我们进行了大量测试以探究这些新技术的实现原理。同时，我们也和Trolltech的首席执行官Benoit Schillings一起讨论了Qt4.4，Benoit Schillings向我们分享了一些关于Qt4.4的技术内幕，并且给出了Qt未来版本趋势的一些建议。

 <strong>桥接桌面与移动设备的鸿沟</strong>

 Qt支持所有主流的桌面操作系统，包括Linux、Mac OS X和Windows。她使用本地API渲染工具组件，这就意味着大多数的Qt应用程序具有基于操作系统的外观一致性。Trolltech同时提供了Qtopia——一个基于Linux移动操作系统的综合开发平台，Qtopia亦被广泛应用于如Sony Mylo、一些Motorola mobile phone handsets中。Qt4.4通过引入对Windows CE的完整支持扩充了自身的可移植性。开发人员现在可以用Qt编写任何一段应用程序代码，就可以完全移植到任何所支持的操作系统中去。

 Qt为Windows CE提供了大量功能，且能很好地适用于创建基于图形的触摸式用户界面。开发人员声称Qt表现出良好的性能和对本地外观和平台体验的强力支持。Trolltech同时发布了可以使Qt与Visual Studio紧密集成的工具，这样能够让现有Windows CE程序员轻松适应这个应用程序框架。

 <a href="null"><img class="aligncenter" title="qt44structure" src="http://media.arstechnica.com/news.media/450/qt44structure.png.png" alt="" width="450" height="283" /></a>

 Qt支持Windows CE所带来的潜在好处是，其允许程序员能够将现有的KDE或Qt程序移植到更多的环境中，这将大大扩展目前开源软件在操作系统支持方面的生态系统。我确信自己会非常乐意在Windows Mobile设备中运行基于Qtopia的软件如FBReader，Trolltech的开发人员也已经在实验性地将相对复杂的KDE4应用程序移植到Windows Mobile中。例如，Thomas Hartmann在上个月撰写的<a href="http://labs.trolltech.com/blogs/2008/04/04/marble-running-on-windows-ce/" target="_blank">博客</a>展示了一种基于KDE4软件的移植，这个软件是一种复杂地图系统绘制系统，它能够在多种Windows设备中流畅地运行。

<a href="null"><img class="aligncenter" title="qtmarble" src="http://media.arstechnica.com/news.media/450/qtmarble.png" alt="" width="450" height="309" /></a>

这种来自Qt更高级别的可移植性吸引了移动设备巨头Nokia，后者正在进行对Trolltech的收购进程中（译注:收购已于2008年结束）。

 这项交易仍然处于欧盟审查之中。今年早先的一项声明指出，Nokia宣称将力促Qt成为一种跨平台的软件开发平台，它将可以在大多数移动和桌面平台上运行。 Nokia也已计划将Qt引入S60和Maemo移动操作系统的（译注：这项计划目前已实现）。

<strong> （未完待续）</strong>
