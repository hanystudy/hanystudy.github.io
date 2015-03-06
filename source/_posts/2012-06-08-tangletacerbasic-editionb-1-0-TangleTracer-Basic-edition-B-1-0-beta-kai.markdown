---
layout: post
status: publish
published: true
title: TangleTracer(Basic edition/B) 1.0 beta开源发布
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 370
wordpress_url: http://www.hanyi.name/blog/?p=370
date: 2012-06-08 22:32:17.000000000 +08:00
categories:
- 可视化
tags:
- 光线跟踪
- TangleTracer
comments: []
---
TangleTracer是一个实验性光线跟踪渲染平台，目的在于为真实感绘制技术的初学者提供完备的学习例程和友好的实验接口。系统设计思路主要源于Kevin Suffern的《Ray tracing from the Ground Up》一书，同时部分参考了著名的真实感绘制圣经《PBRT 2nd》。从今年三月中旬起我们就开始了前期的技术储备工作，该项目由四月初正式开始，截至目前完成代码近9万行，系统框架基本完善，实现并调试通过了440个demo或Kevin书中的习题（这部分代码超过4万行）。与K书中提供的原型系统不同，项目开发平台选择了Microsoft Visual Studio 2010和Qt framework 4.7.3，为快速开发提供了良好基础。
由于系统和部分框架来源于K书，我们循例采用GPLv2开源发布。此次发布的主要内容是系统的代码部分，为便于大家测试，我们稍后会提供基于win32平台的二进制版本（目前正在进行兼容性测试），运行系统内置的全部例程需要同时满足以下条件：
1.下载例程所需的纹理包：http://www.raytracegroundup.com/downloads/TextureImages.zip；
2.下载例程所需的模型包：http://www.raytracegroundup.com/downloads/PLYFiles.zip；
3.确保纹理包的TextureFiles目录和模型包的PLYFiles目录与程序二进制文件位于同一目录下；
4.部分模型需要下载stanford的原始文件并进行替换（具体请阅读README及代码注释）：http://www-graphics.stanford.edu/data/3Dscanrep/；
此次发布的是TangleTracer的Basic edition/B版本，其目的在于提供思路清晰的CPU渲染技术，但实际渲染性能较低。我们正在进行系统的并行迁移实现，并力求在下半年提供TangleTracer的GPU edition/G版本。详情请关注项目github主页：http://mp77.github.com。
