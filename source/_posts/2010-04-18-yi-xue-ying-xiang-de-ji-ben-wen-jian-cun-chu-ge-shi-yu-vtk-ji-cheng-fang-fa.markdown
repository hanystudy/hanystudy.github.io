---
layout: post
status: publish
published: true
title: 医学影像的基本文件存储格式与vtk集成方法
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 226
wordpress_url: http://www.hanyi.name/blog/?p=226
date: 2010-04-18 17:32:52.000000000 +08:00
categories:
- 可视化
tags:
- vtk
- 可视化
- dicom
- gdcm
comments:
- id: 9047
  author: shanon
  author_email: shanon@info.jhc.cn
  author_url: ''
  date: !binary |-
    MjAxMS0wMy0xMiAxNTowMToxNSArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wMy0xMiAwNzowMToxNSArMDgwMA==
  content: 相关的知识技术，请多秀秀
---
 毫无疑问，PACS已经是现代乃至未来医疗机构里不可或缺的基础设施之一，医学影像的基本存储格式就成为了标准化过程中的关键环节。

 ACR/NEMA联合发布了数字医学影像和通信标准，即DICOM。DICOM涉及的领域非常之多，NEMA上公布了DICOM的全部文档信息：

 <a href="http://medical.nema.org/">http://medical.nema.org/</a>

 此外，通常我们研究DICOM存储格式主要是为了实现文件压缩格式的转换，这里建议参考一些额外信息，以避免受到DICOM海量文档的困扰：

 <a href="http://www.dclunie.com/medical-image-faq/html/part8.html">http://www.dclunie.com/medical-image-faq/html/part8.html</a> 

 这里我们主要讨论vtk对DICOM标准的支持情况。在早期vtk版本中并未直接提供对DICOM的IO支持，读取DICOM文件通常要借助vtkVolume16Reader来完成，但要涉及一系列复杂问题，我们使用如下示例说明：

[c]
vtkVolume16Reader *Reader = vtkVolume16Reader::New();
Reader-&gt;SetDataDimensions(256,256);
//图像尺寸
Reader-&gt;SetDataByteOrderToLittleEndian ();
Reader-&gt;SetFilePrefix (&quot;E:\\ct&quot;);
//路径
Reader-&gt;SetImageRange(1, 93);
//切片起始段
Reader-&gt;SetDataSpacing (0.8, 0.8, 1.5);
//切片间距
[/c]

 另外，该方法也不支持对压缩数据或多帧影像进行IO操作。

 从vtk4.4起，一个直接针对DICOM文件读取的类被引入vtkDICOMImageReader，从而完全替代了vtkVolume16Reader的历史地位。vtkDICOMImageReader的重要改进在于，其直接支持DICOM，避免了许多没有必要的前置操作。

 <a href="http://www.vtk.org/doc/nightly/html/classvtkDICOMImageReader.html">http://www.vtk.org/doc/nightly/html/classvtkDICOMImageReader.html</a>

 但在实际应用中vtkDICOMImageReader仍然无法满足需求，如不支持查看完整的meta file，也不支持压缩数据和多帧影像。

 为了全面支持DICOM 3.0标准，开发者必须借助外部组件辅助IO。我们在起初提出引入ITK是一种有效的改进方法，这里推荐另外一个重要的开源项目GrassrootDCM基层dicom库，简称GDCM。

 GDCM旨在对DICOM文件编码进行处理，提供DICOM标准中各类编码格式的相互转换操作，包括将各种文件编码转换为RAW原始数据文件。目前GDCM已经提供了针对vtk的DICOM文件IO接口，结合vtk和gdcm，我们就可以完全实现稳定的dicom文件IO操作了。

 但是将GDCM纳入现有工程项目中并非易事，我们将在后续文章中就gdcm做一些说明。
