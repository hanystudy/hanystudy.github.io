---
layout: post
status: publish
published: true
title: MS Kinect开发社区的最新进展
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 330
wordpress_url: http://www.hanyi.name/blog/?p=330
date: 2011-08-11 10:47:36.000000000 +08:00
categories:
- 项目
- Kinect
tags:
- kinect
comments: []
---
三个月前，我们展示了一套配合使用OpenNI+SensorKinect+NITE在Windows平台上驱动Kinect的Demo程序，事实证明，上述框架已完全可满足基于最新版本的Kinect的PC项目开发了。不过，微软在连续跳票一个多月后终于坐不住了，于北京时间6月17日凌晨开放The Kinect for Windows SDK beta下载页面，并作为正规军首次杀入这个吸引了众多发烧友和研究人员的技术萌芽领域。

由于最近一段时间非常忙，我们几乎没有空闲时间用于对快速发展中的Kinect技术进行潜心研究。幸运的是，我得以在暑假一天半的假期里花几个小时研究来自微软的SDK。很显然，因为她的姗姗来迟，越来越多的国内开发者尝试进入这个领域，而又由于Kinect技术在应用层面实际上要求比较高的知识和技术门槛，这也成了Kinect社区拥有一批具有较高技术素养的核心成员的重要原因。

首先应当说明的是，我拿到的SDK其实是微软在7月29日发布的1.00.12版，msdn上给出的Refresh Details解释新版本在驱动和运行库部分相较旧版而言得到了一些增强，目前也没有发现存在任何兼容问题。须知MS的Kinect SDK beta只支持Windows 7操作系统，开发环境要求VS2010/.Netframework 4.0。关于硬件部分的要求在试验阶段其实几乎可以忽略，除非你买到了非盒装的Kinect（拆机版也可额外购买电源/USB数据接口）。

对于初学者而言，还有一些组件需要额外安装，但这些都不是必须的。例如几个经典的Demo程序，例如骨骼跟踪，就需要Microsoft DirectX® SDK - June 2010 or later version和Runtime for Microsoft DirectX® 9才可以编译运行；另外对于语音命令识别的Sample程序，还需要安装Microsoft Speech Platform Runtime, version 10.2 (x86 edition)、Microsoft Speech Platform - Software Development Kit,version 10.2 (x86 edition)以及Kinect for Windows Runtime Language Pack, version 0.9(acoustic model from Microsoft Speech Platform for the Kinect for Windows SDK Beta。上述开发库提供了内容绘制、图像和图形处理、语音识别等非Kinect原生功能，仅仅这些，Kinect综合技术的一角其实已经显现出来了。

那么下面就是一个运用骨骼跟踪和语音识别技术的官方Demo：

[singlepic id=32 w=320 h=240 mode=watermark float=center]

在试用了几个不同的Demo后，我认为微软这次放出的SDK其实并非想象中好。问题主要在于开发平台比较单调，尽管支持非托管的C++代码，但是官方demo基本上都使用C#.NET/WPF开发，在与现有资源相结合的部分还做得不够好，而且demo的bug比较多；另外与OpenNi/NITE组合相比前者似乎性能较低——当然其精度较之后者能好一些，但区别并不明显。

荷兰人Jasper Brekelmans是一名长期关注Kinect技术的开发人员，他在<a href="http://www.brekel.com/?page_id=671" target="_blank">博客</a>上对截至目前微软的beta版和PrimeSense的OpenNi进行了比较。我们总结了他的文章，并将其划分为四个主要问题：

<strong>问：什么是Microsoft’s Kinect SDK (Beta)能而OpenNi还不能做到的？</strong>

答：目前微软的产品提供了音频支持、调整倾角的转动电机motor/tilt、在全身跟踪骨骼跟踪方面：非标准姿势检测（相对于OpenNi的投降姿势...），头部、手、脚、锁骨检测以及关节遮挡等细节上的处理更为细致（但精度是否更高还不能确定）。此外，SDK beta还提供了多机接口，使许多尚在设想中的特殊应用有直接变为现实的可能。当然，MS的开发库在安装上非常容易，也基本不需要任何设置即可使用。提供了可用视频和深度图输入的事件触发机制。

<strong>问：那么微软的产品没有提供什么功能？</strong>

答：对许多开发者而言最大的问题可能是微软对非商业使用的限制，而OpenNi则不存在这方面问题。此外，微软的SDK目前并未提供手势识别和跟踪功能，而在前一篇文章中我们已经看到用NITE的手势识别和跟踪组件控制鼠标光标的例子。

另一方面，SDK beta未能实现RGB图像/深度图像的互对齐，只是提供了对个体坐标系的对齐，而该功能在许多应用中实际上是很有必要的。

在全身骨骼跟踪中，SDK只计算了关节的位置，并未得出其旋转角度。从可移植的角度来看，SDK beta只能用于Kinect/Win7平台，而OpenNi还至少支持华硕的WAVI Xtion体感设备，今后支持的硬件平台还可能更多。相比较而言SDK beta不支持Unity3D游戏引擎、不支持记录/回放数据写入磁盘、不支持原始红外视频数据流、也不支持像OpenNi一样的角色入场和出场的事件响应机制。

<strong>问:能不能再说说OpenNi/NITE提供的功能？</strong>

答：可用于商业开发、包含手势识别和跟踪功能、可自动对齐深度图像和RGB图像，全身跟踪、关节旋转角度计算、看起来性能较好、跨平台多设备支持、已有众多游戏产品应用、支持记录/回放数据写入磁盘、支持原始红外视频数据流、支持角色入场和出场的事件响应机制。

<strong> 问：那么OpenNi/NITE不能做什么？</strong>

答：未提供音频功能、不支持调整倾角的转动电机motor/tilt、在全身跟踪骨骼跟踪方面：无法跟踪头部、手、脚和锁骨的旋转动作，需要标准姿势检测（即著名的投降姿势...），关节遮挡等细节上的处理似乎存在算法bug。不能自动安装并识别Kinect多机环境。安装过程较为繁琐，特别是NITE还要申请开发证书编码。OpenNi也没有提供可用视频和深度图输入的事件触发机制。

<strong>总结：</strong> OpenNI最大的优势就是允许跨平台多设备，以及商业应用。但从原始数据的采集和预处理技术上看，微软的SDK似乎更稳定一些，况且还提供了不错的骨骼和语音支持。对于部分身体部位识别方面的功能，SDK beta没有提供局部识别和跟踪，这需要自己的后续开发（至少在相当一段时期内微软可能都不会提供此类功能）。OpenNi/NITE虽然提供了手势识别和跟踪，然而在全身骨骼姿势识别和跟踪上还要更多借鉴微软的产品。

因此，按照目前在社区中的表现，SDK beta和OpenNi/NITE孰优孰劣还真无法一下子确定。而且随着越来越多的开发者加入微软这一方，SDK beta的普及可能会更快，但在更高层次的应用上，对二者的选用往往是需要一定智慧的。
