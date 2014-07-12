---
layout: post
status: publish
published: true
title: 样条插值的一般方法（上）
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 288
wordpress_url: http://www.hanyi.name/blog/?p=288
date: 2011-03-05 20:19:05.000000000 +08:00
categories:
- 可视化
tags:
- 样条插值
- Bezier
- catmull-rom
comments: []
---
  没有接触过计算机曲线、面生成技术的人可能会想到，如果我们给定一个点集序列，再根据该集合生成一条经过每个点的曲线，同时曲线的形态还可以依据点的相互位置而定。

 然而当我们初步接触了Bezier curve，敬佩之余还带有稍许失望。不过在这之前我们必须承认，“控制点”生成曲线的方法早已是老祖宗的专利，后来Citroen的de Casteljau和Renault的Bezier先后终于完成了样条曲线的数学定义。Bezier曲线的成功得益于其三个出色的特性：控制点简单、光滑度较好且可控以及允许Bezier反算。下面是直接根据三次Bezier公式生成带有四个控制点的Bezier曲线的过程。

<object style="width: 412px; height: 300px;" classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" width="412" height="300" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="wmode" value="transparent" /><param name="src" value="http://www.hanyi.name/blog/wp-content/uploads/2011/03/bezier.swf" /><param name="align" value="top" /><embed style="width: 412px; height: 300px;" type="application/x-shockwave-flash" width="412" height="300" src="http://www.hanyi.name/blog/wp-content/uploads/2011/03/bezier.swf" wmode="transparent" align="top"></embed></object>

 应当注意的是，工业标准的Bezier曲线生成往往采用基于de Casteljau算法的递归细分策略，其目的是为了确保进行计算机数值计算的稳定性，进一步提高曲线的精度。 现在我们希望解决文章开始的疑问，其实质是利用Bezier曲线进行插值，以构建能够通过“所有”控制点（实际上Bezier曲线往往通过首尾两个控制点）的曲线。

 通过对Bezier的学习我们知道，Bezier曲线必然通过首尾控制点的特性其实完全可以被用于插值算法。我们把有序点序列相邻两点作为首尾控制点，据此生成分段Bezier曲线，即可满足要求。这一过程需要解决两个问题：

 1、若采用分段三次Bezier曲线，如何确定除首尾控制点的另外两点；

 2、连接若干分段三次Bezier曲线的平滑性如何得以保证；

 早在1974年，Edwin Catmull和Raphael Rom发表《A class of local interpolating splines》一文，就解决了上述问题，而且至今仍是最重要的曲线插值方法。

 值得一提的是，Edwin Catmull是计算机图形学领域一名做出过许多突出贡献的科学家（如纹理映射、双三次Bezier曲面、反走样算法、曲面细分的优化算法，还独立发明了Z-Buffer技术），而且还是现代影视动画工业的奠基人之一。1979年，Catmull离开了纽约理工学院计算机图形学实验室，加盟Lucas的工业光魔并领导其计算机动画部门。1986年该部门被Steve Jobs收购，Catmull也成为新公司Pixar的CTO。2006年Disney收购Pixar，Catmull成为现任Disney Animation Studios&amp;Pixar Animation Studios总裁。

 假设有m+1个插值点<strong>p0</strong>,...<strong>pm</strong>，现在要才用分段三次Bezier曲线，则Catmull-Rom样条曲线包含m-2条Bezier曲线，其中第i条Bezier曲线起始于<strong>pi</strong>终止于<strong>pi+1</strong>。设：

 <strong>li</strong>=1/2(<strong>pi+1</strong> - <strong>pi-1</strong>)

 且

 <strong>pi+</strong> = <strong>pi </strong>+ 1/3li以及<strong>pi-</strong> =<strong> pi</strong> - 1/3<strong>li</strong>

 然后设<strong>qi(</strong>u)是<strong>pi</strong>、<strong>pi+</strong>、<strong>pi+1-</strong>、<strong>pi+1</strong>为控制点的Bezier曲线—曲线的定义域被变成了i&lt;=u&lt;=i+1。将这些Bezier曲线拼接在一起，就构成了完整的Catmull-Rom样条曲线q(u)，使得对于任何i&lt;=u&lt;=i+1都有<strong>q</strong>(u)=<strong>qi</strong>(u)。

 其示例如图：

<img class="aligncenter size-medium wp-image-290" title="catmull" src="http://www.hanyi.name/blog/wp-content/uploads/2011/03/catmull-300x180.png" alt="" width="300" height="180" />

 Catmull-Rom存在一定的不足，当某相邻点间距明显小于其它点的平均相邻距离时，生成的曲线会发生“漂离现象”，也就是导致G1不连续。实际上Catmull-Rom的问题主要在于额外的控制点位置异常，采用被称作Bessel-Overhauser的样条函数即可解决这一问题，这就是不同于均匀参数化的弦长参数化方法，此外还有一种节点值参数化方法，即向心参数化方法。有兴趣的读者可以查阅其它相关文献。

 本文的参考资料来源于Samuel R.Buss的《3D computer graphics—a mathmatical introduction with OpenGL》相关章节，以及两篇分别来自T.Y.Kim和Christopher Twigg的论文。下次我们将讨论构造曲面的一般方法（当然具体日期要根据下周的空余时间而定了）。
