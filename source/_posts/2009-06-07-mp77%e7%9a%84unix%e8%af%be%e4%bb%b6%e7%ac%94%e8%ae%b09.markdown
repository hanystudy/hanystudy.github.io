---
layout: post
status: publish
published: true
title: MP77的UNIX课件笔记(9)
author: mp77
author_login: admin
author_email: 8621316@qq.com
excerpt: ! "本章和下一章讨论UNIX进程控制和进程间通信机制和相关System call。从“操作系统原理”中我们了解到，进程是OS进行系统资源管理的基本单位。其具体的内容则不属于本章讨论范围，但我们假设读者已经阅读过有关操作系统进程管理方面的文献。\r\n\r\n"
wordpress_id: 177
wordpress_url: http://www.hanyi.name/blog/?p=177
date: 2009-06-07 09:49:39.000000000 +08:00
categories:
- Unix/Linux
tags:
- Unix/Linux
- linux
- c
- os
comments: []
---
本章和下一章讨论UNIX进程控制和进程间通信机制和相关System call。从“操作系统原理”中我们了解到，进程是OS进行系统资源管理的基本单位。其具体的内容则不属于本章讨论范围，但我们假设读者已经阅读过有关操作系统进程管理方面的文献。

<a id="more"></a><a id="more-177"></a>

1 进程控制

进程是如何创建的？我们以日常工作的shell平台为起点展开讨论。

1.1 用程序运行程序

简单地说，shell是用于管理程序的系统工具，其实质也是一个程序。

用程序运行程序的思想来实现一个简单shell，我们想到被称为exec的System call。exec的特点是：

1、exec创建执行其它程序的进程，而不必返回。

2、这些例程简单的把新程序覆盖在旧程序上，并以调用者提供的参数去执行这个新代码。

下面是一段使用exec运行ls的程序示例：

main()

{

/*the definition below can also be made like this:

char *args[4]={"ls","-l","/usr/bin","(char*)0"}*/

char *args[4];

args[0] = "ls"; /* build the arglist */

args[1] = "-l";

args[2] = "/usr/bin";

args[3] = (char *) 0; /* terminate with NULL */

printf("about to execute ls -l /usr/bin\n" );

execvp( "ls" , args );

printf( "that was ls, cool, eh?\n");

}

exec包含六种形式，除了建立在exec基础上之外：

l与v，指定命令行参数的两种方式，l代表的形式在函数参数中列举出命令行参数，v要实现组织成一个数组。函数参数中的命令行参数表，参数数目可变，所以最后一个要以0为结束标识。

e，用envp数组的内容初始化环境参数，否则使用与当前相同的环境参数environ。

p，函数第一个参数，是程序文件名，在环境变量PATH指定的多个查找目录中查询可执行文件，否则不按PATH指定路径自动搜索。

由于exec覆盖源进程直接运行目标程序，因此上段C代码中的最后一行输出that was ls,cool,eh?并不会显示在标准输出。

很明显，仅仅使用exec无法满足我们的需求，这种需求主要表现在：

1、为了保护旧程序，要把它分割成两个副本：其中一个被覆盖，而另一个则等待新的覆盖进程的结束。

2、调用成功不返回；失败返回。

为了实现以上功能，需要建立新的进程供exec调用。

1.2 进程创建

Unix涉及进程创建的基本System call是int fork()。fork()调用成功内核建立一个新进程。新进程是调用fork()的进程的副本。即新创建的进程继承了其父进程的环境。

新进程(子进程）运行与创建者（父进程）一样的程序，其中的变量与创建进程的变量相同.

父进程与子进程并发执行，哪一个进程能够占用CPU由进程调度程序决定。它们都是从fork()调用后的那条语句开始执行。例如程序段：

main()

{int pid;

printf(“for example”);

pid=fork();

printf(“fok system calll”);

……

}

标准输出中至少会出现fok system call * 2的显示，且表象上根本无法分别“父”与“子”。简单地说，父子进程间互不影响其数据堆栈的情况。

注意到fork的返回值为整型，实际上就是子进程的process id。也就是说，fork会同时返回两个分别指示父子进程的pid。其中：

1、父进程的返回值为子进程的PID；

2、子进程的返回值为0；

3、出错返回值为-1，并将errno置为相应值。

#include

main(){

int ret_from_fork, my_pid;

my_pid = getpid();

printf("Hi, my pid is %d\n", my_pid );

ret_from_fork = fork();

sleep(1);

printf("after fork().  It returned %d and my pid is %d\n",

ret_from_fork, getpid());

}

本段程序演示了fork父子进程的pid状态。至于父子进程标识谁最先显示，完全由CPU决定。

1.3 空闲进程

现在回过头来再看shell，似乎心中有数了许多。紧接着问题来了，我们现在知道shell程序通过创建子进程运行新程序，那么此时父进程处于什么地位？子程序运行结束后又是如何回退至父进程的?

显然父进程需要做的是在子进程运行过程中等待，直到子进程结束。这就需要用到/usr/include/sys/wait.h中的System call了。

pid_t wait(int *status);

其中pid_t定义在/usr/include/sys/types.h中，返回值代表已终止的子进程的pid号。status的作用是返回一个整型值，其代表了子进程终止的原因。子进程的终止一般有两种情况：“自杀”和“被杀”，“自杀”即自愿终止，在程序中调用函数exit()或主函数return均可。“被杀”是指由其它进程或者操作系统内核向进程发送信号将其杀死。第一种情况可以获得进程正常终止的返回码，第二种情况可以获得进程被杀死的信号值，“信号值”会在下一篇文章讨论。

在/usr/include/sys/wait.h中定义了几个宏支持返回status的调用：

WIFEXITED(status)，如果进程正常终止，则为真。调用WEXITSTATUS(status)可以返回子进程的返回码。

WIFSIGNALED(status)，如果进程异常终止，则为真。调用WTERMSIG(status)获得子进程被杀的信号值。

严格说来，status的低8位反映了子进程的终止状态：0表示子进程正常结束，非0表示出现了各种各样的问题。status高8位带回了exit的返回值。低7位记录信号序号,bit 7 用来指明发生了错误并产生了内核映像(core dump)。

1.4 简单shell的实现

至此，我们完全有能力制作一个简单的仿真shell程序了，尽管尚无法实现有关信号、文本编辑器以及一些其它特殊功能的调用。

从本篇开始，我们将构建一个shell及其基本系统命令的C语言程序，此项工作会持续到本系列连载的结束。

/* example of simple shell */

#include

#include

#define   MAXARGS    20 /* cmdline args */

#define   ARGLEN 100 /* token length */

main()

{char *arglist[MAXARGS+1]; /* an array of ptrs */

int numargs; /* index into array */

char argbuf[ARGLEN]; /* read stuff here */

char *makestring(); /* malloc etc */

numargs = 0;

while ( numargs &lt; MAXARGS )

{   printf("Arg[%d]? ", numargs);

if ( fgets(argbuf, ARGLEN, stdin) &amp;&amp; *argbuf != '\n' )

arglist[numargs++] = makestring(argbuf);

else

{  if ( numargs &gt; 0 ) /* any args? */

{ arglist[numargs]=NULL; /* close list */

execute( arglist ); /* do it */

numargs = 0; /* and reset */

}

}}

return 0;}

execute( char *arglist[] )

{    int pid,   exitstatus; /* of child */

pid = fork(); /* make new process */

switch( pid ){

case -1:

perror("fork failed");

exit(1);

case 0:

execvp(arglist[0], arglist); /* do it */

perror("execvp failed");

exit(1);

default:

while( wait(&amp;exitstatus) != pid );

printf("child exited with status %d,%d\n",

exitstatus&gt;&gt;8, exitstatus&amp;0377);

}

}

char *makestring( char *buf )

/*

* trim off newline and create storage for the string

*/

{

char *cp, *malloc();

buf[strlen(buf)-1] = '\0'; /* trim newline */

cp = malloc( strlen(buf)+1 );         /* get memory */

if ( cp == NULL ){ /* or die */

fprintf(stderr,"no memory\n");

exit(1);

}

strcpy(cp, buf); /* copy chars */

return cp; /* return ptr */

}

/* end of example */

1.5 字符串解析的C语言细节处理

通过上述程序我们可以获得一种对输入字符串进行格式解析的方法，事实上C语言库函数中即给出了一些极为方便的函数调用。

char *strtok(char *string, char *tokens);

1、给定字符串string以及“单词”分界符的集合tokens，从字符串中分析出一个“单词”。忽略字符串中连续多个的单词分隔符。

2、函数返回值是指向单词的首字符的指针。

3、函数的副作用是，会修改原先给定的字符串存储空间中的内容。

以下是strtok运行原理解析：

首先键盘输入 who  am i后按enter，应当注意who之前有一个空格和一个制表符（tab），who和am之间有两个空格。

下面是用fgets(s, sizeof s, stdin)读入到内存中的字符串s的存储结构，每个字节用十六进制列出。

\t w h o a m i \n \0

s 20 09 77 68 6f 20 20 61 6d 20 69 0a 00

第一次执行p=strtok(s," \t\n")之后的状态:

\t w h o a m i \n \0

s 20 09 77 68 6f 00 20 61 6d 20 69 0a 00

p

第二次执行p=strtok(NULL," \t\n")后的状态:

\t w h o a m i \n \0

s 20 09 77 68 6f 00 20 61 6d 00 69 0a 00

p

第三次执行p=strtok(NULL," \t\n")后的状态:

\t w h o a m i \n \0

s 20 09 77 68 6f 00 20 61 6d 00 69 00 00

p

这时，strtok扫描结束的位置指向了字符串尾部的'\0'，第四次再执行strtok就会返回NULL，表示找不到新的单词。

1.6 另类的进程创建方法

vfork()创建一个进程，而新进程的目的是exec一个新程序。vfork与fork一样都创建一个子进程，但它并不把父进程的地址空间完全复制到子进程，因为子进程会立即调用exec（或exit），于是也就不访问该地址空间，不过它在exec(或exit)调用之前，它在父进程空间运行。

vfork()保证子进程先运行，在它调用exec(或exit)之后父进程才运行。

这里需要明确两个终止操作的区别exit和_exit，它们均定义在/usr/include/unistd.h中，不同的是前者属于C语言库函数的范畴，而_exit属于System call，也就是说，在具体实现中，exit最终仍需要调用_exit。

但exit终止进程时比系统调用_exit多做一些操作。这些操作主要是自动关闭用fopen等函数创建的缓冲I/O文件，将缓冲区里的数据写到磁盘上。exit释放缓冲文件的文件缓冲区和缓冲文件所需要的数据结构，因为库函数相关的这些数据都存放在进程的数据区内，这会影响子进程借用的父进程的数据段，所以，当采用vfork()创建进程时，必须直接使用系统调用_exit。

例如下段程序：

# cat vf.c

int main(void){

int a = 1;

if (vfork() == 0) {

a++;

_exit(0);

} else {

a++;

}

printf("a=%d\n", a);

}

# cc vf.c -o vf

# ./vf

a=3

显然子进程使用了父进程的数据区进行操作。

1.7 另类的程序运行方法

在C语言标准库中还定义了这样的函数，其本质上使用了fork、exec、wait等System call，用于执行某一行命令。

int system(char *cmd)

其中cmd是可执行程序的名称。该系统调用的功能是执行参数cmd所指定的命令。正确返回0，否则返回错误代码（非0值）。它的执行与命令行键入的命令具有同样的效果。

现在考虑如下实际问题：

编程时希望在程序中获得系统当前的IP转发路由表，一时又找不到编程接口，但是，执行netstat -rn命令后可以获得这个表格。选项r指的是路由表，选项n指的是输出IP地址时用数字形式，而不是自动将地址转换为主机名。

将命令输出重定向到一个临时文件中，然后从程序中读这个文件的内容，获取所希望的信息。最后，把文件删除。具体实现如下：

# cat cmd.c

#include

main(){

char fname[256], cmd[256], buf[256];

FILE *f;

tmpnam(fname);

sprintf(cmd, "netstat -rn &gt; %s", fname);

printf("Execute \"%s\"\n", cmd);

system(cmd);

f = fopen(fname, "r");

while(fgets(buf, sizeof buf, f))

printf("%s", buf);

fclose(f);

printf("Remove file \"%s\"\n", fname);

unlink(fname);

}

唯一需要解释的是char * tmpnam(char *str);

其作用是获得一个临时文件的文件名，库函数会保证这个文件名与已有文件名不冲突。

库函数sprintf的用法和printf类似，只是打印的字符存到了字符串而不是显示在终端输出。sprintf使用和printf一样的格式控制字符串，程序员还经常用它将二进制数字转换成ASCII码串。

程序把system(cmd)得到的文件打印出来。需要时，程序应该解读这个文件，获得所需要的信息。

1.8 进程信息获取命令

ps（process status）命令列出系统中进程的当前状态，实际上就是将进程系统数据段中的部分进程属性有选择地打印出来。不同的UNIX系统，ps命令的使用也有些差别。

-a （all）列出所有当前终端上启动的进程；

-t （tty）指定一个终端名字，列出这个终端上启动的所有进程，如ps -t pts/1；

-u （User）指定一个用户名，列出这个用户的所有进程，如ps -u jiang；

-e （everything）列出系统中所有的进程；

-f （full）以full格式列出进程；

-l （long）以long格式列出进程。

下一节将介绍进程间通信的相关原理和方法。
