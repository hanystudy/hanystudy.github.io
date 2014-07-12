---
layout: post
status: publish
published: true
title: Kinect的三维重建(1)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 368
wordpress_url: http://www.hanyi.name/blog/?p=368
date: 2012-06-22 20:35:07.000000000 +08:00
categories:
- 可视化
- Kinect
tags:
- kinect
comments:
- id: 10516
  author: 言次
  author_email: mty@chu-jiao.com
  author_url: http://ty-m.com
  date: !binary |-
    MjAxMy0wNi0yNiAwMTowMzoxNCArMDgwMA==
  date_gmt: !binary |-
    MjAxMy0wNi0yNSAxNzowMzoxNCArMDgwMA==
  content: 楼主你好，我是AR开发者，楼主是否有本文的（2）部分，诚恳拜读！
---
有关Kinect应用开发正日新月异，稍有懈怠就会被远远甩在身后。不过，Kinect目前带给我们的仍只是一个充满无限可能的远景，正如App store能吸引年仅<a href="http://www.cnbeta.com/articles/161695.htm">11岁的开发者</a>一样，Kinect未来将对“全民开发者”产生重要推动作用。另一方面，一些基于Kinect的应用研究仍颇复杂，主要是因为一些关键环节的滞后而导致的。2011年的siggraph talks上，<a href="http://research.microsoft.com/en-us/projects/surfacerecon/">KinectFusion</a>首次展示了实时、廉价、轻便的室内场景三维重建，使得我们向着“无处不在的数字化”迈进了一大步。该项目主要由微软剑桥研究院的研究人员发起实施，研究小组目前公开发表了两篇文章：

Shahram Izadi, David Kim, Otmar Hilliges, David Molyneaux, Richard Newcombe, Pushmeet Kohli, Jamie Shotton, Steve Hodges, Dustin Freeman, Andrew Davison, and Andrew Fitzgibbon, <strong>KinectFusion: Real-time 3D Reconstruction and Interaction Using a Moving Depth Camera</strong>, ACM Symposium on User Interface Software and Technology, October 2011

Richard A. Newcombe, Shahram Izadi, Otmar Hilliges, David Molyneaux, David Kim, Andrew J. Davison, Pushmeet Kohli, Jamie Shotton, Steve Hodges, and Andrew Fitzgibbon, <strong>KinectFusion: Real-Time Dense Surface Mapping and Tracking</strong>, in IEEE ISMAR, IEEE, October 2011

除了Microsoft Research外，国内外大量机构都借助Kinect进行相关研究。仅就实时场景三维重建这项应用而言，由开源机器人研发公司Willow Garage在2011年发起的Point Cloud Libary (PCL)就吸引了全世界数十家著名科研机构参与、十余家公司提供经济支持。PCL是一项集点云获取与处理、滤波、特征提取、关键点标定、表面重建和点云配准以及点云融合等功能为一体的开源点云处理库，PCL使用OpenNI作为系统IO接口，实际成为KinectFusion的开源实现项目，该项目的相关论文发表在<a href="http://www.pointclouds.org/assets/pdf/pcl_icra2011.pdf">ICRA2011</a>上。

上述文献可以说是迄今为止有关Kinect的三维重建应用的最佳切入点了。应注意的是这两篇文章讨论重点的区别，前者先是对KinectFusion整个项目进行了流水式说明，随后解释了文中采用的经典算法的GPU实现，开发者可能会比较感兴趣；而后一篇文章则重点讨论了新的并行算法的形式化描述和性能分析，这部分可能会更吸引一部分研究者。我们将采用一种自上而下的方法先对这两篇文章进行简要介绍。

<strong> 概述</strong>

有关Kinect的结构光技术读者可以参考这篇<a href="http://www.hanyi.name/blog/?p=335">文章</a>或直接去Google下PrimeSense的专利，这里不再赘述。不过，尽管Kinect在获取深度图方面几乎是达到了性能与质量的完美结合，但仍无法避免深度图中大量抖动的产生，如果我们直接对单张深度图进行bilateral filter双边滤波然后重建出网格模型，会发现重建出的模型表面质量较低，甚至出现孔洞的情况。更重要的是，单张深度图能够获得的只是模型在某个视角下所展现出的一部分，而非表示完整的模型。因此，如何改善基于Kinect深度图的模型重建质量，并实现物体的完整重建成为该项技术的关键。

KinectFusion的首要任务就是克服该难题，它允许用户手持Kinect设备在室内场景自由移动，并实现场景的高质量三维重建。同时，KinectFusion还提供了精彩的AR应用，包括前景、背景和人体的分割与重建、以及AR世界中的多点触摸识别技术。

<strong>场景重建</strong>

基于上述需求，KinectFusion允许用户手持Kinect设备自由探索室内环境，系统将自动跟踪Kinect摄像头的6DOF姿态，然后融合不同时序的深度图数据并重建出场景的全局模型。由于视角的不断变化，Kinect反馈的深度图也会发生改变，这里采用了类似图像超分辨率技术对深度图进行了细节优化，从而提高模型的重建质量。最后利用Kinect自带的RGB数据进行场景纹理映射。

首先如何利用单Kinect摄像头去跟踪它自己的6DOF姿态？这里采用点云模型的刚性配准对齐来计算摄像头在空间中的6DOF变换，点云配准首选经典的ICP[Besl92, RusinKiewicz01]。之前需要将深度图像数据变换至摄像机的空间坐标系中，并计算其对应点的法线信息；然后逐帧采用基于GPU的ICP实现进行模型配准和6DOF计算；在重建部分，KinectFusion并没有直接融合点云或生成网格模型，而是采用了[Curless96]的体集成算法，最终在全局坐标系中生成一个三维体素网格并进行不断更新，每个体素内最终保存了一段时间内从该体素到物理表面上某一点的平均距离。最后使用Raycast给出隐式表面的渲染结果，另一方面，以摄像机位置作为视点做Raycasting，同样能够得到一个具有真实细节的高质量的合成深度图，然后再对其进行下一轮ICP迭代。这就允许我们能够利用合成深度图与下一帧的深度图进行配准，使重建结果的精度不断提高。

具体算法实现采用了CUDA并行架构，算法基本流程如下：

1）深度坐标变换，对于每个像素启用一只CUDA线程，给定一个Kinect红外摄像头的内部校准矩阵K，使用如下公式

<img class="aligncenter" title="\textup{v}_{i}(u) = \textup{D}_{i}(u)\textup{K}^{-1}[u ,1]" src="http://latex.codecogs.com/gif.latex?\textup{v}_{i}(u) = \textup{D}_{i}(u)\textup{K}^{-1}[u ,1]" alt="" />

计算坐标变换；相应的顶点法线则直接取其右、下邻向量的外积，并进行归一化。时间i时的6DOF摄像机姿态使用一个刚性变换矩阵T表示，其中T包括了摄像机的旋转矩阵R和平移矩阵t。变换后的顶点和法线通过T即可再变换至全局坐标系中。

2）摄像机跟踪，该部分的主要目的就是计算6DOF姿态。核心的ICP即迭代最近点算法，首要是需要逐帧计算不同朝向的点集的相关度。这里采用了projective data association方法计算相关度。

3）体集成，针对已配准的点云数据，需要执行后续的融合处理，这里采用了经典的[Curless96]体集成方法融合这些点云数据。

4）光线投射渲染，采用光线投射渲染前步生成的隐式表面。

上述整个管线均采用了并行GPU实现，在下文中，我们将重点解析上述算法的CUDA实现细节。
