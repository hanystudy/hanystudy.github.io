---
layout: post
status: publish
published: true
title: 有关Beta 2和Kinect for Windows商用前瞻
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 356
wordpress_url: http://www.hanyi.name/blog/?p=356
date: 2011-11-13 00:07:03.000000000 +08:00
categories:
- Kinect
tags:
- kinect
comments: []
---
在本月初Kinect正式发布一周年之际，微软公司副总裁Frank X. Shaw在公司官方博客上发表纪念文章“<a href="http://blogs.technet.com/b/microsoft_blog/archive/2011/10/31/feeling-the-kinect-effect.aspx" target="_blank">Feeling the Kinect Effect</a>”，并透露Kinect的商用计划“Kinect for Windows commercial program”，该项目将于2012年初正式公布。文章称专门负责商用计划的团队已获得来自超过25个国家的知名公司共超过200项商业应用申请，这些应用覆盖了20多个不同领域，这恐怕会令即将到来的2012年成为名副其实的“Kinect年”。

而在上个月27号，微软注册了kinectforwindows.org域名并将其作为Kinect for Windows项目新的官方网站，紧接着Kinect for Windows SDK 1.0 Beta 2在本月5号正式发布。相较于8月份后就再没更新过的Beta 1，新版本SDK在以下功能上进行了持续改进：

1、骨骼跟踪功能得到显著增强，在跟踪精度、计算和传输性能上有了明显提升。此外骨骼跟踪现在能够正确支持多核处理器，在同时使用2个Kinect时，开发人员也可以指定专门用于骨骼跟踪的设备了；

2、API现在能够对设备的状态进行有效检测和管理了，例如device unplugged, device plugged in, power unplugged等，这样应用程序就能在系统从待机状态恢复时自动重新连接设备了。一个很好的程序示例被写进了新版本的Shape Game Demo里；

3、在WPF程序中使用语音功能的开发人员不需要从额外的线程访问DMO了，现在可以直接从UI线程创建KinectAudioSource，从而能够简化现有工程的代码。

4、新的驱动程序、运行时系统和SDK现在能够在<strong>Windows 8 Developer Preview</strong>中使用了。

5、现在能直接创建64位应用程序；

6、NuiImageBuffer被新的INuiFrameTexture代替,新定义在MSR_NuiImageCamera.h中，现有项目不再需要引用NuiImageBuffer.h了；

7、安装目录的结构进行了调整，现在的安装路径使用环境变量%KINECTSDK_DIR%定义，默认值为C:\Program Files\Microsoft SDKs\Kinect\v1.0 Beta2；

8、示例代码的更改包括：

一个新的C#程序示例：KinectAudioDemo；

示例程序现在被默认安装在C:\Program Files\Microsoft SDKs\Kinect\v1.0 Beta2\Samples，你需要Unzip以便查看源代码，而这里建议将源码Unzip到Program Files以外的目录去；

9、驱动程序和运行时系统的稳定性和性能进一步提升，<strong>尤其是在托管API层</strong>。

总的来看，Beta 2并未提供关键功能上的改进，更多是对现有问题的修正。此外，备受瞩目的Kinect for Windows杀手级应用仍未出现，社区反馈是目前很多未公开的商业项目在尝试OpenNI，对MS SDK反倒比较谨慎。不过从长远角度考虑，MS SDK在开发社区、系统平台和资源支撑方面拥有众多优势，明年商业化后必然会率先占据有利位置。由此联想到近期在4S上被热炒的Siri，看来自然交互的春天真的离我们不远了。
