---
layout: post
status: publish
published: true
title: 基于ITK+VTK的计算机医学图像处理技术研究(系列文章)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 217
wordpress_url: http://www.hanyi.name/blog/?p=217
date: 2010-03-05 10:03:40.000000000 +08:00
categories:
- 可视化
tags:
- vtk
- itk
- 可视化
- 医学图像
comments:
- id: 9048
  author: shanon
  author_email: shanon@info.jhc.cn
  author_url: ''
  date: !binary |-
    MjAxMS0wMy0xMiAxNToyMDoxOSArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wMy0xMiAwNzoyMDoxOSArMDgwMA==
  content: 没写全啊
- id: 9049
  author: mp77
  author_email: 8621316@qq.com
  author_url: http://
  date: !binary |-
    MjAxMS0wMy0xMyAxMTozMDoyOCArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wMy0xMyAwMzozMDoyOCArMDgwMA==
  content: 你好，vtk的项目是我本科毕设的一部分，答辩结束后的确就没再在blog上深入讨论了，不过至今仍保留了vtk的mail-list。如果你对这方面感兴趣的话可以和我邮件讨论，谢谢!
---
 今后几个月的时间里，我们将深入研究itk和vtk在计算机医学图像处理技术中的应用。itk是由美国国家医学图书馆发起的开源软件项目，该项目旨在促进人体信息数字化研究(Visible Human Project)，是未来进行人体机能智能模拟工程的基础性研究项目。vtk则是致力于计算机图形图像处理和可视化的开源软件项目。

 一般而言，vtk(Visualization Tookit)本身即是一款强大的可视化软件系统，本系列引入itk(Insight Segmentation and Registration Toolkit)，主要考虑有二：

 1、itk专注于医学图像分割与配准技术，在医用方面是对vtk很好的补充，也非常利于未来的功能扩展。

 2、当前版本的vtk(5.4.2)对DICOM通用标准医学图像的io支持性不佳。在已经进行的一系列试验中，vtk产生了一些不稳定状况，这可能会影响到本项研究今后的进展。

 下面我们将介绍各种软件系统的安装配置流程，我们预设的编译平台为Microsoft Visual Studio 6。
