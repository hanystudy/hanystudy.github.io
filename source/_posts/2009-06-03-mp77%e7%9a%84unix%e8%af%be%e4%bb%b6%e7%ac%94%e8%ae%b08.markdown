---
layout: post
status: publish
published: true
title: MP77的UNIX课件笔记(8)
author: mp77
author_login: admin
author_email: 8621316@qq.com
excerpt: ! "本章主要介绍目录和文件的状态信息，这些是大量系统功能应用的基础。后续文章会针对前次连载的文件系统、System call等内容进行部分常用程序的设计以及分析工作。\r\n\r\n"
wordpress_id: 175
wordpress_url: http://www.hanyi.name/blog/?p=175
date: 2009-06-03 17:11:21.000000000 +08:00
categories:
- Unix/Linux
tags:
- Unix/Linux
- linux
- c
- os
comments: []
---
本章主要介绍目录和文件的状态信息，这些是大量系统功能应用的基础。后续文章会针对前次连载的文件系统、System call等内容进行部分常用程序的设计以及分析工作。

<a id="more"></a><a id="more-175"></a>

1 文件状态信息

1.1 获取文件的状态信息

该部分系统调用定义在/usr/include/sys/stat.h中，并且需要types.h的支持。

int stat(char *path, struct stat *statbuf); /* return 0 on success or -1 on error */

int fstat(int fd，struct stat *statbuf); /* returns 0 n success or -1 on error */

stat和fstat的原理均是从inode节点中获取文件状态信息。值得注意的是，如果是利用C语言缓冲I/O方式打开文件，将FILE *f转换为相应文件描述符的方法是，使用宏调用fileno(f)即可返回discriptor。stat由于是提供了路径，因此可能需要逐级深入，导致效率普遍不如fstat，但考虑到fstat仅适用于已打开的文件，那么两种call就需要灵活使用了。

注意到参数struct stat *statbuf定义了存储文件状态信息的结构体，其在types.h中定义，其基本成员如下：

struct stat {

dev_t     st_dev;        /*device of inode*/

ino_t     st_ino; /* inode  number*/

mode_t   st_mode; /* mode bits */

nlink_t   st_nlink; /* number of links to file */

uid_t     st_uid; /* owener’s userid */

dev_t     st_gid; /* owener’s group id */

dev_t     st_rdev; /* for special files */

off_t     st_size; /*file size in characters */

time_t    st_atime; /*time file last read*/

time_t    st_mtime; /*time file last written or created*/

time_t st_ctime; /*time file or inode last changed*/

}

尽管stat成员类型大部分均为整型，但基于可移植性的需要，通常不直接定义为C语言的内部数据类型如short、int或long等等。可以发现，此类类型定义都是以_t结尾的使用typedef重定义后的类型声明符。

1.1.1 st_mode转换成字符

st_mode字段长度为16位，包括4位的类型标识，suid、sgid和sticky字段以及9位权限标识位。

类型标识位我们在连载五中已经有所涉及：

http://www.hanyi.name/blog/?p=169

suid和sgid涉及到系统安全，它们分别指Set User ID和Set Group ID，其作用类似。对一个suid被置位的程序来说，其权限设置示例如下:

rwsr--r--

这里需要解释一下effective uid和effective gid，它们是记录在内核中相对于用户uid和gid的标识，其主要目的是为了分配给其相应的权限。一般情况下，如果suid和sgid未被置位，那么进程显示的euid应是正在执行操作的用户uid。但是在suid被置位的情况下，euid会显示为程序拥有者的uid，并且该进程享受拥有者权限。

简单举例/usr/bin/passwd命令，功能是修改用户密码，其实质是对/etc/passwd和/etc/shadow进行更新操作。我们知道/etc/.一般对普通用户不具备wx权限。借助于/usr/bin/passwd，该程序的默认权限为：

rws--x--x

那么普通用户就可以以root身份对上述两个文件进行w操作了。

sgid的作用与suid类似，创建该位的主要目的是当涉及到root权限时，如非必要应置sgid而非suid以避免造成安全缺陷。

sticky粘贴位，该位一般只在目录文件定义时有效。sticky置位的目录下所有用户都可以在该目录下创建文件，但除root外的用户只能删除自己创建的文件。

这里可能需要解释一下Unix的文件删除机制。严格说来删除权限和源文件的mode字段无关，而是与存放该文件的目录文件权限有关，如果允许相应用户拥有删除权限，那么用户必须在该目录下拥有写权限。

但是/usr/include/sys/stat.h中对标识的宏定义为八进制，且无法与st_mode中的bit直接对应，为此要借助于掩码运算计算出相应的字段定义，计算方法是st_mode &amp; 相应的标识掩码（其中&amp;为位运算中的“与”）。例如文件类型宏：

#define  S_IFMT  0170000  /*type of file*/

#define  S_IFREG 0100000  /* Regular file */

#define  S_IFDIR 0040000  /* Directory */

#define  S_IFCHR 0020000  /* Character device */

#define  S_IFBLK 0060000  /* Block device */

#define  S_IFIFO 0010000  /* FIFO*/

#define  S_IFLNK 0120000  /* Symbolic link */

#define  S_IFSOCK 0140000 /* SOCKET*/

S_IFMT即为文件类型的掩码，运算时只需st_mode &amp; S_IFMT，判断其相应的文件类型宏编码即可。下面仍列出一些/usr/include/sys/stat.h宏定义编码：

#define S_ISUID   0004000    /*set user  id on execution*/

#define S_ISGID   0002000   /*set group id on execution*/

#define S_IREAD 0000400 /*read permission */

#define S_IWRITE  0000200 /*write permission */

#define S_IEXEC  0000100  /*execute/search permission,owner*/

在编写程序的时候，可能会利用如下代码：

if  ((info.st_mode &amp; S_IFMT) == S_IFDIR)

printf(“this is a directory”);

/usr/include/sys/stat.h同样给出了此类转换的宏定义，例如：

#define  S_ISDIR(mode)   (((mode)&amp;S_IFMT)==S_IFDIR)

#define  S_ISREG(mode)   (((mode)&amp;S_IFMT)==S_IFREG

#define  S_ISBLK(mode)   (((mode)&amp;S_IFMT)==S_IFBLK

#define  S_ISCHR(mode)   (((mode)&amp;S_IFMT)==S_IFCHR

那么就可以实现本节题目所提出的问题了：

void mode_to_letters( int mode, char str[] ){

strcpy( str, "----------" );                    /* default=no perms */

if ( S_ISDIR(mode) )    str[0] = 'd';     /* directory?       */

if ( S_ISCHR(mode) )   str[0] = 'c';    /* char devices     */

if ( S_ISBLK(mode) )    str[0] = 'b';    /* block device     */

if ( mode &amp; S_IRUSR )  str[1] = 'r';    /* 3 bits for user  */

if ( mode &amp; S_IWUSR ) str[2] = 'w';

if ( mode &amp; S_IXUSR )  str[3] = 'x';

if ( mode &amp; S_IRGRP )  str[4] = 'r';    /* 3 bits for group */

if ( mode &amp; S_IWGRP ) str[5] = 'w';

if ( mode &amp; S_IXGRP )  str[6] = 'x';

if ( mode &amp; S_IROTH )   str[7] = 'r';    /* 3 bits for other */

if ( mode &amp; S_IWOTH )  str[8] = 'w';

if ( mode &amp; S_IXOTH )   str[9] = 'x';

}

1.1.2 时间字段转换成字符

1.1中已经给出了文件状态信息中的st_atime（read）、st_mtime（write or create）和st_ctime（change）时间字段的定义，为了将其转换为标准系统时间，需要使用/usr/include/time.h中的System call函数。其基本格式为：

char *ctime(const time_t *clock);

注意到stat中的时间字段均为time_t型，也是Unix中系统定义的基本类型。ctime的目的是将time_t转换为标准时间格式，并以字符串形式回传。其最小单位时间为秒，采用UTC(Coordinated Universal Time )时间，其绝对值00:00:00代表1970年1月1日00:00:00，返回的格式示例如下：

Thu Nov 24 18:22:48 1986

值得注意的是，当年份字段长度小于4个字节时需要用前导0补齐，当年份大于4个字节时，显示会成为：

Thu Nov 24 18:22:48     81986

在年份字段之前会出现5个空格，这是为了防止在一些老式软件中将26个字节的输出判断为超出时间范围而显示出错。

showtime( time_t timeval )

{

char *ctime(); /* convert long to ascii */

char *cp; /* to hold address of time    */

cp = ctime( &amp;timeval ); /* convert time to string */

printf("%12.12s", cp+4 ); /* pick 12 chars from pos 4 */

}

以上是一段输出time_t类型时间的程序示例。

1.1.3 st_uid &amp; st_gid 转换成字符

st_uid和st_gid是在stat字段中记录文件拥有者ID和拥有者所在用户组ID的字段。前文已经提到用户信息和用户组信息分别存储在/etc/passwd和/etc/group中，因而要获取详细的用户或组信息就必须实现读取并分析操作，系统给出了此方面的System call。不同类型的Unix发行版其passwd结构也略有不同，这就造成了其相应结构体字段的不同。这里用OpenBSD的/usr/include/pwd.h定义加以说明，其它系统的设定较之并不全面：

struct passwd {

char      *pw_name;          /* user name */

char      *pw_passwd;       /* encrypted password */

uid_t       pw_uid;             /* user uid */

gid_t       pw_gid;              /* user gid */

time_t     pw_change;        /* password change time */

char       *pw_class;           /* user access class */

char       *pw_gecos;          /* login info */

char       *pw_dir;               /* home directory */

char      *pw_shell;            /* default shell */

time_t      pw_expire;         /* account expiration */ };

}

其调用函数为struct passwd *getpwuid(uid_t uid);

st_gid定义在/usr/include/grp.h中，其结构体字段如下：

struct group {

char     *gr_name;          /* group name */

char      *gr_passwd;       /* group password */

gid_t     gr_gid;              /* group id */

char      **gr_mem;        /* group members */

};

其调用函数为struct passwd *getgrgid(uid_t uid);

2	设备文件

2.1	一些概念

我们在连载5中已经介绍过特殊文件类型，它是Unix一种独特的文件类型。特殊文件类型总的说来包含物理设备文件和逻辑设备文件，Unix的设备管理将设备组织得看起来同磁盘文件一样，可以通过文件系统里的文件名进行访问。

UNIX的设备分为块设备和字符设备：

1、块设备面向磁盘等可以随机访问的存储设备

2、mkfs，mount等使用块设备文件名，映射块设备。

3、终端、打印机等为字符设备，也叫做“原始（raw）”设备。

所有的设备在文件系统中都有一文件名，在文件的inode节点中记录了文件类型是块设备文件或者字符设备文件。

每个设备都对应一个主设备号和次设备号，也记录在inode节点中。按照惯例，设备文件都放在/dev目录下。

我们通过ls -l查看一些设备文件时得到如下信息：

# ls -l /dev/hdc1 /dev/fd[01] /dev/tty1

brw-rw---- 1 root floppy  2, 0 Jan 30  2003 /dev/fd0

brw-rw---- 1 root floppy  2, 1 Jan 30  2003 /dev/fd1

brw-rw---- 1 root  disk  22,1 Jan 30  2003 /dev/hdc1

crw------- 1 root  root  4, 1 Jun  2 20:32 /dev/tty1

其中主设备号和次设备号的作用列出如下：

1、主设备号对应物理设备的一类。

2、设备能够正常工作，需要提供设备驱动程序。

3、设备驱动程序中有多个入口函数，设备驱动程序安装时注册这些函数，安装完成之后，会得到一个主设备号。

4、主设备号与驱动程序相关联，根据主设备号，可以检索到已经登记了的这个设备的设备驱动程序的入口函数。

5、主设备号用来定位所使用的设备驱动程序，次设备号用来区分这一类物理设备的哪一个。

要创建设备文件，需要使用mknod命令。

# mknod /dev/ttyE0 c 16 0

/dev/ttyE0是要创建的设备文件名，c指的是字符设备，16是主设备号，0是次设备号。

2.2	随机数生成器/dev/random

我们在连载5详细介绍了逻辑设备文件/dev/null和/dev/zero，逻辑设备文件也被称作虚拟设备文件，设备驱动程序不操作任何物理硬件,它像普通设备驱动程序一样提供设备驱动程序入口，通过这些入口，可以和内核交互作用，得到某些数据。这些设备叫做“虚拟设备”。

如设备文件/dev/mem和/dev/kmem可以通过某些操作获取内核中的一些数据表格内容，常常被随操作系统软件带来的一些运行在用户态的工具使用。

这里再引入一个常用的虚拟设备文件——/dev/ranodm。

在Linux中，虚拟设备文件/dev/random是一个随机数生成器，根据系统键盘和鼠标等外设的活动生成二进制的随机数数列。可以在用户自己编写的程序中使用这个设备文件。

命令od -x /dev/random可以观察这些随机数序列。在系统外设都不活动时，这个设备暂停供应随机数序列。

另一个虚拟设备文件/dev/urandom，无论外设是否活动，只要你的程序读取该设备，都会源源不断地供应随机数序列。命令od -x /dev/urandom可以观察到这些随机数序列。

2.3	终端设备文件

每个终端设备，无论是网络虚拟终端设备，还是一个真正的终端设备，都对应一个名字。可以按名字像普通文件那样使用终端，但是不方便的是无法决定当前终端到底是哪个终端。

特殊的设备文件/dev/tty就是当前终端，而无论实际使用的终端到底是哪个。尽管scanf和printf等函数可以从当前终端获得输入和从当前终端输出，但是标准输入和标准输出的重定向功能，会使得程序不再从当前终端输入和输出数据。

对于程序员来说，当程序在这种情况下仍然需要和用户交互时，/dev/tty文件特别有用。程序可以打开/dev/tty文件输入和输出数据，在重定向环境中，仍然保持从当前终端输入和输出数据。

3	文件和目录权限

尽管前部分内容很多涉及到了文件和目录权限的问题，但并没有详细描述文件系统的权限控制方法和掩码运算原理。

3.1	权限控制

UNIX的每个文件和目录，都有对应的一组权限存放在它的inode节点中，用于控制用户对它的访问。每个文件都有惟一的属主和组号，记录在inode节点中。

UNIX有命令chown和chgrp可以修改文件的属主和组，也有相应的系统调用函数chown() 。在inode节点中记录的权限有9b，描述3个级别，分别是文件主、同组用户、其他用户。每个级别的权限都包括3部分：读权限、写权限、执行权限。

1、普通文件的权限

用户对普通文件文件具有读权限，用户可以读取文件。

用户对文件具有写权限，可以修改文件的内容。

用户对文件具有可执行权限，可以执行这个文件。

2、目录的权限

目录文件存储了多个由“文件名-i节点号”组成的目录项。目录的读写权限，就是对这个目录表文件的读写权限。

目录无读权限，目录文件不许读，ls操作会失败。

目录无写权限，目录文件修改的操作都被禁止。创建文件、删除文件、文件改名、复制文件到该目录、创建子目录、删除子目录等这些操作需要在目录表中增加、删除或者修改条目。

对于上面所叙述的权限控制，现总结概括如下：

1、目录不可写，不可以保护目录下的所有的文件不可写

在一个只允许读的目录下修改一个已经存在的文件，不需要修改目录表中的任何数据。

inode节点中的数据和文件内容要发生变化，只要文件自身有可写属性，这些操作就能正常完成。

2、文件的只读权限可保护文件不被误写，不能保护文件被误删

一个文件有只读属性，文件所在目录有写权限，就可删除这个文件。删除文件，不需要打开文件，只需修改文件所在目录，只要目录可写就可以了。rm命令会在删除一个只读文件时给出一个善意的提示，但是删除照样可以完成。

3、目录有执行权限指可以检索该目录

cat /a/b/c要求/，/a，/a/b三目录有x权限，c有读权限。

cd../stud4628要求.目录、..目录和stud4628目录有x权限。

3.2	访问合法性验证的顺序

用户操作一个文件时，系统根据登录用户名、组名及文件i节点中存储的文件主和组，判断该使用3级权限的哪一级。

若文件主与登录用户相同，则只使用文件主权限，不再查组和其他用户的权限。

若文件主与登录用户不同，但文件i节点中记录的组号与登录用户的组号相同，则只使用组权限，不使用关于其他用户的权限。

若文件主与登录用户不同，文件i节点中记录的组号与登录用户的组号也不同，则使用文件关于其他用户的权限。

超级用户root不受任何权限的制约。

3.3	修改权限

# ls -l

可以查当前目录下所有文件和子目录的权限

# ls -1d .

列出当前目录自身的权限

如果要修改已有文件和目录的权限，只有root用户和文件拥有者才能执行此类操作。其命令为chmod。

使用chmod时有两种体现形式，分别是字母形式和数字形式：

1、字母形式

# chmod [ugoa][+-=][rwx] file-list

u （user）   文件主的权限

g （group）  同组用户的权限

o （other）  其他用户权限

a （all）    所有上述三级权限

要执行的操作的符号：

+ 给指定用户增加存取权限

- 取消指定用户的存取权限

= 给指定用户设置存取权限，其他权限都取消

定义存储权限的字母：

r     read

w    write

x     execute

# chmod   u+rw    *

# chmod   go-rwx *.[ch]

# chmod   a+x  batch

# chmod   u=rx try2

2、数字形式

chmod mode file-list

使用3个八进制数字，分别描述文件主、同组用户、其他用户的权限。

八进制：    6     4     0

二进制：   110   100  000

权限：     rw-   r--  ---

# chmod 640 *.[ch] makefile *.log

3.4	权限修改示例

1、文件写权限

# who am i

jiang      pts/2       Jun 06 08:34

# who &gt; mydata

# ls -l mydata

-rw-r--r--  1 jiang   usr   58 Jun 06 09:04 mydata

# chmod u-w mydata

# who &gt;&gt; mydata （只读文件不许写）

mydata: The file access permissions do not allow the specified action.

# rm mydata （只读文件可以被删除）

rm: Remove mydata? y

# ls -l mydata

ls: 0653-341 The file mydata does not exist.

2、文件的读权限

# who &gt; mydata

# chmod   u-rw   mydata

# cat   mydata （无法读取不允许读的文件中内容）

cat: 0652-050 Cannot open mydata.

# chmod 644 mydata

3、目录写权限

# chmod  u-w   .   （当前目录不许写）

# who &gt; mydata2 （不能创建新文件）

mydata2: The file access permissions do not allow the specified action.

# who&gt;&gt; mydata （可以修改已有的文件，追加一部分数据）

# rm mydata     （不能删除文件）

rm: 0653-609 Cannot remove mydata.

The file access permissions don’tallow the specified action.

# cp /etc/passwd   mydata （可以覆盖旧文件）

# cp /etc/passwd   mydata2 （不能创建新文件）

cp: mydata2: The file access permissions do not allow the specified action.

# mv mydata MyData  （文件不许改名）

mv: 0653-401 Cannot rename mydata to MyData:

The file access permissions do not allow the specified action.

# mkdir Test  （不可创建子目录）

mkdir: 0653-357 Cannot access directory ..

.: The file access permissions do not allow the specified action.

4、目录读权限

设pwd为:           /usr/jiang

# chmod   u-r .

# ls （不可读的目录无法列出其中文件）

ls: .:The file access permissions do not allow the specified action.

# chmod   000 . （取消当前目录所有权限）

# ls

ls: 0653-345 .: Permission denied.

# chmod 755 . chmod: .:The file access permissions do not allow the specified action.

# chmod 755 /usr/jiang （这种访问不需要当前目录的权限，可恢复当前目录权限）

5、子目录ttt没有读写权限，但是保留了x权限

# chmod  u=x  ttt

# cat ttt/ccp.c

main(int argc, char **argv)

{

...

}

# rm ttt/arg.c （子目录没有写权限，不能删除其中的文件）

rm: 0653-609 Cannot remove ttt/arg.c.

The file access permissions do not allow the specified action.

# ls ttt   （子目录没有读权限，不能列出其中的文件）

ls: ttt: The file access permissions do not allow the specified action.

6、子目录有读写权限，但没有x权限

# chmod u=rw ttt

# ls ttt

BUGS.report  arg.c     ccp.c      chap.h       mydata

arg          auth.c       chap.c       disk.img

# cat ttt/arg.c

cat: 0652-050 Cannot open ttt/arg.c.

7、试图设置其他用户的文件或目录的权限

# chmod 777 /

chmod: /: Operation not permitted.

#

3.5	文件掩码umask

3.5.1	概念

umask指设置文件和目录的初始权限。掩码值的含义是在新创建的文件和目录中，不含有掩码值中列出的权限。

# umask       （打印当前的umask值）

# umask 022   （将umask值设置为八进制的022）

umask是shell内部命令，是进程属性的一部分，每个进程都对应一umask值。umask命令用于修改shell进程自身的umask属性。

常将umask命令放到自动执行批处理文件中，如用csh作登录时，可将umask命令加入到.login文件或.cshrc文件中；用sh作登录shell时，可以将umask命令加入到.profile文件中。

# umask 022   （将umask值设置为八进制的022）

掩码值：    022

二进制：    000010010

掩掉的权限：----w--w-

即文件和目录的初始权限，不含同组用户和其他用户的写权限。

# umask 077   （将umask值设置为八进制的077）

掩码值：    077

二进制：    000111111

掩掉的权限：---rwxrwx

除文件主外，不许其他用户访问

3.5.2	System call在umask的应用

int  umask(int new_mask);

new_mask为新掩码值，函数返回值为原先的掩码值。为了读出进程的掩码值,需要调用umask两次。

新文件的初始权限由umask和open中的权限参数共同确定。

设umask为077

fd = open(filename, O_CREAT | O_WRONLY, 0666);

文件的权限为0666，屏蔽掉077后为0600，即rw-------。

初始创建的文件的权限受open调用的规定值和进程自身属性的umask值影响。

文件的权限只有在初始创建时受上述因素影响，一个已存在的文件的权限，不会受open和umask的影响。

系统调用chmod可修改文件的权限,它不受umask的影响。

int chmod(char *path, mode_t mode);

int fchmod(int fd, mode_t mode);
