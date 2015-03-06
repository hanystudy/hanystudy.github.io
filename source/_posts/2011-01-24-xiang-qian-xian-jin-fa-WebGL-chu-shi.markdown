---
layout: post
status: publish
published: true
title: 向前线进发：WebGL初试
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 276
wordpress_url: http://www.hanyi.name/blog/?p=276
date: 2011-01-24 22:29:16.000000000 +08:00
categories:
- 可视化
tags:
- webgl
- web3d
comments: []
---
非常幸运的是，我们生活在一个正处于高速变革中的时代里。新的技术、理念乃至行业标准即将定稿，虽然我们无从参与任何权威文件的最终发布，却能全程体验这一史无前例的飞速发展的全貌。

从1996年VRML1.0发布时起，Web3D就追赶上了互联网高速发展的黄金时期。然而端上的资源匮乏严重制约了VRML前进的脚步，这期间又经历了针对Java Applet的普遍质疑。如今，除了Princeton 3D Model Search Engine闻名于学术界以外，似乎就很难再找到如此振奋人心的killer platform了。 2004年，w3c开始着手筹备html5。一系列高级功能，如表单控制、应用程序接口(APIs)、多媒体、结构化和语义化等计划被引入新的标准。由于这些全新概念的引入（实际上在pc领域这些早已算不上是“全新”了），Web3D的支持者们终于看到了新的突破点：直接在浏览器中引入3D API，再通过一系列底层优化，甚至采用硬件加速来提升效率和稳定性。这次Khronos Group终于可以携自己的OpenGL ES 2.0大展身手了。本文的目的旨在向读者介绍WebGL当前的标准制定和研发进度，并提供一些关于平台demo的建议。本文剩余的内容是基于learningwebgl.com整合而成的，在此对相关作者表示感谢。

首先要说明的是，要想率先体验WebGL所带来全新的视觉冲击力，你至少需要安装一款支持它的浏览器。幸运的是，目前除了微软的IE系列，几乎所有主流浏览器的开发版本都支持WebGL。那么我们如今的问题就变成了：如何为你现有的系统配置一款WebGL支持良好的浏览器。

对于windows操作系统而言，如果你拥有一块性能不差的ATI或Nvidia显卡，那么试着安装Firefox或是Chromium，它们也是目前win系统上最好的浏览器。如果你的机器只配置了一套来自Intel的集成图形芯片组（目前普通笔记本电脑普遍集成了这种配置），那么情况可能变得有些糟：这是因为Intel的图形硬件驱动并不能很好地支持OpenGL，而事实上目前的WebGL主要是基于OpenGL的（尽管这种情况在今后将会有所改善）。在这种情况下即使你安装了Firefox或Chromium，浏览器可能还是不会正常地运行WebGL程序——当然对此有更好的解决办法，我们可以使用带软件渲染的Firefox浏览器（这种软件渲染方式即把针对3D图形的运算全部交由CPU执行而非显卡，这将大大降低WebGL的运行效率......但至少我们很大程度上解决了这一问题）。

在Macintosh系统中，如果你采用了Snow Leopard操作系统（版本号OS X 10.6），那么我建议你使用WebKit的开发版本，它将是Safari的替代版本。如果你使用的是Leopard （版本号OS X 10.5），你可能无法运行WebKit开发版，那么Firefox或Chromium就是你最好的选择。当然，Snow Leopard的用户也可以采用上述两款浏览器。遗憾的是，我目前尚无法确定WebKit在更旧版本的Mac操作系统上的运行情况。

如果你正在使用Linux，那么解决方案可能和windows差不多。但需要注意，如果你的集成显卡不能很好地支持WebGL，那么在采用软件渲染之前，你需要安装Mesa，然后再安装最新版本的Firefox即可。

由于笔者的配置环境是ATI HD5650/Windows 7 ultimate，故我们以chromium浏览器的安装为例介绍这一流程。

目前chrome的开发者强烈建议用户使用Chromium的nightly build版本，它可以看成是chrome的一种实验版本，由于nightly build版本的存在使得其发展速度要全面领先于chrome，后者的缺点主要是功能较前者而言欠缺一些，但运行更加稳定。chromium的安装因操作系统的差异有所区别，这里我们仅给出windows操作系统的chromium安装流程。

1、首先下载chrome-win32.zip并解压之；

2、运行chrome.exe;

3、开始在google上搜索WebGL纷繁复杂的demo欣赏吧！

值得一提的是，本站的首页采用了WebGL的展示技术，如果你的浏览器不支持WebGL，今后可能就无法正常浏览这里的全部信息了。
