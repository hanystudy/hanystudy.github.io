---
layout: post
status: publish
published: true
title: 简单的词法分析程序和一种lex for win32用法示例（上）
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 154
wordpress_url: http://www.hanyi.name/blog/?p=154
date: 2009-05-01 19:25:02.000000000 +08:00
categories:
- 编译技术
tags:
- 编译原理
- 词法分析
- lex
- yacc
comments:
- id: 9055
  author: Yong
  author_email: yong089@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMS0wNC0xMSAyMzowMDowMSArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wNC0xMSAxNTowMDowMSArMDgwMA==
  content: 我想问一下。。。词法分析程序中。。。“from nwu”是你吗？这个nwu...跟我想的是不是一样。。。难道这么巧。。。
- id: 9056
  author: Yong
  author_email: yong089@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMS0wNC0xMiAxMjo1ODoyMCArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wNC0xMiAwNDo1ODoyMCArMDgwMA==
  content: ! "。。。\r\n刚刚看到右上角那个相册。。。\r\n里面有多功能报告厅。。。\r\n还有一张合照？貌似里面有ZJF？还有一个雷什么的？忘记了~~\r\n原来这么巧啊。。。\r\n嗯~~学长好~~~"
- id: 9058
  author: mp77
  author_email: 8621316@qq.com
  author_url: http://
  date: !binary |-
    MjAxMS0wNC0xNSAxOTo1NDozNiArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wNC0xNSAxMTo1NDozNiArMDgwMA==
  content: ! '...我是西大10届毕业的...现在在本校读研...'
- id: 9063
  author: Yong
  author_email: yong089@gmail.com
  author_url: ''
  date: !binary |-
    MjAxMS0wNC0yMiAyMjoxNTo0MiArMDgwMA==
  date_gmt: !binary |-
    MjAxMS0wNC0yMiAxNDoxNTo0MiArMDgwMA==
  content: 呵呵，好巧，我08软工的~~~
---
 学计算机的同学都要学习编译原理，理论是抽象了点，但最终目的毕竟只是让我们了解一下程序语言编译器的构造原理。

 尽管没有充分的证据显示lex+yacc能证明一个人学习cp程度的好坏（考试的作用就更难以拿来说明这些了），但好歹大家学过nfa，学过确定化，学过dfa化简，用c语言大概可以写出设定简单的词法分析程序了。

 然而教材上都是一些纯理论性的内容，实现技术的精粹还得去找洋人学。因而本科生只需掌握lex和yacc——这已经是为极限，再低就难以搞清楚cp和programming有什么关系...再高？说实话暂时已是心有余而力不足了...

 那么lex的作用大概就是这些，尽管在几乎所有的程序设计语言编译器中词法分析都是由语法分析程序不断调用而生效，但对于我们来说分别实现并作演示是再好不过。这就是本文介绍词法分析器lex的目的。

 一个lex源程序由四部分构成，基本框架如下：

%{

//包含的头文件、函数声明以及一些全局量的声明

%}

//词法定义

%%

//规则定义

%%

//自定义程序

 需要注意的是，带有%的分隔符必须顶格书写，词法分析和规则定义的基本形式为正规式。为了显示词法分析的最终成果，需要定义一个二元结构体，分别存储其单词内容以及归属类别，并存入定义好的目标文件中，同时按国际惯例显示源代码出错信息。

 现在给出一个PL/0语言（pascal语言的一种子集）的EBNF描述：
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;prog&gt; → program &lt;id&gt;；&lt;block&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;block&gt; → [&lt;condecl&gt;][&lt;vardecl&gt;][&lt;proc&gt;]&lt;body&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;condecl&gt; → const &lt;const&gt;{，&lt;const&gt;}</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;const&gt; → &lt;id&gt;:=&lt;integer&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;vardecl&gt; → var &lt;id&gt;{，&lt;id&gt;}</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;proc&gt; → procedure &lt;id&gt;（&lt;id&gt;{，&lt;id&gt;}）;&lt;block&gt;{;&lt;proc&gt;}</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;body&gt; → begin &lt;statement&gt;{;&lt;statement&gt;}end</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;statement&gt; → &lt;id&gt; := &lt;exp&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">               |if &lt;lexp&gt; then &lt;statement&gt;[else &lt;statement&gt;]</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">               |while &lt;lexp&gt; do &lt;statement&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">               |call &lt;id&gt;[（&lt;exp&gt;{，&lt;exp&gt;}）]</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">               |&lt;body&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">               |read (&lt;id&gt;{，&lt;id&gt;})</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">               |write (&lt;exp&gt;{，&lt;exp&gt;})</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;lexp&gt; → &lt;exp&gt; &lt;lop&gt; &lt;exp&gt;|odd &lt;exp&gt;</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;exp&gt; → [+|-]&lt;term&gt;{&lt;aop&gt;&lt;term&gt;}</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;term&gt; → &lt;factor&gt;{&lt;mop&gt;&lt;factor&gt;}</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;factor&gt;→&lt;id&gt;|&lt;integer&gt;|(&lt;exp&gt;)</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;lop&gt; → =|&lt;&gt;|&lt;|&lt;=|&gt;|&gt;=</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;aop&gt; → +|-</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;mop&gt; → *|/</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;id&gt; → l{l|d}   （注：l表示字母）</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;integer&gt; → d{d} </p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;"> </p>
<p style="margin: 0cm 0cm 0pt; mso-pagination: widow-orphan;">注释：</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;prog&gt;：程序 ；&lt;block&gt;：块、程序体 ；&lt;condecl&gt;：常量说明；&lt;const&gt;：常量；</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;vardecl&gt;：变量说明 ；&lt;proc&gt;：分程序 ； &lt;body&gt;：复合语句 ；&lt;statement&gt;：语句；</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;exp&gt;：表达式 ；&lt;lexp&gt;：条件 ；&lt;term&gt;：项； &lt;factor&gt;：因子 ；&lt;aop&gt;：加法运算符；</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">&lt;mop&gt;：乘法运算符； &lt;lop&gt;：关系运算符</p>
<p style="margin: 0cm 0cm 0pt; tab-stops: 45.8pt 91.6pt 137.4pt 183.2pt 229.0pt 274.8pt 320.6pt 366.4pt 412.2pt 458.0pt 503.8pt 549.6pt 595.4pt 641.2pt 687.0pt 732.8pt; mso-pagination: widow-orphan;">odd：判断表达式的奇偶性。</p>

 利用上述文字中提取出的词法信息，我们可以建立一个简单的词法分析程序：

%{
/*
 *
 * coded by 韩翼 from nwu    Email:8621316@qq.com
 *
 */
#include &lt;stdio.h&gt;
#include &lt;stdlib.h&gt;
#include &lt;string.h&gt;
#define MAX_COUNT 10000     //最大可接受id数
int process();       //处理函数
int main(int argc,char **argv);
int nowline = 1;      //当前行数计数器
int errorno = 0;      //出错计数器
int flag = 0;       //分支标识
int count = 0;       //id计数器
char desfilename[32];     //目标文件名
char *srcfilename;      //源文件名
FILE *srcfile;       //源文件缓冲指针
FILE *desfile;       //目标文件缓冲指针
struct result       //id标记二元组
{
 char *content;
 char *description;
}result[MAX_COUNT];
%}
d [0-9]         
l [a-zA-Z]
wr ([0-9]+)[a-zA-Z]([a-zA-Z]|[0-9])*
integer [0-9]+
id [a-zA-Z]([a-zA-Z]|[0-9])*
anotherline [\n]
whitespace [\t]+
%%
"program" { flag = 0; process(); }
"const" { flag = 1; process(); }
":=" { flag = 2; process(); }
"var" { flag = 3; process(); }
"procedure" { flag = 4; process(); }
"begin" |
"end" { flag = 5; process(); }
"if" |
"then" |
"else" |
"while" |
"call" |
"read" |
"write" { flag = 6; process(); }
"+" |
"-" { flag = 7; process(); }
"*" |
"/" { flag = 8; process(); }
"=" |
"&lt;&gt;" |
"&lt;" |
"&lt;=" |
"&gt;" |
"&gt;=" { flag = 9; process(); }
"(" |
")" |
"," |
";" { flag = 10; process(); }
{wr} { flag = 13; process(); }
{id} { flag = 11; process(); }
{integer} { flag = 12; process(); }
{anotherline} { nowline++; }
{whitespace} { ; }
" " { ; }
. { flag = 14; process(); }
%%
int process()
{
 switch(flag)
 {
 case 0:
  result[count].description = "&lt;prog&gt;";
  break;
 case 1:
  result[count].description = "&lt;condecl&gt;";
  break;  
 case 2:
  result[count].description = "&lt;op&gt;";
  break;
 case 3:
  result[count].description = "&lt;vardecl&gt;";
  break;
 case 4:
  result[count].description = "&lt;proc&gt;";
  break;
 case 5:
  result[count].description = "&lt;body&gt;";
  break;
 case 6:
  result[count].description = "&lt;statement&gt;";
  break;
 case 7:
  result[count].description = "&lt;aop&gt;";
  break;
 case 8:
  result[count].description = "&lt;mop&gt;";
  break;
 case 9:
  result[count].description = "&lt;lop&gt;";
  break;
 case 10:
  result[count].description = "&lt;bop&gt;";
  break;
 case 11:
  result[count].description = "&lt;identifier&gt;";
  break;
 case 12:
  result[count].description = "&lt;integer&gt;";
  break;
 case 13:
  errorno++;
  result[count].description = "&lt;wrong integer or id&gt;";
  printf("%s(%d) : error '%s' : wrong integer or identifier\n",srcfilename,nowline,yytext);
  break;
 case 14:
  errorno++;
  result[count].description = "&lt;unknown identifier&gt;";
  printf("%s(%d) : error '%s' : unknown identifier\n",srcfilename,nowline,yytext);
  break;
 }
 result[count].content = yytext;
 fprintf(desfile,"%d: %s\t%s \n",count+1,result[count].content,result[count].description);
 count++;
 return 0;
}
int main(int argc,char **argv)
{ 
 int i = 0;
 if( argc != 2)
 {
  printf("No sourcecode processed : parameter error\n");
  return 0;
 }
 srcfilename = argv[1];
 strcat(desfilename,argv[1]);
 while (desfilename[i] != '.')
 {
  i++;
 }
 desfilename[i] = '\0';
 strcat(desfilename,".pl0");
 if( (srcfile = fopen(argv[1],"r")) == NULL )
 {
  printf("No sourcecode processed : cannot open the file \"%s\"\n",argv[1]);
  return 0;
 }
 if( (desfile = fopen(desfilename,"a")) == NULL )
 {
  printf("No sourcecode processed : cannot write the file \"%s\"\n",desfilename);
  return 0;
 }
 yyin = srcfile;
 yylex();
 fprintf(desfile,"\n");
 fprintf(desfile,"completed! welcome to lex by mp77,this is only a demo for compile principle.\n");
 printf("%s - %d error(s)\n",desfilename,errorno);
 printf("completed! welcome to lex by mp77,this is only a demo for compile principle.\n");
 fclose(desfile);
 fclose(srcfile);
 return 0;
}

 接下来我们会介绍如何利用lex工具生成本代码的C语言源代码，并使用vc6.0编译成win32 console程序。
