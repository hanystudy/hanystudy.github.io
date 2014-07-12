---
layout: post
status: publish
published: true
title: 关于GrassrootDCM补完
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 228
wordpress_url: http://www.hanyi.name/blog/?p=228
date: 2010-04-19 20:52:16.000000000 +08:00
categories:
- 可视化
tags:
- vtk
- 可视化
- gdcm
- grassrootdcm
comments: []
---
 之前提到GDCM基层DCM类库是一个颇为强大的开源dicom文件基础类库。目前，GDCM已经专门针对vtk提供了应用程序接口，我们可以利用vtkGDCMImageReader和vtkGDCMImageWriter两个类完整支持DICOM标准V3的IO操作。

 但是获得GDCM后，遇到一些颇为棘手的问题。

 首先，GDCM对Win32平台只支持vc7.1以后的编译器，而现有的vtk则是采用vc6.0编译。即是说，如果编译GDCM时要cmake选择vtk的扩展项，就必须采用vc7.1以后的编译器重新编译vtk。

 其次，现有的工程均是采用vc6.0创建，如果将平台完全迁移至后续vc版本，就可能会影响目前的工程进度。

 另外，从vc6.0迁移至新版本中所带来的编码问题可能会影响到应用程序的稳定性。

 出于以上考虑，先尝试重新编译vtk，这里我们预设的编译平台为vs2005，即vc8.0。但注意不应覆盖原有vc6.0编译的vtk文件，即在cmake阶段改一下prefix属性值就可以了，其余操作不变。对于新install的vtk文件，我们将环境变量path中bin的原有vtkbin的设置改为新版本值（有些读者之前可能会选择将所有bin文件放入system32中，但是按修改path的方法较好，当然后者的修改方法是相对应的）。

 现在cmake从sourceforge上找来的GDCM2.0.x，仔细检查一些配置项，尤其是扩展vtk项一定要选，g后用vc8.0编译。这里不应出现任何error。现在看来，GDCM的安装步骤其实和vtk没有什么区别，但是对vtk和编译器的要求较严格。

 这里再总结一下我们针对vc6.0使用GDCM+VTK编程的基本思路：

 1、vc6.0引用库采用原有vc6.0编译生成的vtk文件。

 2、运行库采用vc8.0编译生成的vtk文件（即DLL文件）。

 3、在vc6.0中再引用vc8.0编译生成的GDCM文件。

 完成上述步骤，就可以使用vc6.0进行下一步工作了。要注意的是，vtk的运行库一定要更新为vc8.0新编译版本，否则我们一调用GDCM就会因为DLL版本不足而出错。

 关于gdcm的全套文档可以参考：

 <a href="http://gdcm.sourceforge.net">http://gdcm.sourceforge.net</a>

 这里要再次说明一下，gdcm完整支持dicomV3，这是vtkDICOMImageReader乃至ITK都无法与之相提并论的。在实际应用开发中，还要注意程序基础构架对选择开发平台的影响。
