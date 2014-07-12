---
layout: post
status: publish
published: true
title: 站点维护
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 245
wordpress_url: http://www.hanyi.name/blog/?p=245
date: 2010-08-12 14:12:39.000000000 +08:00
categories:
- 天选之作
- 站点日志
tags: []
comments: []
---
 今天扫除了所有已知的bug，包括slideshow和nav menu的问题。值得注意的是，在更新了pixopoint_menu后发生了未知的兼容错误，给出的free support声称是和某些主题的css设置发生冲突，这条信息确误导过我们一段时间。经过调试后发现问题和主题css设置无关，而是出在曾经采用的一段web图片效果脚本lightbox上。事实上我们早在一年多前就已经弃用lightbox，但是当时并未将脚本载入的代码注释掉，结果与新的menu插件发生冲突而导致menu无法访问的bug...无论如何，一切都成为过去了。

 另外，最近在自学Numerical Methods和CG时突然发现以前学过的数学是极有用的，但是不得不反复学习...这再一次验证了我的观点：多数人学习前应当尽量培养兴趣，尤其是对理论应用于创新实践的憧憬。仅凭借对各种天才神童的吹捧，永远无法使我们在黑暗中找到光明。
