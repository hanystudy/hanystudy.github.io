---
layout: post
status: publish
published: true
title: MP77的UNIX课件笔记(7)
author: mp77
author_login: admin
author_email: 8621316@qq.com
excerpt: ! "1 UNIX系统调用\r\n\r\n1.1 System Call\r\n\r\nSystem call是OS提供的应用程序和OS交互的惟一手段，应用程序只能通过系统调用访问硬件资源。\r\n\r\n在
  Windows 中，OS 很少直接将System call提供给程序员使用，它提供类似缓冲文件操作那样既有用户态代码又有System call的函数库， 如 Windows
  API。\r\n\r\n"
wordpress_id: 173
wordpress_url: http://www.hanyi.name/blog/?p=173
date: 2009-06-01 00:02:27.000000000 +08:00
categories:
- Unix/Linux
tags:
- Unix/Linux
- linux
- c
- os
comments:
- id: 7623
  author: tramadol no prescription amex
  author_email: wonqyvea@tzdtdsjg.com
  author_url: http://blog.umphreys.com/cs/members/Tramadol-drug-interactions/default.aspx
  date: !binary |-
    MjAxMC0wNi0wNCAwNzowNDoxMiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0wMyAyMzowNDoxMiArMDgwMA==
  content: <a href="http://www.cdsra.org/community/members/Adipex+reviews.aspx" rel="nofollow">adipex
    cheapest price no prescription</a> , [url="http://www.cdsra.org/community/members/Adipex+reviews.aspx"]adipex
    cheapest price no prescription[/url] , http://www.cdsra.org/community/members/Adipex+reviews.aspx
    adipex cheapest price no prescription ,
- id: 7677
  author: tramadol hydrochloide
  author_email: kwawlihm@ctxzjrwx.com
  author_url: http://portal.prmag.com/_Tramadol-drug-interactions/photo/9634947/16633.html
  date: !binary |-
    MjAxMC0wNi0yMCAyMToxNDo0MiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wNi0yMCAxMzoxNDo0MiArMDgwMA==
  content: Great site. Good info.
- id: 7792
  author: msu pre soma
  author_email: wtzosmzt@wepnwiew.com
  author_url: http://connect.advance.net/user/Soma-Online-2138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAxOTo1Mzo0MCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxMTo1Mzo0MCArMDgwMA==
  content: Very interesting site. Hope it will always be alive!
- id: 7851
  author: adipex affiliate
  author_email: popdoltp@pcfqsghj.com
  author_url: http://connect.gulflive.com/user/Adipex-Online-w138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNiAyMDoyMjoxOCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxMjoyMjoxOCArMDgwMA==
  content: Excellent site. It was pleasant to me.
- id: 8318
  author: teva pharmaceuticals and tramadol
  author_email: lpvbndix@cpycoyux.com
  author_url: http://connect.gulflive.com/user/Tramadol-Online-9138m/index.html
  date: !binary |-
    MjAxMC0wOC0wNyAwMDoxNDoyMCArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOC0wNiAxNjoxNDoyMCArMDgwMA==
  content: Perfect work!
- id: 8817
  author: does phentermine test positive for methamphetamine
  author_email: tzgzepmt@gywyzyps.com
  author_url: http://www.wikisandbox.com/account/phentermine-1148m
  date: !binary |-
    MjAxMC0wOS0xOCAwMDozMzo0MiArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0wOS0xNyAxNjozMzo0MiArMDgwMA==
  content: Great site. Good info.
- id: 8857
  author: phentermine diet tablets
  author_email: iepngzuq@xmefxjgp.com
  author_url: http://specialtymatch.com/forums/members/Phentermine+sample.aspx
  date: !binary |-
    MjAxMC0xMC0xNyAwMDozMzoxMSArMDgwMA==
  date_gmt: !binary |-
    MjAxMC0xMC0xNiAxNjozMzoxMSArMDgwMA==
  content: Great site. Good info.
---
1 UNIX系统调用

1.1 System Call

System call是OS提供的应用程序和OS交互的惟一手段，应用程序只能通过系统调用访问硬件资源。

在 Windows 中，OS 很少直接将System call提供给程序员使用，它提供类似缓冲文件操作那样既有用户态代码又有System call的函数库， 如 Windows API。

<a id="more"></a><a id="more-173"></a>

同样的，在C语言中同时提供了一些标准库函数，从程序员的角度来说，这些与 System call 并不等同，其主要原因是：

1） System call 后立刻产生一个软中断，系统从用户态进入到内核状态，函数的功能由操作系统的内核代码实现。

2）普通函数同程序员编写的程序一样，通过函数调用，在用户态完成函数内部的处理后返回。有些库函数如缓冲文件fopen等，在函数里完成一些必要处理之后再引用系统调用。

为便于不同UNIX系统之间C语言源程序的移植，UNIX的系统调用函数的名字，参数排列顺序，参数类型定义，返回值的类型，以及实现的功能，都属于POSIX标准来规范的内容。

Windows也支持大部分的POSIX函数标准，所以许多UNIX的系统调用函数在Windows系统下也可以使用，Windows提供了功能等价的函数。

1.2 系统调用的返回值

UNIX的System call一般都返回一个整数值，例如：

1）errno.h中定义了许多宏名代替整数值。如EACCESS、EIO、ENOMEM等。每个System call的在线手册中都列出可能的出错情况和对应的错误代码的宏名。

2）char *strerror(int errno)可将整数错误代码转换成一个字符串。

3）void perror(const char *string);它会在标准错误输出设备上产生一条消息，描述在系统调用或使用库函数（库函数中使用了系统调用）期间所遇到的最后一个错误。先输出字符串string，再输出一个 冒号 和 空格 ,然后再打印出这条消息和换行符。

void perror(const char *string)定义在中。

2 文件和目录的访问

2.1 文件存取

在UNIX中，由于文件系统的支持，应用程序根据文件名访问文件，不用操心数据怎样存放在外存上。

首先引入文件描述符的概念。

在通常的认识里，处理整数比字符串更有效，索引整数叫“文件描述符”（file descriptor）。

系统根据它定位文件当前读写位置和文件inode节点信息。每个进程有自己的文件描述符，文件描述符从0开始编号，它是PCB里的一个数组的下标。

值得注意的是，文件操作一般使用“文件描述符”作参数而不是文件名，文件访问结束后，“关闭”操作释放占用的存储空间。

首先介绍，它是位于/usr/include/sys下的文件控制库文件引用，其实际地址为/usr/include/fcntl.h，该文件包含了了几乎所有与文件控制操作有关的系统调用定义。

2.1.1 打开文件

int open(char *filename, int flags);

int open(char *filename, int flags, mode_t mode);

open函数有两种定义方式，返回值为文件描述符。char* filename传入目标文件所处的路径（absolute or relative），int flags指定此次操作的限制范围，例如只读/读写，或是否创建新文件。flags通常为整形常量，但便于使用期间fcntl.h中还为其设置了宏定义描述。open的返回值通常为源文件的文件描述符，如果文件打开失败，则返回值为-1。

O_RDONLY 只读，Open for reading only；

O_WRONLY 只写，Open for writing only.；

O_RDWR 读/写，Open for reading and writing；

O_CREAT 不存在时创建，Create file if it does not exist.；

O_TRUNC 清空源文件，Truncate size to 0；

O_APPEND  在末尾添加，Append on each write；

mode定义了文件保护模式（该项在创建时有效），其值需要与系统掩码值综合后产生最终结果，在权限一节会详细介绍。

2.1.2 文件读写

首先介绍头文件unistd.h，其实际地址为/usr/include/unistd.h。unistd意为Unix standard header，即Unix标准库文件，作用类似于C语言的stdlib.h（standard library header，包含了标准函数以及NULL等宏定义内容）。Unix中与file I/O有关的的System call均包含在此文件内。

int write(int fd, void *buf, int n);

write()尝试将buf缓冲区中的内容输出至文件描述符所对应的磁盘数据区中，n指期望写入的字节数，其返回值为真正写入的字节数，如果该系统调用返回值为-1，则说明系统调用失败。

int read(int fd, void *buf, int n);

read() 尝试从文件描述符对应的磁盘数据区中输入文件内容至内存缓冲区中，n指期望读的字节数。返回值为实际读的字节数，如果该系统调用返回值为-1，则说明系统调用失败。

值得注意的是，read/write操作类似于文件的 顺序访问 机制，这是因为：

1、在内核中为每个打开的文件保留一个记录当前操作位置的指针；

2、无论是read还是write，都从文件的当前位置读取或写，操作后，文件当前指针自动向后移动；

3、这非常适合于按顺序访问文件中的数据；

这就可能出现类似下面的情况，在多进程同时对一个文件进行操作时，当read返回值为0时，此时已读至文件尾部，但如果文件被外部进程进行写操作时，会默认写至文件末尾，再次调用read会返回新的可能值。

System call还支持 随机访问 机制，这要利用如下函数（同样包含在unistd.h中）：

off_t lseek(int fd,off_t offset,int whence);

off_t offset定义强制移动当前文件读写指针的字节偏移量，int whence指offset的实际参考基准，以上两个参数在/usr/include/sys/types.h中定义。其中关于whence的宏定义如下：

SEEK_SET，读写指针移动到offset指定的位置（绝对值）；

SEEK_CUR，读写指针移动到当前读写指针加offset后的位置；

SEEK_END，读写指针移动到文件尾加offset后的位置，也就是文件中字节数加offset后的位置。

在后两种方式中，允许offset为负值。

利用lseek时仍然需要注意系统在文件管理机制上的问题。一般来说，如果lseek被指定为超过文件末尾之后的位置，那么对应用程序来说文件大小的确是目前“实际的文件大小”，但是对系统来说，在块管理机制的情况下仅仅会给多余的非空内容分配出相应的存储块（这就可能存在占用1-9块和100块而中间块不占空间的情况）。此时当文件被写入时会导致系统分配真正的磁盘存储块，就造成了大量的“空块”浪费的情况，绝大多数情况下这是毫无意义的，因而应尽量避免出现此类情况。

System call还允许用户截断一个文件：

int ftruncate(int fd,off_t length);

length定义了截取的文件字节长度，当length为0则文件长度即变为0。

2.1.3 文件关闭

int close(int fd);

首先需要注意：

1、每个程序允许同时打开的文件数是有限的，整个系统允许打开的文件总数，也是有限的。

2、程序无论正常还是异常退出，所有由程序打开但尚未关闭的文件描述符，系统都会自动关闭。

3、文件访问的另一种方式是使用C语言的标准函数fopen，fprintf，fscanf，fgets，fread，fwrite，...，close。这一组函数，是在系统调用open，read，write，...，close基础上构建的库函数。

因而尽管程序终止时文件描述符总会被强制关闭，但在一些后台程序中，close的作用对资源的合理分配是不可忽视的。

2.2 目录访问

2.2.1 与普通数据文件相同的方式打开目录文件

目录和普通文件一样，占用文件存储区中的空间。目录中的数据是“文件名-inode节点”对。

早期的UNIX每个目录项16B，包括2B的inode节点号和14B文件名。inode节点号为0标志文件已删，因此inode节点1开始编号。用open以只读方式打开一个目录文件，read得到16B目录项。程序自己来分析inode节点号和文件名。

使用长文件名后，目录存储结构发生了变化，应用程序必须了解目录文件的存储结构，这很不方便。因此系统提供了库函数，用于操作目录文件。可将目录文件和普通文件一样打开读，但内核保留写目录的权利，任何用户态程序，就算是超级用户，也不允许改写目录内容，这样，保证其中数据的结构正确和有效。

当以处理普通磁盘文件的方式打开目录读取目录文件数据时，由于需要根据目录表的存储结构在二进制数据中判断文件名、inode节点号，这对普通用户来说是极不方便的。

2.2.3 目录有关的System call

首先介绍/usr/include/dirent.h，即directory entry目录项头文件，定义了有关Unix目录操作的System call和宏定义。

DIR *opendir(char *dirname);

char *dirname指定目标目录路径（absolute or relative），该调用返回该目录的标志句柄，DIR在dirent.h中定义。

struct dirent *readdir(DIR *dir);

readdir取第一个目录项的内容，并返回dirent型结构体指针，dirent定义了目录项包括的具体属性，常用的有d_name（char *）、d_ino（int）等，且当前句柄指针跳至下一目录项等待读取。readdir返回NULL,标志目录表已经读到尾。

2.2.4 目录操作的其它System call

这里不再详述其使用方法，事实上这些都是显而易见的。

int closedir(DIR *dir);     //关闭目录表访问句柄

int unlink(char *pathname);    //删除文件

int link(char *oldpath,char *newpath);  //建立硬连接

int symlink(char *oldpath,char *newpath); //建立符号连接

int rename(char *oldpath,char *newpath); //文件改名

int chdir(char *path);     //修改当前工作目录

int getcwd(char *buf,int size);   //当前工作目录名

int mkdir(char *pathname,mode_t mode); //创建目录

int rmdir(char *pathname);    //删除目录

后续文章中我们将利用以上System call进行实际应用示例研究。
