---
layout: post
status: publish
published: true
title: C++学习笔记(重温计划之四)
author: mp77
author_login: admin
author_email: 8621316@qq.com
wordpress_id: 36
wordpress_url: http://www.hanyi.name/blog/?p=36
date: 2008-04-08 10:49:37.000000000 +08:00
categories:
- c++/c
tags:
- 连载
- cpp
comments: []
---
3、数据类型（中）

 1）C-串与string

   c++的字符串分两类，一种是由c语言沿袭而来，称为c-串。例如，

   char buffer[7]="Hello!"; //由于所有字符串必须以\0为结束符，则长度定义必须比实际元素个数多1

   事实上，字符数组的实际类型为char*，或const char*，称为字符型指针。实际应用中我们做如下定义:

   char* str="Hello!";//输出*str时为"H"，str则为"Hello"

   在c库函数中，又定义了字符串操作函数，例如strcmp(比较)、strcpy(复制)、strcat(连接)、strrev(倒置)、strset(设置)、strstr/strchr(查找串或字符)。通常我们需要指定头文件string.h，标准c++将其嵌套入iostream中，即省略了这一调用。

  其实，C++中提供了另一种高效可行的字符串类型，即string类型。string类是标准c++中的基础类，这在后文中我们会有详细解释。string的目的是使字符串操作更加方便安全。

#include"iostream"

#include"algorithm"

using namespace std;

int main(){

   string a,s1 = "Hello!";

   string s2 = "123";

   a=s1;                                                                                    //复制

   cout &lt;&lt; (a==s1 ? " " : "not") &lt;&lt; "equal\n";                          //比较

   cout &lt;&lt; a+s2 &lt;&lt;endl;                                                         //连接

   reverse(a.begin(),a.end());                                                     //倒置串

   cout &lt;&lt;a &lt;&lt; endl;

   cout &lt;&lt; a.replace(0,9,9,'c')&lt;&lt;endl;                                     //设置

   cout &lt;&lt; (s1.find("ell")!= -1 ? "" : "not") &lt;&lt; "found\n";        //查找串

   cout &lt;&lt; (s1.find('c')!= -1 ? "" : "not") &lt;&lt; "found\n");          //查找字符

}

   由上基本可以看出，string在与c-串对比中是明显有优势的。而且，c-串可以随时赋值给string变量，具有相当的亲和程度.

  现在讨论string与c-串在输入输出中的c++用法.

   当输入时,我们以:

   fot(string s;cin &gt;&gt; s;)            //string s可以替换为char s[10]

   cout &lt;&lt; s &lt;&lt;" ";

   cout &lt;&lt; endl; 

   cin &gt;&gt;在读入时一般过滤前导空格,包括空格、回车、水平/垂直制表符等等。

  我们同样可以采用非循环的方式。例如:

   string s;                           //在字符数组中，可以定义char s[10]

   getline(cin,s);                   //相应可以改为cin.getline(s,10)

   cout &lt;&lt; s &lt;&lt;endl;           //getline的好处是，在读入多行字符时，程序可以将内容逐行分解操作

  下面假设，存在一个文件aaa.txt,文件内有若干行整数，并且每行整数个数未知。这时求每行的整数和。

#include&lt;iostream&gt;                      //该程序已调试通过
#include&lt;sstream&gt;
#include&lt;fstream&gt;

using namespace std;
int main(void){
 ifstream in("aaa.txt");
 for(string s;getline(in,s);){              //逐行读入的循环
  int a, sum = 0;
  for(istringstream sin(s);sin&gt;&gt;a;sum+=a);          //istringstream sin(s)意在创建输入string流sin()，sin&gt;&gt;即从string流中将整数输入到a中进行运算
  cout &lt;&lt; sum &lt;&lt; endl;
 }
 getchar();
 return 0;
}

  上面的程序体现了string流的概念，进一步体现了string字符串型的优势和特点。

  2）数组

   int a[5];   //即定义了一个数组，5代表了数组中元素个数。并且[ ]内必须为常量表达式，通常我们可以使用const int定义。

   数组的初始化过程中，有如下几种方式进行:

   int iArray[10] = {1,2,3,4,5,6,7,8,9,10};

   请注意，以上方式禁止的有:元素个数大于定义数、中途有元素定义为空、元素省略...不过实际操作中可以使元素个数少于定义数，比如  int iArray[10] = {1,2,3,4,5,6,7,8}; 这是允许的。

　int iArray[] = {1,2,3,4,5,6,7,8,9,10};  //虽然没有定义常量值，但初始化值的个数已经做出说明了。

　我们还能用到sizeof(a[n])来返回a[n]元素所占空间的字节数。

　下面来探测未初始化数组元素的默认值情况。计算机把这一类数组区分为全局数组或静态数组、局部数组。在全局数组和静态数组中，未定义情况下的元素值恒为０，而在局部数组中则变成不确定数。

　数组也有多位数组的形式，可定义为:

   int a[3][5];   //这是一个２维数组，其输入输出方式和１维数组是相同的
