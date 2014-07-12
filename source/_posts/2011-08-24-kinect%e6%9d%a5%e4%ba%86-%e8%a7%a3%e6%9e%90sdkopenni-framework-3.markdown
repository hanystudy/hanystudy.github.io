---
layout: post
status: publish
published: true
title: Kinect来了——解析SDK(OpenNI Framework 3)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 344
wordpress_url: http://www.hanyi.name/blog/?p=344
date: 2011-08-24 23:24:07.000000000 +08:00
categories:
- 项目
- Kinect
tags:
- kinect
comments: []
---
<strong>基本编程方法</strong>

本节的主要内容是结合OpenNI编程。

首先假设使用MS VS作为开发工具，那么应先在工程中配置OpenNI库：其中分别需要添加环境变量$(OPEN_NI_INCLUDE)和$(OPEN_NI_LIB)，以及静态链接库OpenNI.lib；此外代码文件中还要添加相应的头文件，C语言工程需包含XnOpenNI.h，C++工程为XnCppWrapper.h。

下面的代码段演示了如何初始化一个上下文对象，并从深度节点中创建和读取数据：

[c]XnStatus nRetVal = XN_STATUS_OK;
xn::Context context;       //声明上下文对象
nRetVal = context.Init();        //初始化并返回异常编码
xn::DepthGenerator depth;        //声明深度生成器节点

nRetVal = depth.Create(context);        //初始化节点，返回异常编码
nRetVal = context.StartGeneratingAll();        //启动深度节点数据生成
while (bShouldRun)
{
nRetVal = context.WaitOneUpdateAll(depth);        //等待数据更新
if (nRetVal != XN_STATUS_OK)        //数据更新异常
{
printf(&quot;Failed updating data: %s\n&quot;,xnGetStatusString(nRetVal));
continue;        //不间断通信
}
//深度数据获取正常
const XnDepthPixel* pDepthMap = depth.GetDepthMap();        //获取数据缓冲区指针
}
context.Shutdown();        //关闭上下文对象[/c]

通常配置一个工作链需要实现大量代码，OpenNI提供了工作链查询接口，使得程序员可以通过查询关键字的形式找到系统内已有的相关工作链配置信息，考虑到符合查询条件的结果数量可能不唯一，OpenNI的查询机制结果总是以集合的形式返回。下面给出相关代码：

[c]//创建查询对象
xn::Query query;
nRetVal = query.SetVendor(&quot;MyVendor&quot;);        //设置查询条件，这里设置了有关提供商名称
query.AddSupportedCapability(XN_CAPABILITY_SKELETON);        //这里的查询条件指定数字骨架数据支持
xn::NodeInfoList possibleChains;
nRetVal = context.EnumerateProductionTrees(XN_NODE_TYPE_USER, &amp;query, possibleChains, NULL);        //根据查询对象枚举全部的工作链配置
xn::NodeInfo selected = *possibleChains.Begin();        //返回最相似的查询结果，此时仍是但节点信息
nRetVal = context.CreateProductionTree(selected);        //根据节点信息创建相应工作节点
xn::UserGenerator userGen;
nRetVal = selected.GetInstance(userGen);        //获取节点实例
......        //进一步操作[/c]

实际上上述代码在OpenNI编程中非常常见，因为通过编程配置有关工作链实际上是一件很烦琐的事情。但显然对相关节点的查询并不一定能得到理想的结果，有时确实会发生查询结果为零的情况。例如该功能类型的节点尚未配置入系统中，或者节点的许可证序号不一致等异常情况。为方便查看节点测试失败的原因，OpenNI提供了一种针对节点类型的全局测试机制，该机制允许记录节点初始化失败的原因。相关代码如下：

[c]xn::EnumerationErrors errors;        //实际上是一个异常结果集合
xn::HandsGenerator handsGen;
nRetVal = context.CreateAnyProductionTree(XN_NODE_TYPE_HANDS, NULL, handsGen, &amp;errors);
//如果没有一个节点测试成功
if (nRetVal == XN_STATUS_NO_NODE_PRESENT) {
//遍历所有的异常信息，并输出
for (xn::EnumerationErrors::Iterator it = errors.Begin(); it != errors.End(); ++it) {
XnChar strDesc[512];
xnProductionNodeDescriptionToString(&amp;it.Description(), strDesc, 512);
printf(&quot;%s failed to enumerate: %s\n&quot;, xnGetStatusString(it.Error()));
}
return (nRetVal);
}
//如果存在相关节点，但无法验证
else if (nRetVal != XN_STATUS_OK) {
printf(&quot;Create failed: %s\n&quot;, xnGetStatusString(nRetVal)); return (nRetVal);
}[/c]

下面这段代码演示了从创建深度生成器，到节点设置配置信息，再从中获取数据并最终显示的全过程。

[c]XnStatus nRetVal = XN_STATUS_OK;
Context context;
nRetVal = context.Init();        //创建上下文
DepthGenerator depth;
nRetVal = depth.Create(context); //创建深度生成器
XnMapOutputMode mapMode;
mapMode.nXRes = XN_VGA_X_RES;
mapMode.nYRes = XN_VGA_Y_RES;
mapMode.nFPS = 30;
nRetVal = depth.SetMapOutputMode(mapMode);        //设置其输出模式为VGA+30FPS，
nRetVal = context.StartGeneratingAll();        //开始获取数据
XnUInt32 nMiddleIndex = XN_VGA_X_RES * XN_VGA_Y_RES/2 + XN_VGA_X_RES/2;        //计算中心像素索引
while (TRUE) {
nRetVal = context.WaitOneUpdateAll(depth);       //刷新数据缓冲区
const XnDepthPixel* pDepthMap = depth.GetDepthMap();
printf(&quot;Middle pixel is %u millimeters away\n&quot;, pDepthMap[nMiddleIndex]);        //输出中心像素的深度值
}
context.Shutdown();[/c]

音频生成器（AG）的操作与其它节点相比有一定区别，原因在于当程序执行UpdateData()方法后AG才会将之前记录的音频数据存入数据缓冲区中，而且许多情况下音频生成器的数据缓冲区大小是未知的，因此需要经常使用xn::AudioGenerator::GetDataSize()方法获取当前环境下的数据缓冲区大小。下面的例子中首先创建了一个AG，然后对其进行配置，最后读取音频数据。

[c]Context context;
nRetVal = context.Init();        //初始化上下文
AudioGenerator audio;
nRetVal = audio.Create(context);         //初始化AG
XnWaveOutputMode waveMode;
waveMode.nSampleRate = 44100;
waveMode.nChannels = 2;
waveMode.nBitsPerSample = 16;
nRetVal = audio.SetWaveOutputMode(waveMode);        //设置AG配置属性
while (TRUE) {
nRetVal = context.WaitOneUpdateAll(audio);        //刷新数据缓冲区
const XnUChar* pAudioBuf = audio.GetAudioBuffer();
XnUInt32 nBufSize = audio.GetDataSize();        //获取当前数据大小
}
context.Shutdown();[/c]

最后，我们来看看OpenNI提供的录制与缩放功能（NSSDK未提供该功能）。执行录制的前提是首先创建一个Recorder节点对象,并设置好它的待保存文件名。然后应用程序在该节点中添加需要进行录制目标节点，当在Recorder中加入一个节点后，Recorder将首先保存其配置信息，然后对其进行记录。同时Recorder会持续监听目标节点的所有触发事件，以便当节点配置发生改变时能及时获得响应。一旦所有节点添加至Recorder中，应用程序就可以使用Recorder记录节点的数据流了。需要注意的是，针对数据的记录可以使用xn::Recorder::Record()方法，或者通过UpdateAll函数进行。

此外，使用外部XML文件初始化的OpenNI程序可方便地记录其session信息，从而避免修改相应代码。具体只需要在XML文件中为Recorder创建一个新的节点，并将所有节点添加至其中，每当应用程序调用一类UpdataAll函数时均会自动触发记录操作。下面的代码演示了记录一个深度生成器的操作：

[c]DepthGenerator depth;
nRetVal = depth.Create(context);          // 创建深度生成器
RetVal = context.StartGeneratingAll();
Recorder recorder;
nRetVal = recorder.Create(context);        //创建一个Recorder
nRetVal = recorder.SetDestination(XN_RECORD_MEDIUM_FILE, &quot;c:\\temp\\tempRec.oni&quot;);        //配置Recorder属性
nRetVal = recorder.AddNodeToRecording(depth, XN_CODEC_16Z_EMB_TABLES);        //将深度节点加入Recorder
while (TRUE) {
nRetVal = context.WaitOneUpdateAll(depth);        //注意UpdataAll函数既刷新了数据缓冲区，同时对其中的帧数据进行了记录
}[/c]

录制数据的重放需要首先调用xn::Context::OpenFileRecording()函数，OpenNI读入该文件后先针对文件中的每一个节点创建一个对应的模拟节点，然后使用记录的配置信息进行重配置。应用程序可以随时调用xn::Context::FindExistingNode()获取任何需要的节点，并能像正常节点一样使用。但是应注意，由播放器创建的节点处于锁定状态，且无法被更改，因此其配置信息只能维持记录中保存的状态。同时 ，使用XML文件的OpenNI程序可以轻松替换其载入录制内容，即直接从XML的Recorder节点中读取相应数据。下面的代码就演示了如何载入录制文件，并获取其中保存的深度节点信息。

[c]Context context;
nRetVal = context.Init();        //初始化上下文
nRetVal = context.OpenFileRecording(&quot;c:\\temp\\tempRec.oni&quot;);        //载入录制文件
DepthGenerator depth;
nRetVal = context.FindExistingNode(XN_NODE_TYPE_DEPTH, depth);        //获取已录制的深度生成器节点[/c]
 在关于OpenNI的最后一篇文章中，我们将重点关注有关节点配置的内容。</pre>
