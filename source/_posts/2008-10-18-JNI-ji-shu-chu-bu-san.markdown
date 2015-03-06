---
layout: post
status: publish
published: true
title: JNI技术初步(三)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 103
wordpress_url: http://www.hanyi.name/blog/?p=103
date: 2008-10-18 16:29:57.000000000 +08:00
categories:
- 转载
- java
tags:
- Java
- jni
comments:
- id: 7619
  author: average cost adipex generic
  author_email: ysxvavrv@eobqmmbd.com
  author_url: http://www.azcentral.com/members/Blog/medecinesupply/84988
  date: !binary |-
    MjAxMC0wNi0wNCAwNjo1MzoxOCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMjo1MzoxOCArMDgwMA==
  content: <a href="http://www.myoutdoorchannel.com/cialisxl124" rel="nofollow">cialis
    fact sheet</a> , [url="http://www.myoutdoorchannel.com/cialisxl124"]cialis fact
    sheet[/url] , http://www.myoutdoorchannel.com/cialisxl124 cialis fact sheet ,
- id: 7661
  author: tramadol and grapefruit juice
  author_email: afaorbto@nwknruiq.com
  author_url: http://www.freerider-tracks.com/account/tramadol-online-712
  date: !binary |-
    MjAxMC0wNi0wOSAxMTowMDoxMiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wOSAwMzowMDoxMiArMDgwMA==
  content: Great. Now i can say thank you!
- id: 7828
  author: herbal phentermine review best diet pills
  author_email: iftiyqow@sahfwhmb.com
  author_url: http://connect.lehighvalleylive.com/user/phenterminey138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMDoxMToyNyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxMjoxMToyNyArMDgwMA==
  content: Great site. Good info.
- id: 8181
  author: which is stronger tramadol or percoset
  author_email: fqsvzslb@qkpjljhw.com
  author_url: http://connect.masslive.com/user/Tramadol-Online-u138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMzowNTo1MyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNTowNTo1MyArMDgwMA==
  content: I bookmarked this link. Thank you for good job!
- id: 8211
  author: adipex moodswing
  author_email: kathxovp@rhatjupf.com
  author_url: http://connect.al.com/user/adipex8138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMzoyMDozMiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNToyMDozMiArMDgwMA==
  content: Perfect work!
- id: 8411
  author: phentermine online physician prescription
  author_email: giqnvofz@yywqtzjp.com
  author_url: http://connect.gulflive.com/user/Phentermine-Online-k138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMTowMToxNSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNzowMToxNSArMDgwMA==
  content: It is the coolest site, keep so!
- id: 8561
  author: valium gel for seizures
  author_email: ryttlnvu@cvyyqwbz.com
  author_url: http://connect.masslive.com/user/Valium-Online-8138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMjoxMzoxNiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxODoxMzoxNiArMDgwMA==
  content: I bookmarked this link. Thank you for good job!
- id: 8652
  author: whats tramadol
  author_email: urjpqaos@kkmjisuc.com
  author_url: http://connect.lehighvalleylive.com/user/tramadolb138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMjo1NjowNyArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxODo1NjowNyArMDgwMA==
  content: Incredible site!
- id: 8745
  author: phentermine no prescription get it online
  author_email: htdonpju@vruksooc.com
  author_url: http://connect.nola.com/user/Phentermine-Online-8138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMzo0MDowOCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxOTo0MDowOCArMDgwMA==
  content: Very interesting site. Hope it will always be alive!
- id: 8837
  author: viagra xanax phentermine online pharmacy carisoprodol
  author_email: ijnzeyyz@xkbqyyuw.com
  author_url: http://www.stumbleupon.com/stumbler/phentermine0l150/reviews/
  date: !binary |-
    MjAxMC0xMC0wMyAwMjowMToxMCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0xMC0wMiAxODowMToxMCArMDgwMA==
  content: Excellent site. It was pleasant to me.
---
全部的C/C++方法实现代码如下:
/**//*
*
* 一缕阳光(sundy)版权所有，保留所有权利。
*/
/**//**
*
* TODO Jni 中一个从Java到C/C++参数传递测试类
*
* @author 刘正伟(sundy)
* @see http://www.cnweblog.com/sundy
* @see mailto:sundy26@126.com
* @version 1.0
* @since 2005-4-30
*
* 修改记录：
*
* 日期 修改人 描述
* ----------------------------------------------------------------------------------------------
*
*
*
*/
// JniManage.cpp : 定义 DLL 应用程序的入口点。
//
package com.sundy.jnidemo;
＃i nclude "stdafx.h"＃i nclude &lt;stdio.h&gt;
＃i nclude &lt;math.h&gt;
＃i nclude "jni.h"
＃i nclude "jni_md.h"

＃i nclude "./head/Base.h"
＃i nclude "head/wmi.h"
＃i nclude "head/com_sundy_jnidemo_ChangeMethodFromJni.h" //通过javah -jni javactransfer 生成
＃i nclude &lt;stdio.h&gt;
＃i nclude "stdlib.h"
＃i nclude "string.h"

#pragma comment (lib,"BaseInfo.lib")
#pragma comment (lib,"jvm.lib")
//硬盘信息
struct {
char name[256];
int serial;
}DiskInfo;
/**//*BOOL APIENTRY DllMain( HANDLE hModule,
DWORD ul_reason_for_call,
LPVOID lpReserved
)
{
LPTSTR strName = new CHAR[256] ;
(*GetHostName)(strName);
printf("%s\n",strName);
delete [] strName;

return TRUE;
}*/
//将jstring类型转换成windows类型
char* jstringToWindows( JNIEnv *env, jstring jstr );
//将windows类型转换成jstring类型
jstring WindowsTojstring( JNIEnv* env, char* str );

//主函数
BOOL WINAPI DllMain(HANDLE hHandle, DWORD dwReason, LPVOID lpReserved)
{
return TRUE;
}
//输入常用的数值类型 Boolean,Byte,Char,Short,Int,Float,Double
JNIEXPORT void JNICALL Java_com_sundy_jnidemo_ChangeMethodFromJni_displayParms
(JNIEnv *env, jobject obj, jstring s, jint i, jboolean b)
{
const char* szStr = (env)-&gt;GetStringUTFChars(s, 0 );
printf( "String = [%s]\n", szStr );
printf( "int = %d\n", i );
printf( "boolean = %s\n", (b==JNI_TRUE ? "true" : "false") );
(env)-&gt;ReleaseStringUTFChars(s, szStr );
}

//调用一个静态方法,只有一个简单类型输出
JNIEXPORT jint JNICALL Java_com_sundy_jnidemo_ChangeMethodFromJni_add
(JNIEnv *env, jobject, jint a, jint b)
{
int rtn = (int)(a + b);
return (jint)rtn;
}

/**/////输入一个数组,这里输入的是一个Boolean类型的数组
JNIEXPORT void JNICALL Java_com_sundy_jnidemo_ChangeMethodFromJni_setArray
(JNIEnv *env, jobject, jbooleanArray ba)
{
jboolean* pba = (env)-&gt;GetBooleanArrayElements(ba, 0 );
jsize len = (env)-&gt;GetArrayLength(ba);
int i=0;
// change even array elements
for( i=0; i &lt; len; i+=2 )
{
pba[i] = JNI_FALSE;
printf( "boolean = %s\n", (pba[i]==JNI_TRUE ? "true" : "false") );
}
(env)-&gt;ReleaseBooleanArrayElements(ba, pba, 0 );
}

/**/////返回一个字符串数组
JNIEXPORT jobjectArray JNICALL Java_com_sundy_jnidemo_ChangeMethodFromJni_getStringArray
(JNIEnv *env, jobject)
{
jstring str;
jobjectArray args = 0;
jsize len = 5;
char* sa[] = { "Hello,", "world!", "JNI", "is", "fun" };
int i=0;
args = (env)-&gt;NewObjectArray(len,(env)-&gt;FindClass("java/lang/String"),0);
for( i=0; i &lt; len; i++ )
{
str = (env)-&gt;NewStringUTF(sa[i] );
(env)-&gt;SetObjectArrayElement(args, i, str);
}
return args;
}

//返回一个结构,这里返回一个硬盘信息的简单结构类型
JNIEXPORT jobject JNICALL Java_com_sundy_jnidemo_ChangeMethodFromJni_getStruct
(JNIEnv *env, jobject obj)
{
/**//* 下面为获取到Java中对应的实例类中的变量*/

//获取Java中的实例类
jclass objectClass = (env)-&gt;FindClass("com/sundy/jnidemo/DiskInfo");

//获取类中每一个变量的定义
//名字
jfieldID str = (env)-&gt;GetFieldID(objectClass,"name","Ljava/lang/String;");
//序列号
jfieldID ival = (env)-&gt;GetFieldID(objectClass,"serial","I");
//给每一个实例的变量付值
(env)-&gt;SetObjectField(obj,str,(env)-&gt;NewStringUTF("my name is D:"));
(env)-&gt;SetShortField(obj,ival,10);

return obj;
}
