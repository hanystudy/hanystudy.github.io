---
layout: post
status: publish
published: true
title: 安装与配置
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 218
wordpress_url: http://www.hanyi.name/blog/?p=218
date: 2010-03-07 01:10:46.000000000 +08:00
categories:
- 可视化
tags:
- vtk
- itk
- 可视化
comments: []
---
 事实上，vtk users' mannual和itk software guide两部手册即可满足本部分需要了。对于中文读者而言，改编自c3p社区原创文章的简略中文教程也已经在互联网中广泛流传。这里我们仅仅讨论一些常见安装问题和联用问题。

 对于VC6.0用户 而言，需要注意编译环境是否已经打上了Sevice pack 5，编译过程中出现的未响应问题大部分是sp5环境不存在或不完整所导致的。

 关于是否编译examples，一般建议初学者选择，但itk的该部分将耗费数G硬盘空间，而且严重降低编译效率，资源有限者慎选。

 关于itk在vc++6.0环境中的使用，我们要注意cmake文档的创建。例如对工程ItkRegMFC，我们可以构造如下CMakeLists：

[c][/c]
PROJECT( ItkRegMFC )
#ADD_DEFINITIONS(-D_AFXDLL)

SET(CMAKE_MFC_FLAG 2)

SET(ItkRegMFC_SRCS
   stdAfx.cpp
   stdAfx.h
   ItkRegMFC.cpp
   ItkRegMFC.h
   ItkRegMFC.rc
   ItkRegMFCDoc.cpp
   ItkRegMFCDoc.h
   MainFrm.cpp
   MainFrm.h
   ItkRegMFCView.cpp
   ItkRegMFCView.h
   )

IF(WIN32)
  LINK_LIBRARIES(
    wsock32
  )
ENDIF(WIN32)

FIND_PACKAGE(ITK)
IF(ITK_FOUND)
   INCLUDE(${ITK_USE_FILE})
ELSE(ITK_FOUND)
   MESSAGE(FATAL_ERROR
           "Cannot build without ITK.  Please set ITK_DIR.")
ENDIF(ITK_FOUND)

ADD_EXECUTABLE(ItkRegMFC WIN32 ${ItkRegMFC_SRCS})
INSTALL_TARGETS(/bin ItkRegMFC)
TARGET_LINK_LIBRARIES(ItkRegMFC ${ITK_LIBRARIES} )
[c][/c]
运行cmake生成相应的工程文件即可。</pre>
