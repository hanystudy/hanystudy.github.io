---
layout: post
status: publish
published: true
title: 啃奶来了—Kinect for Windows抢先体验
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 307
wordpress_url: http://www.hanyi.name/blog/?p=307
date: 2011-05-14 20:46:15.000000000 +08:00
categories:
- Kinect
tags: []
comments: []
---
如果说微软将“被迫”开放Kinect for Windows SDK，无疑这次是PrimeSense立了首功。然而要不是我冒着生命危险在鸟不拉屎的地方蹲守了近十天，终于在一个学院林立的院落里拿下Kinect for Taiwan Only，并跋涉1200公里侥幸逃生的话，本文的出炉可能还要推迟一个月左右。总之无论如何，感谢MS，感谢PS，感谢万恶的铁道部，我终于活着且独立完成了这篇文章。

Kinect到底好不好玩，我不知道。不过我知道为了测试一个hand tracker手臂举得酸痛，然而结果就像是我们数年前的理想如今突然变成了现实，而且这种久逢甘霖的冲动仍在持续，套用某牛逼CEO创业时的一句话：“说实话，我们并不清楚它未来将会变成什么。”由于微软将于2011年5月16日正式开放Kinect for Windows SDK beta，尽管是non-commercial use，但对我们来说已经足矣。本文在某种程度上是基于已有hacker的经验谈，基本框架一般是OpenNI+SensorKinect+NITE这样的半官方组合，而要想全面入门体感开发还是需要先研读一下OpenNI UserGuide这种小部头。

下面是啃奶来自NiViewer的第一张深度对比图：

[singlepic id=30 w=320 h=240 mode=watermark float=center]

根据PS的说法，自然交互（Natural Interaction）就是一种基于声音和视觉等人类体感信息的人机交互方式。目前看来，NI设备完全可以取代如遥控器、鼠标等远程控制外设。当前日常生活中常见的自然交互方式包括：

演说以及口令识别，NI设备通过声音接收装置接收语音指令；

手势识别，通过预设计一定的交互范型，人们可以通过简单的手势控制一般设备，如卧室的光照强度；

躯体运动跟踪，NI设备通过识别当前图像中的完整人类躯体，快速分析躯体骨架信息，并将实际躯体运动姿态转换为骨架运动信息。

值得注意的是，前面的图中展示了一张Kinect的深度对比图，也就是说大多数NI设备通过技术手段可以感应物体的空间深度信息，目前已有利用Kinect进行三维场景重建和空间交互的hacker例子。下图是利用NITE的StickFigure追踪人体骨架姿态信息的例子：

[singlepic id=28 w=320 h=240 mode=watermark float=center]

简单修改一下NITE的PointViewer例子，我们可以把手势跟踪进一步改进为手势鼠标控制（已做消除抖动处理），如下图：

[singlepic id=29 w=320 h=240 mode=watermark float=center]

由于目前官方SDK尚未推出，基于Kinect的开发框架方案已有许多，但缺乏权威性。较令人信服的组合除了我们上述建议的三件套，再加上OpenCV算法包和OpenGL库就足以做出十分不错的应用了。即日起本站新增加Kinect项目板块，将重点关注基于Kinect的应用开发。值得一提的是，目前在kinect开发领域拥有一个人气不错的中文社区http://www.cnkinect.com，这在国内学院派日益衰微的今天绝对是难能可贵的，今后我们也将持续关注这一社区的动态。
