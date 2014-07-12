---
layout: post
status: publish
published: true
title: KINECT来了——解析SDK(MS SDK 1)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 347
wordpress_url: http://www.hanyi.name/blog/?p=347
date: 2011-09-17 23:41:18.000000000 +08:00
categories:
- 项目
- Kinect
tags:
- kinect
- sdk
comments: []
---
在8月初的<a href="http://www.hanyi.name/blog/?p=330" target="_blank">文章</a>里，我们曾对Microsoft推出的官方Kinect SDK beta进行了初步介绍，本文将在此基础上对其做出进一步说明。值得注意的是对托管代码和非托管代码的选择，从社区反馈看来似乎前者占据了绝对上风，coding4fun推荐的项目也基本都是以WPF编程为主。不过考虑到平台的潜在可扩展，这里还是推荐使用标准C++构建非托管程序。下面先给出两种编程方式在VS2010中的配置方法：

对于托管C#程序，添加Add Reference中.Net标签页的Microsoft.Research.Kinect.dll，使用using Microsoft.Research.Kinect.Nui引用NUI API的声明文件，如果还要引用音频API，还要加上using Microsoft.Research.Kinect.Audio。

对于标准C++程序，代码中必须加入&lt;windows.h&gt;头文件，其中NUI API需要包含MSR_NuiApi.h头文件、音频API需要包含MSRKinectAudio.h。另外需要添加相应的静态链接库，同时确保安装的DLL文件存放在PATH路径中。这里给出SDK所包含的头文件说明：

MSR_NuiApi.h，该文件包含所有关于NUI API声明，包括初始化调用和访问函数，如NuiInitialize、NuiShutdown、MSR_NuiXxx以及INuiInstance，其主要功能是枚举设备并对其进行访问调用。MSR_NuiImageCamera.h，该文件包含对NUI图像和摄像头功能API的声明，其一般形式为NuiCameraXxx和NuiImageXxx：此类API的功能为调整摄像头倾角和仰角，并启动数据流并读入图像帧。MSR_NuiProps.h，该文件包含对NUI属性枚举API函数的声明。MSR_NuiSkeleton.h，该文件包括NUI骨架API函数的声明，其一般形式为NuiSkeletonXxx and NuiTransformXxx：功能为打开/关闭骨架跟踪、获取骨架数据、将骨架数据进行变换以实现平滑绘制。MSRKinectAudio.h，该文件包括对音频API函数的声明，其中ISoundSourceLocalizer接口可返回波束方向和音源位置。NuiImageBuffer.h，该文件定义了一个帧缓冲器，其作用类似DirectX9的纹理缓冲。

<strong>Kinect for Windows应用程序架构</strong>

[singlepic id=43 w=320 h=240 mode=watermark float=center]

上图给出了beta SDK的整体组件架构，硬件层我们已经在上月进行了详细介绍。驱动的内核模式包含了设备驱动程序，上层的数据交互统一使用WinUSB数据栈，其中设备栈主要用于设备的配置和访问，摄像头栈用于视频数据流控制，USBAudio栈用于音频数据流控制；用户模式为API提供了访问和控制接口。应用层API上包括了三部分组件，其中MS SDK beta直接提供了NUI API即基本API集和KinectAudio DMO，后者的功能主要是提供波束成形和音源定位功能。此外，如果要使用Kinect的所有功能，还需要自己准备Windows 7 SDK中的音频、语音、媒体API集以及微软语音识别SDK，上述组件并未包含在SDK中。近两篇文章会主要介绍NUI API部分的设计原理和编程说明。

<strong> NUI API概述</strong>

NUI API是Kinect SDK的核心组成部分，其主要功能包括：提供连接至PC的Kinect传感器元件的访问接口、提供对由Kinect成像传感器生成的图像和深度数据流访问接口、通过经处理的图像和深度数据实现骨骼跟踪。下面我们分别使用托管和非托管代码介绍整个Kinect API上下文环境的创建和销毁。

与OpenNI不同的是，Kinect SDK允许方便地同时使用多台Kinect设备，但是每台设备（或传感器）同一时间只能被一个程序实例使用。<strong>下面是使用C++代码实现传感器枚举和访问的整个过程：</strong>

情形一：应用程序同一时间仅使用一台Kinect设备：

1、调用NuiInitialize，该函数首先会初始化一个Kinect传感器设备实例；

2、调用NuiXxx，该函数集的功能是传输图像和骨骼数据流，并管理并配置相关摄像头；

3、调用NuiShutdown结束。

情形二：应用程序同一时间使用多台Kinect设备：

1、调用MSR_NuiDeviceCount，查看可用Kinect设备的数量；

2、调用MSR_NuiCreateInstanceByIndex创建指定设备索引的实例，该函数将返回该实例的INuiInstance接口指针；

3、调用INuiInstance::NuiInitialize，该函数用于初始化针对该实例的NUI API上下文；

4、调用针对INuiInstance接口的其它方法，用于传输图像和骨骼数据流并管理相关摄像头；

5、调用INuiInstance::NuiShutdown，销毁某个设备实例的NUI API上下文；

6、调用MSR_NuiDestroyInstance函数销毁该实例；

<strong>接下来是使用C#代码实现传感器枚举和访问的整个过程：</strong>

<strong></strong>情形一：应用程序同一时间仅使用一台Kinect设备：

1、创建一个新的运行时对象，将其参数列表留空，例如nui = new Runtime()，调用该构造函数表示在系统中创建了一个Kinect传感器设备实例；

2、调用Runtime.Initialize函数初始化NUI API上下文；

3、调用其它托管接口，用于传输图像和骨骼数据流，并管理和配置相关摄像头；

4、调用Runtime.Shutdown销毁实例。

情形二：应用程序同一时间使用多台Kinect设备：

1、调用MSR_NuiDeviceCount函数获取可用设备数量；

2、创建一个新的运行时对象，参数为设备索引号，如nui = new Runtime(index)，调用该构造函数表示创建了针对某个Kinect传感器的系统实例；

3、调用Runtime.Initialize函数初始化NUI API上下文；

4、调用其它托管接口方法，用于传输图像和深度数据、并管理和配置相关摄像头；

5、调用Runtime.Shutdown函数销毁实例。

NUI API的数据处理是通过一个多级管线实现的，在初始化阶段，应用程序需要指定相应级别的子系统，这样运行时系统才能系统所需的部分管线。在初始化时系统通常允许进行以下配置：

彩色：应用程序指定从设备中获取彩色图像数据流；

深度：应用程序指定从设备中获取深度图像数据流；

深度和人物索引：应用程序指定从设备中获取深度数据流，并请求骨骼跟踪引擎当前生成的人物索引；

骨骼：获取骨骼位置数据；

上述设置指定了有效数据流类型和解析度，例如当一个应用程序没有在NUI API初始化阶段指定深度数据时，它随后也无法开启一个深度数据流。
