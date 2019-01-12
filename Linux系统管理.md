# Linux系统管理



# **第1章  Linux基础入门**



## **1.1 Linux终端**



### **1.1.1 Linux的界面**

Linux的界面有两种：**CLI**和**GUI**

它们有一个共同的特点，就是为用户提供了 一个接口，这个接口用于接收用户输入的命令，并且解释或执行它，然后根据命令调用相应的应用程序。

这个接口，称之为**shell**

在CentOS中，默认支持的shell是**bash**

查看当前shell的类型可以使用：

```sh
$ echo $SHELL
```



### **1.1.2 终端**

任何一个shell接口都需要与一个**终端**进行关联才能实现交互

终端主要用于用户信息的输入以及处理结果的输出

CentOS系统提供了6个虚拟终端，以**`tty[1-6]`**命名，使用组合键盘**CTRL+Alt+[F1~F6]**可以实现虚拟终端之间的切换，当然，如果我们不需要那么多虚拟终端，也可以手动关闭它。

另外，对于远程用户，他们通过**伪终端**来实现对Linux的操作，所谓的伪终端，就是一种类似于终端的设备，但是这种设备不与任何终端硬件相关。比如，我们通过Xshell软件，或telnet连接的终端，都是伪终端。

在Linux中，有一个重要的哲学思想，就是**一切皆文件**。所以这些终端设备在Linux上都是以文件的形式存在，称之为**设备文件**。内容如下表所示：

| 终端类型 | 设备文件名称               |
| -------- | -------------------------- |
| 控制终端 | /dev/tty                   |
| 控制台   | /dev/console               |
| 虚拟终端 | /dev/tty#  (#表示一个数字) |
| 伪终端   | /dev/pts/# (#表示一个数字) |
| 串行终端 | /dev/ttyS# (#表示一个数字) |

> **说明1：**控制终端，相当于当前终端的一个链接

>**说明2：**控制台又称为物理终端，而现在Linux已经将控制台和虚拟终端这两个概念模糊化了，无论你在哪个虚拟终端敲入命令，它执行的结果将会显示在当前的虚拟终端下。也就是说，Linux会把当前你敲入命令的那个虚拟终端当作控制台来看待。

如果想知道当前的进程与哪个控制终端相连，可以使用`ps -ax`来进行查看

现在我们举几个小例子。

**示例1**：分别在第一个伪终端输入`：echo “hello world” >/dev/tty，`在第二个伪终端上输入`echo “hello pts1” >/dev/tty`。然后查看结果：

```sh
[root@MyLabC7 ~]# tty
/dev/pts/0
[root@MyLabC7 ~]# echo "hello world" > /dev/tty
hello world
```

切换到第二个伪终端

```sh
[root@MyLabC7 ~]# tty
/dev/pts/1
[root@MyLabC7 ~]# echo "hello pts1" >/dev/tty
hello pts1
```

通过上述实验结果，可以知道，`/dev/tty`是一个控制终端，它相当于当前终端的一个链接。

**示例2**

```sh
$ echo "hello world" > /dev/console
```

当你输入这条指令后，"hello world"就会显示在你当前所在的虚拟终端上面。



### **1.1.3 登陆Linux**

当我们以命令行的方式登陆到系统后，会出现如下形式：

```sh
[root@huyiun ~]# 
```

这是Linux登陆的一个提示符号，由环境变量PS1所设定的。

```sh
[root@huyiun ~]# echo $PS1
[\u@\h \W]\$
```

| 符号 | 说明                                             |
| :--- | :----------------------------------------------- |
| \u   | the username of the current user                 |
| @    | 分隔符                                           |
| \h   | the hostname up to the first '.'                 |
| \W   | the  basename  of  the current working directory |
| \$   | if the effective UID is 0, a #, otherwise a $    |

其实可以更改PS1变量的值

```sh
PS1="\[\e[37m\][\[\e[32m\]\u\[\e[37m\]@\h \[\e[36m\]\w\[\e[0m\]]\\$ "
```



## **1.2 Linux命令**



### **1.2.1 Linux命令的使用**

在Linux中，一切皆文件，包括在Linux上所使用的命令，它其实也是以文件的形式存在的，这种文件叫**二进制程序文件**

那么当我们在终端上键入一个命令的时候，bash是如何找到ls的呢? 那么这就要通过一个叫做**`PATH`**路径的环境变量来实现了。

也就是说，当我们输入一个命令的时候，bash程序就会通过寻找PATH环境变量去找到命令的可执行文件，我们可以使用命令`echo $PATH`来查看一下PATH中的变量。

```sh
[root@huyiun ~]# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```



### **1.2.2 命令的类型**

在shell中，命令分为**内置命令**和**外部命令**，内置命令是bash程序自带的，而外部命令有着对应的二进制文件。

我们可以通过`type`命令来查看命令的类型

```sh
[root@huyiun ~]# type type
type is a shell builtin
[root@huyiun ~]# type cd
cd is a shell builtin
[root@huyiun ~]# type ls
ls is aliased to `ls --color=auto'
[root@huyiun ~]# type less
less is /usr/bin/less
```

一般来说，内置命令将比外部命令执行的更快，主要原因如下：

​	1.内部命令在系统启动时就调入内存，是常驻内存的，所以执行效率高

​	2.外部命令是系统软件的功能，用户需要时才从硬盘中读取



### **1.2.3 命令的格式**

不管是内置命令还是外部命令，它们的使用格式上大同小异，大概如下：

```
COMMAND [ -Option [arguments] ] [parameters]
```

说明：

​	**COMMAND**：表示命令，一般是小写

​	**Option**：命令的选项，有些带-，有些不带-

​		选项有长选项和短选项之分：

​			长选项：例如--help，不能合并使用

​			短选项：例如-l，-d，多个短选项可以合并，如-ld

​	**arguments**：有些选项可以带参数，故称为选项参数

​	**parameters**：命令作用的对象，这个对象可能会有多个	



## **1.3 命令帮助与使用**



### **1.3.1 内置命令的使用帮助**

格式：`help COMMAND`

```sh
$ help cd
```



### **1.3.2 外部命令的使用帮助**

格式：`COMMAND --help`

```sh
$ ls --help
```

这只是获取帮助的方法之一，我们还可以使用man来获取命令的帮助。 



### **1.3.3 man命令的使用**

格式：`man COMMAND`

man将命令的帮助文件分成了两个部分，常见的部分有：

- NAME：命令的名字
- SYNOPSIS：语法格式
- DESCRIPTION：描述
- OPTION：选项
- EXAMPLES：使用示例
- AUTHOR: 作者
- BUGS: 报告程序bug的方式
- SEE ALSO: 参考

一般我们只要阅读NAME、SYSNOPSIS、DESCRIPTION、OPTION这几个选项

对已SYSNOPSIS来讲，我们还应该要了解简要字符的格式：

```sh
[   ]:     可选部分
{a|b}:   分组，a和b只能选一个
<   >:      必不可省的部分
.....：    同类内容可以出现多个
```

另外，man是分章节的，具体内容如下：

```
1   Executable programs or shell commands
2   System calls (functions provided by the kernel)
3   Library calls (functions within program libraries)
4   Special files (usually found in /dev)
5   File formats and conventions eg /etc/passwd
6   Games
7   Miscellaneous (including macro packages and conventions)
8   System administration commands (usually only for root)
9   Kernel routines [Non standard]
```

我们可以使用`whatis`命令来查看命令属于哪个章节，如：

```sh
[root@huyiun ~]# whatis ls
ls (1)               - list directory contents
[root@huyiun ~]# whatis useradd
useradd (8)          - create a new user or update default new user information
```

要知道的是，whatis是根据`/etc/man_db.conf`文件来搜索我们所需要的数据，我们可以举个例子：

```sh
[root@MyLabC7 ~]# ifconfig
-bash: ifconfig: command not found
```

输入`ifconfig`命令发现没有这个命令，那么现在我们安装一下这个软件包：

```sh
[root@MyLabC7 ~]# yum install net-tools -y
```

安装完成后，我们再使用`whatis`来查看`ifconfig`命令属于哪个章节

```sh
[root@MyLabC7 ~]# whatis ifconfig
ifconfig: nothing appropriate.
```

出现这样的原因是因为`ifconfig`这个命令是我们刚才安装的，所以它的信息未导入到`/etc/man_db.conf`中去，所以需要使用`mandb`这个指令来更新这个文件的内容。

```sh
[root@MyLabC7 ~]# mandb
Purging old database entries in /usr/share/man
...……………………….（出现的信息省略）
[root@MyLabC7 ~]# whatis ifconfig
ifconfig (8)         - configure a network interface
```

注意：在CentOS6中，更新`/etc/man_db.文件使用的是`makewhatis。

接下来再说说，如何阅读man手册

**翻屏**

| 按键   | 描述       |
| :----- | :--------- |
| B      | 向上翻一屏 |
| 空格键 | 向下一翻   |
| K      | 向上翻一页 |
| 回车键 | 向下翻一页 |
| Pgup   | 向上翻半屏 |
| Pgdown | 向下翻半屏 |


**文本搜索**

|              |    n     |    N     |
| ------------ | :------: | :------: |
| **/keyword** | 向下搜索 | 向上搜索 |
| **?keyword** | 向上搜索 | 向下搜索 |



### **1.3.4 date命令的使用**

我们可以直接使用`man date`来查看`date`命令的使用

```sh
NAME
       date - print or set the system date and time

SYNOPSIS
       date [OPTION]... [+FORMAT]
       date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]
```

可以看到，date命令是用来显示或设置系统时间和日期的。

常用格式：`date [OPTION]... [+FORMAT]`

**示例1：显示当前时间和日期**

```sh
[root@huyiun ~]# date
Mon Jan  7 17:40:33 CST 2019
```

**示例2：设置日期和时间**

```sh
[root@MyLabC7 ~]# date 1218000018.00
Tue Dec 18 00:00:00 CST 2018
```

> **注意**：只有超级用户才能修改系统时间

**示例3：使用参数**

```sh
[root@huyiun ~]# date +%F_%T
2019-01-07_17:42:14
```

说明：

```
常见的参数有：
    %H               小时的HH格式(00-23)
    %M               分钟的MM格式(00-59)
    %S               秒钟的SS格式(00-59)
    %m               月的mm格式(01-12)
    %d               日的dd格式(1-31)
    %y               年的yy格式
    %Y               年的YYYY格式
    %F               完整的日期YYYY-MM-DD格式，=%Y-%m-%d
    %T               时间HH:MM:SS格式。=%H:%M:%S
```

**示例4：date还能接上字符串**

```sh
[root@huyiun ~]# date
Sun Jan  6 17:44:31 CST 2019
[root@huyiun ~]# date -s "1 days ago"
Sat Jan  5 17:44:37 CST 2019
```

提示：要知道的是，在linux里面有两个时间：

- **硬件时间**
  硬件时钟是存储在CMOS里的时钟，关机后该时钟依然运行，主板的电池为它供电。那个时钟依照主板石英晶体振荡器频率工作，在启动系统后，系统从该时钟读取时间信息，之后独立运行。当调整系统时钟或与internet同步后，不会改变硬件时钟，下次启动又会变成硬件时钟的时间

- **软件时间**
  开机时读取硬件时间。当调整系统时钟或与internet同步后，不会改变硬件时钟，下次启动又会变成硬件时钟的时间。

那么我们可以使用命令`hwclock`来调整硬件时间，在这里，有两个常用的关于`hwclock`的选项：

常用的选项：

​	-s：把软件时钟设置成和硬件时钟一样。

​	-w：把硬件时钟设置为软件时钟一样。



### **1.3.5 系统关机与重启**

常用的几个关机命令：

```sh
# shutdown -h [NOW|TIME]   //NOW表示现在马上关机，TIME表示设置某一时间点关机
# init 0
# poweroff
```

常用的重启命令：

```sh
# reboot
# shutdown -r
```





# **第2章 Linux文件管理**

## **2.1 FHS结构**

Linux的发行版有很多种类型，那么如果每个版本都有着自己的想法去配置文件应该存放的位置，那么将造成管理上的困扰，为了解决这个问题，就有了FHS标准。

FHS针对`/`，`/usr`,`/var`这三个目录来定义。



### **2.1.1 The Root Filesystem**

`/`目录是所有目录的起点，即所有的目录都挂载在`/`目录下，当系统开机的时候，内核会从磁盘加载`/`，它的结构如下所示：

```sh
[root@huyiun ~]# tree -L 1 /
/
├── bin -> usr/bin            ##二进制程序文件的存放目录
├── boot			          ##系统开机所需要的内核文件以及grub的存放目录
├── dev                       ##设备文件
├── etc                       ##配置文件存放的目录
├── home                      ##普通用户家目录所存在的目录，如/home/USERNAME
├── lib -> usr/lib            ##库文件的存放目录
├── lib64 -> usr/lib64        ##库文件的存放目录
├── media                     ##便携式媒体的挂载目录
├── mnt                       ##临时文件系统的挂载目录
├── opt                       ##第三方软件的安装目录
├── proc                      ##虚拟文件系统，反映出来的是内核、进程信息的实时状态
├── root                      ##管理员用户家目录
├── run                       ##存放着程序运行所需的数据，重新系统后会生成新数据
├── sbin -> usr/sbin          ##管理员能够执行的二进制程序文件的存放目录
├── sys                       ##虚拟文件系统，大多数内核参数在这个目录中修改
├── tmp                       ##临时文件目录
├── usr                       ##用户程序，数据，帮助文件，库文件等存放的目录
└── var                       ##存放这一些变化的文件，比如日志，邮件，数据库...
```



### **2.1.2 The /usr Hierarchy**

`/usr`目录是文件系统中的第二个重要的目录，它里面存放的是静态的，可共享的数据并且能在兼容FHS的主机上使用；它的结构如下所示：

```sh
[root@huyiun ~]# tree -L 1 /usr
/usr
├── bin				##二进制程序路径
├── etc             ##用户本地程序的配置文件目录，但一般都放在/etc目录下
├── games           ##游戏
├── include         ##头文件的存放目录
├── lib             ##库文件的存放路径
├── lib64
├── libexec    
├── local           ##本地安装软件时所存放的目录
├── sbin            ##管理员能够执行的二进制程序文件的存放路径
├── share           ##帮助文件的存放路径，如/usr/share/man
├── src             ##源码所存放的目录   
└── tmp -> ../var/tmp
```



### **2.1.3 The /var Hierarchy**

如果说`/urs`是安装时会占用磁盘较大容量的目录，那么`/var`就是系统在运行后逐渐占用磁盘容量，因为`/var`目录主要针对一些动态文件的存放。

常用的子目录有：

```sh
[root@huyiun ~]# tree -L 1 /var
/var
├── cache                   ##程序的缓存数据
├── lib                     ##存放着程序的元数据文件，如/var/lib/mysql
├── local                   ##为/usr/local存放可变的数据
├── lock -> ../run/lock     ##锁文件的存放目录
├── log                     ##日志文件的存放目录
├── mail -> spool/mail      ##用户邮箱目录
├── run -> ../run           ##存放着程序运行所需的数据，重启系统后会重新生成新数据
    └── spool   
        ├── /var/spool/mail
        ├── /var/spool/cron     
```



## **2.2 目录管理命令**



### **2.2.1 cd命令**

`cd`命令是用来改变当前的工作目录的。

在使用`cd`命令之前，我们理解相对路径和绝对路径的概念。

​	**相对路径**：就是从`/`目录开始写起的路径，如`/etc/fstab`。

​	**绝对路径**：相对于当前工作目录而言。

我们可以使用`ls -la`查看某一个目录

```sh
# ls -la
. ..
```

我们可以发现，在任何目录下都会有一个`.`和`..`的目录

​	`.`：表示当前用户的工作目录

​	`..`：表示当前工作目录的父目录

例如，在/tmp下有个test的目录，我想要进入这个test目录：

​	如果使用绝对路径来说，那么进入test目录应方法就是，`cd /tmp/test`。

​	如果使用相对路径来说，那就是`cd  ./test`（前提是你本身就在/tmp目录下）。

说的简单一点，cd就是用来切换目录的，用法就是使用cd命令后面跟上你需要切换的目录即可。

一般`cd`有一些快捷用法：

​	`cd ~`：用户进入自己的家目录中，相当于直接敲入cd命令

​	`cd ..`：当前工作目录的父目录

​	`cd -`：返回上一次所在的工作目录

另外，在进行cd切换的时候，当前的工作目录和上一次的工作目录会保存在`PWD`、`OLDPWD`这两个环境变量中。



### **2.2.2 pwd命令**

`pwd`这个命令是用来显示当前工作目录的

```sh
[root@MyLabC7 ~]# cd /bin
[root@MyLabC7 bin]# pwd
/bin 
```

在CentOS7中，/bin是一个链接目录，它指向/usr/bin，所以如果你想显示链接文件所指向的目录，可以使用：

```sh
[root@huyiun bin]# pwd -P
/usr/bin
```



### **2.2.3 dirname命令**

我们知道，pwd可以显示文件的绝对路径，如果，我只想显示文件所在的目录怎么办？

可以使用如下操作：

```sh
# dirname /etc/sysconfig/network-scripts
/etc/sysconfig
```

那么，既然有显示文件所在路径，当然也可以只显示绝对路径中的文件名，操作如下：

```sh
# basename /etc/sysconfig/network-scripts
network-scripts
```



### **2.2.4 mkdir命令**

描述：创建目录

用法：`mkdir [OPTION]...DIRECTORY...`

常用选项：

​	-p：递归创建目录

​	-v：显示创建目录的详细信息

示例：

```sh
# mkdir test
# mkdir /tmp/a/b/c -p
```

说明：

​	1.在创建目录的时候，目录名字不能重名，否则会报错

​	2.路径基名为命令的作用对象，基名之前的路径必须得存在

​	3.目录名称中最好不要带空格

思考：怎样一次性创建x,x/a,x/b,a/n,a/m

```sh
[root@MyLabC7 ~]# mkdir -pv x/{a/{m,n},b}
mkdir: created directory ‘x’
mkdir: created directory ‘x/a’
mkdir: created directory ‘x/a/m’
mkdir: created directory ‘x/a/n’
```



### **2.2.5 rmdir命令**

描述：用来删除空目录

常用选项：

​	-p：递归删除空目录

​	-v：显示删除空目录





## **2.3 文件管理命令**



### **2.3.1 文件的类型**

在Linux中，一切皆文件，主要有以下几类文件：

| 文件类型 | 符号 |
| :------- | :--- |
| 常规文件 | -或f |
| 目录文件 | d    |
| 链接文件 | l    |
| 块设备   | b    |
| 字符设备 | c    |
| 套接字   | s    |
| 命名管道 | p    |

**常规文件**又可以分为以下三种：

​	ASCII文件：纯文本文档

​	二进制程序文件：可执行文件

​	数据文件：程序在运作过程中可能读取的文件

**链接文件**：这里指的是软链接，相当于Windows的快捷方式

**字符设备**：在I/O传输过程中以字符为单位进行传输的设备，如键盘，打印机

**块设备**：系统中能够随机访问固定大小的数据块的设备。最常见的就是硬盘

**套接字**：IP地址和端口号组合称为套接字，其用于标识客户端请求的服务器和服务

**管道**：负责将一个进程的信息传递给另一个进程，从而使得该进程的输出成为另一个进程的输入



### **2.3.2 ls命令**

描述：用于显示目录和文件信息

格式：`ls [OPTION]...[FILE]...`

常用的选项：

​	-a：显示全部的文件，包括隐藏文件

​	-d：显示目录本身的信息，而非目录下文档的信息

​	-h：可读写显示，一般与-l一起使用

​	-i：可列出文件的inode

​	-R：递归显示

​	-r：逆序显示

​	-t：以修改时间排序，默认的是以文件名排序

​	-p：给目录加上表示符号"/"

​	-S：根据文件大小进行排序

​	-s：显示每个文件所占的block的大小，默认为kb

​	--color={never|always|auto}：是否使用彩色分辨文件

​	--full-time：输出完整时间

​	--time={atime,ctime}：默认是mtime



示例1：显示当前目录下的子目录与文件的名称

```sh
# ls
```

示例2：查看所有文件的内容，包括隐藏文件

```sh
# ls -a
```

示例3：查看文件和目录的详细信息

```sh
# ls -l
```

列出的内容信息如下图所示：

![image_1cfd1jvpbb9fshe1c1h1075c6v36.png-57.5kB](http://static.zybuluo.com/huiyun/b85aft50hq48mp4idnmp6mlf/image_1cfd1jvpbb9fshe1c1h1075c6v36.png)

另外要注意的是，文件的大小那一列有时显示的并不是文件的大小，如：

```sh
[root@huyiun ~]# ls -l /dev/tty{0..5}
crw--w---- 1 root tty 4, 0 Jan  7  2019 /dev/tty0
crw--w---- 1 root tty 4, 1 Jan  7  2019 /dev/tty1
crw--w---- 1 root tty 4, 2 Jan  7  2019 /dev/tty2
crw--w---- 1 root tty 4, 3 Jan  7  2019 /dev/tty3
crw--w---- 1 root tty 4, 4 Jan  7  2019 /dev/tty4
crw--w---- 1 root tty 4, 5 Jan  7  2019 /dev/tty5
```

可以看到这时指的是设备号，即前面一个数字表示**设备类型**，后面一个数字表示该类型设备的**具体设备号**。比如`4,0` ，4就代表该设备类型为虚拟终端，0代表的是第1个虚拟终端。

示例4：可读性显示

```sh
# ls -lh
```

示例5：显示目录本身的详细信息

```sh
# ls -ldh DIR
```

示例6：逆序查看文件

```sh
# ls -lrt
```



### **2.3.3 file命令**

描述：用来查看文件的类型

用法：`file FILE`

示例：

```sh
# file /bin/ls
/bin/ls: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=aa7ff68f13de25936a098016243ce57c3c982e06, stripped
    
# file /bin/cd
/bin/cd: POSIX shell script, ASCII text executable
    
# file /etc/fstab
/etc/fstab: ASCII text
    
# file /dev/sda
/dev/sda: block special
    
# file /etc
/etc: directory
```



### **2.3.4 cp命令**

描述：复制文件与目录

用法：`cp [OPTION] ...SOURCE DEST`

常用的选项：

​	-a：复制文件的全部属性，相当于--preserve=all -dr

​	-d：复制链接文件本身，而不是文件本身

​	-i：人机交互，默认选项，不想交互可使用/bin/cp  

​	-p：复制文件的mode,ownership,timestamps属性

​	-r：复制目录

​	-u：若目标文件已经存在，且源文件比较新，才会更新

​	--preserve：

​		mode：权限

​		ownership：属主和属组

​		timestamps：时间戳

​		context：安全标签

​		xattr：扩展属性

​		links：符号链接

​		all：上述所有属性

示例：

```sh
# cp /etc/hosts /tmp/
# cp /etc/hosts /tmp/host
# cp -r /var/log/ /tmp/
# cp -a /etc/passwd /var/tmp/
```



### **2.3.5 mv命令**

描述：移动/重命令文件或目录

```sh
## 此时相当于重命名
$ mv test test.txt 

## 此时相当于移动
$ mv text.txt /tmp/
```



### **2.3.6 rm命令**

描述：删除文件或目录

用法：`rm [OPTION]...FILE...`

常用选项：

​	-f：强制删除

​	-i：交互式删除

​	-r：删除目录

**提示**：危险操作:`rm -rf /*`；所有不用的文件建议不要直接删除，而是移动至某个专用的目录（模拟windows回收站）



### **2.3.7 touch命令**

其实，按道理说，`touch`命令不是一个真正意义上的创建文件，而是更改文件的时间戳。所谓的时间戳就是指格林威治时间1970年01月01日00时00分00秒(北京时间1970年01月01日08时00分00秒)起至现在的总毫秒数。

在linux操作系统中，有三种时间戳，分别是：

- **access time(atime)**
  当文件被访问的时候，就会更新这个atime时间。
- **modification(mtime)**
  当文件的内容数据被修改时，就会更新这个mtime时间。
- **status time(ctime)**
  当文件的元数据被修改时(比如文件权限)，就会更新这个ctime时间

使用命令`stat`可以查看文件的时间戳(stat命令可以理解为查看文件的元数据属性)

```sh
# stat file1 
  File: ‘file1’
  Size: 997       	Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d	Inode: 138857      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-01-06 14:47:32.954604290 +0800
Modify: 2019-01-06 14:47:54.456717721 +0800
Change: 2019-01-06 14:47:54.456717721 +0800
 Birth: -
```

使用touch命令，可以更新文件的时间戳，但前提是这个文件得事先存在，不然，touch便会创建这个文件。

> **说明：**
>
> RHEL6开始，relatime,atime延迟修改，必须满足其中一个条件：
>
>  - 1.自上次atime修改后，已达到86400s（一天）
>  - 2.发生写操作时

这三个时间戳重点就是mtime，可以查看文件是什么时候被修改的。另外，我们也要知道，一旦文件被修改了，那么文件的atime和ctime都会发生改变。



## **2.4 文件查阅命令**



### **2.4.1 cat命令**

描述：查看文件内容

用法：`cat [OPTION]...[FILE]...`

常用选项：

​	-b：显示行号，但空白行不标行号

​	-n：显示行号，也包括空白行

​	-E：将文件的换行符显示出来

> **说明**
>
> 在Linux中，以$符为换行符
>
> 在Windows中，以^M$为换行符

例1：查看换行符

```sh
[root@MyLabC7 tmp]# cat -E /etc/issue
\S$
Kernel \r on an \m$
$
```

例2：显示行号
```sh
[root@MyLabC7 tmp]# cat -n /etc/issue
1   \S
2   Kernel \r on an \m
3   
[root@MyLabC7 tmp]# cat -b /etc/issue
1   \S
2   Kernel \r on an \m
[root@MyLabC7 tmp]#
```
注意，cat是一个过滤器，当直接输入cat的时候，你键入什么，它就会输出什么。



### **2.4.2 more命令**

`more`就是分屏查看的意思，它的用法很简单，之间在后面接上要查看的文本内容即可。

要注意的是，more是可以支持上下翻屏的，但是如果你翻屏到了文件底部，那么more就会自动退出这个文本了。

这个一般来说用的不多，所以就不举例子了，因为还有一款分配查看工作，它的工作能力比more更强大。这就是我们接下来要说的less



### **2.4.3 less命令**

less命令的具体用法如下：

**翻屏**

| 按键   | 描述       |
| :----- | :--------- |
| B      | 向上翻一屏 |
| 空格键 | 向下一翻   |
| K      | 向上翻一页 |
| 回车键 | 向下翻一页 |
| Pgup   | 向上翻半屏 |
| Pgdown | 向下翻半屏 |


**文本搜索**
<table>
    <tr>
        <td></td>
        <td><b>n</b></td>
        <td><b>N</b></td>
    </tr>
    <tr>
        <td><b>/keyword</b></td>
        <td>向下搜索</td>
        <td>向上搜索</td>
    </tr>
        <td><b>?keyword</b></td>
        <td>向下搜索</td>
        <td>向上搜索</td>
</table>



### **2.4.4 head命令**

`head`命令后面直接跟上要查阅的文本，表示默认输出文本文件内容的前10行，如：

```sh
# head /etc/passwd
```

如果想输出指定的前#行，可以使用`-n`选项
```sh
# head -3 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```



### **2.4.5 tail命令**

描述：查看文件的尾部内容，默认显示末尾10行。

用法：`tail [OPTION]... [FILE]...`

常用选项：

​	-n：显示文件末尾n行的内容

​	-f ：动态显示文件内容，常用于查看日志

示例：
```sh
# ping 192.168.10.1 >>/tmp/test_tail

打开另一个终端
# tail -f test_tail 
64 bytes from 192.168.10.1: icmp_seq=20 ttl=64 time=0.588 ms
64 bytes from 192.168.10.1: icmp_seq=21 ttl=64 time=0.426 ms
64 bytes from 192.168.10.1: icmp_seq=22 ttl=64 time=0.458 ms
64 bytes from 192.168.10.1: icmp_seq=23 ttl=64 time=0.578 ms
…………………………………
【Ctrl + C】结束
```





# **第3章 Bash特性**



## **3.1 命令历史与Tab键补全**



### **3.1.1 history命令**

bash保存过去曾执行的命令列表，我们可以使用上下箭头来翻看此前执行过的命令；如果命令过多，翻不过来，可以使用history命令

```sh
# history 
   1  history 
   2  cd
   3  ls
   4  cat /etc/fstab 
   5  
```

要知道的是，当前shell进程将命令保存在缓冲区中；缓冲区中的命令会随着shell的退出时保存至文件中，这个文件叫做`.bash_history`。

所以，当我们第一次登陆shell时，是不会存在`.bash_history`文件。只有当注销系统后，即退出shell才会存在一个.bash_history。

```sh
# ls /root/.bash_history 
/root/.bash_history	
```

可以发现，这个.bash_history文件存放在用户的家目录中，也就是说每个用户都会有这样一个文件，用来保存命令的历史。

下面介绍一个history的具体用法：

描述：用来查看或操作命令历史列表

格式：`history [-c] [-d offset] [n]`

常用选项：

​	-c：清空命令历史缓存

​	-d：删除指定命令历史

​	-r：从.bash_history中读取命令历史列表至缓冲区

​	-w：将缓冲区中的命令历史列表写入到.bash_history

现在，我们应该知道，在shell中输入的命令都会保存至缓冲区中，我们可以使用`history -c`来清空命令历史缓存。

```sh
[root@MyLabC7 ~]# history -c
[root@MyLabC7 ~]# history 
   1  history
```

如果你想恢复命令历史列表，那么可以使用`history -r`，这样系统又会从`.bash_history`文件中获取命令历史列表。

```sh
# history -r
```

另外，关于history，还可以使用一些快捷方式：

**!#(#为历史命令列表中的编号)**，如：
```sh
[root@MyLabC7 ~]# history 
    1  cd
    2  cat /etc/fstab 
    3  ls
    4  history 
[root@MyLabC7 ~]# !3
    ls
    1.txt  anaconda-ks.cfg  x
```

**!!：重复执行刚才执行过的命令**

**!$：引用上一个命令的最后一个参数**

```sh
[root@MyLabC7 ~]# ls /usr/share/doc
..............文件内容省略...............
[root@MyLabC7 ~]# cd !$
cd /usr/share/doc
```
**!String：String是一个字符串，执行命令列表中最近一次以String开头的命令。**如：
```sh
[root@MyLabC7 ~]# ls /media
[root@MyLabC7 ~]# ls /mnt
[root@MyLabC7 ~]# !l
ls /mnt
```



### **3.1.2 控制history的变量**

我们可以通过`HISTSIZE`变量来控制缓存区中的命令历史列表中的命令数量，可以通过`HISTFILESIZE`变量来控制`.bash_profile`文件中命令历史的数量。
```sh
[root@MyLabC7 ~]# echo $HISTSIZE
1000
[root@MyLabC7 ~]# echo $HISTFILESIZE
1000
```

另外还有一个变量：**HISTCONTROL**
```sh
# echo $HISTCONTROL
ignoredups
```
它是用来控制命令历史的记录，有以下的值：

​	**ignoredups**：忽略重复的命令；（连续的命令输入才为重复的命令，其它不算）

​	**ignorespace**：不纪录以空白字符开头的命令。这样做的好处就是，当我们输入一个和密码等安全相关的指令时 ，可先敲空格键，再输入命令，这样输入的命令就不会被纪录到缓冲区了。

​	**ignoreboth**：同时具备上面两者。

如果想让history中显示命令的执行时间，可以执行如下操作

```sh
# export HISTTIMEFORMAT="%y-%m-%d %T "
# history

   1  18-12-06 15:06:55 ls
   2  18-12-06 15:06:57 pwd
   3  18-12-06 15:06:59 history
```



## **3.2 命令行展开与bash引用**



### **3.2.1 命令行展开**

bash还支持命令行展开，常见的就是当我们输入`cd ~`,我们就进入了用户的家目录，这是由于在执行`cd ~`后，`~`自动展开为用户的家目录。

另外，除了`~`,我们还要说一个中括号：`{}`，中括号的意思就是：可承载一个以逗号分隔的路径列表，并能够将其展开为多个路径；例如：`/tmp/{a,b}`相当于`/tmp/a /tmp/b`



### **3.2.2 bash中的引用**

在bash中可使用单引号`''`，双引号`""`，反引号` `` `来对变量进行引用。

**单引号和双引号的区别：**

​	单引号：强引用，变量替换不会进行。

​	双引号：弱引用，变量替换能够执行。

对于变量的替换，格式是这样的：${变量名}，其中大括号可以省略。如：
```sh
[root@MyLabC7 ~]# name=huiyun
[root@MyLabC7 ~]# echo $name
huiyun
[root@MyLabC7 ~]# echo "$name"
huiyun
[root@MyLabC7 ~]# echo '$name'
$name
```

**反引号：**

反引号（就是tab键上面那个键）：命令的引用，引用命令的执行结果。

```sh
[root@Centos-moban tmp]# echo `date`
2016年 03月 12日 星期六 06:15:58 CST
```

思考：创建一个以当前时间命令的目录
```sh
[root@Centos-moban tmp]# mkdir `date +%Y-%m-%d-%H-%M-%S`
[root@Centos-moban tmp]# ls
2016-03-12-06-21-38
```

对于引用命令的执行结果，还有一种方法，就是$(命令）。如：
```sh
[root@Centos-moban tmp]# mkdir $(date +%Y-%m-%d-%H-%M-%S)
[root@Centos-moban tmp]# ls
2016-03-12-06-28-04
```

提示：`${value}`表示的是变量的替换，而`$(command)`表示的是命令的引用，请不要搞错了哦。



## **3.3 执行状态结果与命令别名**



### **3.3.1 执行状态结果**

不管我们在bash中输入了什么，bash总会给我们返回一个结果，但是从另一个角度来说，如果我们只关注命令的执行是否成功，而不是它执行后所输出的结果，那么这时候我们可以使用`echo $?`来查看。

也就是说，命令执行完成之后，其状态返回值保存于bash的特殊变量`$?`中。如：
```sh
[root@MyLabC7 ~]# ls
1.txt  anaconda-ks.cfg  x  VMwareTools-10.0.0-2977863.tar.gz
[root@MyLabC7 ~]# echo $?
0
[root@MyLabC7 ~]# lss /etc
-bash: lss: command not found
[root@MyLabC7 ~]# echo $?
127
```



### **3.3.2 命令别名**

所谓的命令别名，就是通过设置一个我们习惯的命令使用方式来输入命令。

我们可以使用alias查看哪些命令定义了命令别名。
```sh
[root@MyLabC7 ~]# alias
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

我们可以看到，定义一个命令别名的格式如下：**alias 别名='命令'**

提示：直接在bash中使用alias定义命令别名只是暂时的，如果我们退出shell终端，那么命令别名就没有了。

取消命令别名：**`unalias 别名`**

除了取消命令别名外，我们还可以这样操作不使用命令别名

```sh
## 1.接全路径的时候不会使用命令别名
$ /bin/cp text.txt /tmp/text.txt

## 2.使用\符号
$ \cp text.txt /tmp/text.txt
```



## **3.4 快捷方式与通配符**



### **3.4.1 快捷方式**

| 快捷方式 | 作用                         |
| :------- | :--------------------------- |
| Ctrl+a   | 跳至命令行首                 |
| Ctrl+e   | 跳至命令行尾                 |
| Ctrl+u   | 删除命令行首至光标所在的位置 |
| Ctrl+k   | 删除光标所在的位置至命令行尾 |
| Ctrl+l   | 清屏                         |
| Ctrl+c   | 终止或取消命令               |
| Ctrl+z   | 将当前命令送至后台           |



### **3.4.2 通配符**

通配符，又叫做globbing，一般用法如下：
`*`：匹配任意长度字符

`？`：匹配任意单个字符

`[  ]`：匹配指定字符内的单个字符。如：
​	
**1.查看以a开头的文件**

```sh
[root@MyLabC7 ~]# ls -l a*
```

**2.查看以a开头的两个字**
```
[root@MyLabC7 ~]# ls -l a?
```

**3.查看两个字符的文件**
```
[root@MyLabC7 ~]# ls -l ??
```

对于[ ]，里面可以数字和大小写字母，表现的形式如下：
```sh
[0-9]或[[:digit:]]		   数字
[a-Z]或[[:alpha:]]		   字母，不区分大小写
[[:upper:]]				    大写字母
[[:lower:]]				    小写字母
[[:alnum:]]				    所有字母和数字
[[:space:]]				    空格
[[:punct:]]				    标点符号
[^指定范围]				     匹配指定范围以外的所有的单字符，如[^0-9]等
```
具体示例：
```sh
[root@Centos-moban tmp]# mkdir 'a@b'  'a b'
[root@Centos-moban tmp]# ls
a b  a@b
[root@Centos-moban tmp]# ls -ld a[[:space:]]b
drwxr-xr-x. 2 root root 4096 3月  12 08:42 a b
[root@Centos-moban tmp]# ls -ld a[[:punct:]]b
drwxr-xr-x. 2 root root 4096 3月  12 08:42 a@b
```



## **3.5 Tab键补全与hash缓存**



### **3.5.1 Tab键补全**

Tab键补全分为两种：**命令补全**和**路径补全**

**命令补全**

当我们在bash中输入字符串时，bash首先会查找内部命令；如果没有匹配到字符串，那么就根据PATH环境变量中设定的目录，自左而右逐个搜索目录下的文件名；

对于给定的打头字符串，如果能唯一标识某命令程序，则敲入tab键直接补全；不能唯一标识某命令程序，则再敲一次tab键，给出相关命令列表。

**路径补全**

在给定的起始路径下，以对应路径下的打头字串来逐一匹配起始路径下的每个文件：如果能惟一标识，则敲入tab键直接补全；否则，再敲一次tab，给出相关文件列表；

如果不能补全，则可能需要安装以下软件包

```sh
# yum install bash-completion -y
```



### **3.5.2 hash缓存**

我们之前讲述过，对于外部命令，当我们敲入命令时，shell会自动到一个叫PATH的环境变量下查看与命令名称相关的可执行文件。但是如果每一次执行命令，都需要从PATH中去查找，这样系统执行效率很低，所以我们可以把已经查找的命令缓存起来。那缓存在哪里呢？

查找到并执行的命令会被缓存在一个**hash查找表**中，可以使用`hash`命令查找此表。

```sh
[root@Centos-moban ~]# hash
hits command
6 /usr/bin/tty
1 /usr/bin/dirname
1 /usr/bin/who
1 /usr/bin/ls
```
说明：

​	hits代表使用命令的次数，

​	command表示使用的命令。

如果你想删除某个命令的缓存，可以使用-d选项，即：**`hash -d  COMMAND`**
​	
如果你想清除整个hash缓存表，那么可以使用-r选项，即：**`hash -r`**

那么问题来了，什么时候会用到`hash -r`或`hash -d`呢？
```sh
[root@huyiun ~]# PATH=/tmp/:$PATH
[root@MyLabC7 ~]# hash -r
[root@huyiun ~]# ls
[root@MyLabC7 ~]# hash
Hits   command
1   /tmp/ls
[root@MyLabC7 ~]# mv /tmp/ls /bin/
[root@MyLabC7 ~]# hash
Hits   command
1   /usr/bin/mv
1   /tmp/ls
[root@MyLabC7 ~]# ls
-bash: /tmp/ls: No such file or directory
```

我们可以看到上面这个例子，首先将/bin/ls移动到/tmp目录中，然后将/tmp加入PATH的环境变量中，之后使用了ls命令。当我们用hash查看的时候可以发现它的命令路径是在/tmp/ls下的。

好的，现在我们将它还原，把ls移到/bin目录中去，再使用ls，发现系统报错。这是因为ls在hash表中，所以系统不会再去PATH中寻找了，而hash表中的内容不存在，所以执行不了。那么解决方法就是使用hash -r清空缓存表，那么这样就可以使用ls命令了。 



## **3.6 重定向**



### **3.6.1 文件描述符**

内核给每个访问的文件分配了**文件描述符(File Descriptor)**，它本质是一个非负整数，在打开或新建文件时产生，起到一个索引的作用，也就是说打开的文件都通过文件描述符来引用。

![image_1cfl58dal1ge1bv4d0g1rq4tc9m.png-78.7kB](http://static.zybuluo.com/huiyun/ygg7wxu79wg58v2ddj586lt2/image_1cfl58dal1ge1bv4d0g1rq4tc9m.png)

一个程序在刚刚启动的时候，会默认打开3个文件描述符：

​	标准的输入文件（键盘）             		------>      0

​	标准的输出文件（显示器）  		------>	1

​	标准的错误输出文件（显示器） 		------>	2

比如，我们现在使用vim打开一个文件
```sh
[root@MyLabC7 ~]# ls -l /proc/11841/fd/*
lrwx------. 1 root root 64 Dec 21 03:46 /proc/11841/fd/0 -> /dev/pts/0
lrwx------. 1 root root 64 Dec 21 03:46 /proc/11841/fd/1 -> /dev/pts/0
lrwx------. 1 root root 64 Dec 21 03:46 /proc/11841/fd/2 -> /dev/pts/0
lrwx------. 1 root root 64 Dec 21 03:46 /proc/11841/fd/3 -> /root/.test.txt.swp
```
可以vim打开了4个文件描述符，分别打开了4个文件。

我们可以使用`#>`符号来改变数据输出的位置。其中#表示一个数字，这个数字正是进程打开文件时的文件描述符。比如：

```sh
# cat 1>test.txt
```

我们看到上面这个例子，1本来表示的是标准输出，但是使用上述操作的时候，它却变成了test.txt：
```sh
[root@MyLabC7 ~]# ls -l /proc/20802/fd
total 0
lrwx------. 1 root root 64 Dec 21 04:21 0 -> /dev/pts/0
l-wx------. 1 root root 64 Dec 21 04:21 1 -> /root/test.txt
lrwx------. 1 root root 64 Dec 21 04:21 2 -> /dev/pts/0
```

我们可以看到，文件描述符1此时不再指向标准输出，而是指向了test.txt。类似于这种将文件描述符指向另一个文件的操作称之为重定向。



### **3.6.2 输出重定向**

所谓的输出重定向，就相当于`1>`，即将文件描述符1指向其它位置；这个1可以省略。

`>`代表的是覆盖重定向，也就是说如果你选择的目标文件中有内容的话，那么这个内容将会被覆盖:
```sh
[root@MyLabC7 ~]# cat test.txt 
dadasd
[root@MyLabC7 ~]# > test.txt 
[root@MyLabC7 ~]# cat test.txt
```

我们可以看到`> test.txt`有清空文件的作用，所以使用 `>`会覆盖原来的文件，这样可能造成一些危险。那么我们可以使用`set -C`命来禁止使用覆盖重定向到已存在的文件。
```sh
[root@MyLabC7 ~]# set -C
[root@MyLabC7 ~]# echo 1234 > /etc/passwd
-bash: /etc/passwd: cannot overwrite existing file
```

如果你明确知道自己在做什么，非要覆盖，可以这样做：`>|`表示在-C的特性下强制使用覆盖重定向。
```sh
# cat /etc/issue >| /tmp/test
```

如果想取消上述设置，可以使用`set +C`命令

这是一个`>`的情况下，那么两个`>>`代表的就是追加重定向，即使有`>>`文件的内容不会被覆盖，而是追加至文件尾部。



### **3.6.3 标准错误输出重定向**

如果使用输出重定向，如果出现错误，会直接在显示器上显示：
```sh
[root@MyLabC7 ~]# lss > /tmp/error
-bash: lss: command not found
```

如果我们想将错误重定向到其它文件中去，可以使用`2>`，如：
```sh
[root@MyLabC7 ~]# lss 2> /tmp/error
```

那么通过上述例子我们也知道了，`2>`也是直接覆盖的，如果要追加至文件末尾，可以使用`2>>`。由于`2>`这个操作是危险的，所以我们刚刚设置的`set –C`就生效了，如果想要强制覆盖，则需要使用`>|`。

如果我们想这样操作：不管是正确的还是错误的，都将它保存到同一个文件中去，那么可以使用`&>`。那么可以知道的是，这样的操作会覆盖文件，那么追加可以使用`&>>`。
```sh
[root@Centos-moban ~]# lss &> /tmp/test1
[root@Centos-moban ~]# echo $?
127
```

好的，在这里，我们使用`echo $?`来观察命令是否在执行成功。那么有时候，我们就想知道命令的执行状态是否成功，而不是想得到命令所执行的结果，那么这时候，将结果输出来就不必要了，所以我们可以把结果重定向到`/dev/null`。这个设备很有意思，它会将你输出的一切都吃掉。
```sh
[root@MyLabC7 ~]# cat /etc/issue &>/dev/null
[root@MyLabC7 ~]# echo $?
0
[root@MyLabC7 ~]# cat /etc/issuess &>/dev/null
[root@MyLabC7 ~]# echo $?
1
```
【其它格式】
```sh
COMMAND > /path/to/somefile 2>&1
COMMAND >> /path/to/somefile 2>&1
```



### **3.6.4 输入重定向**

输入重定向可以理解为将文件的输入来源改为从其它文件输入。使用 `“<”`符号进行操作，它就意味着`0<`，只不过这个0可以省略。
​	
下面我们举几个例子：
案例1：
```sh
[root@MyLabC7 ~]# cat 0< /etc/issue
[root@MyLabC7 ~]# cat < /etc/issue
```

案例2：
```sh
[root@MyLabC7 ~]# dd if=/dev/zero of=/tmp/file1.txt bs=1M count=2
[root@MyLabC7 ~]# dd </dev/zero >/tmp/file2.txt bs=1M count=2
```

案例3：
```sh
[root@MyLabC7 ~]# cat >test.txt << EOF
```

案例4：脚本利用重定向打印消息
```sh
cat <<-EOF
    111
    333
    444
    555
EOF
```

注意：-EOF表示结尾处的EOF可以使用tab键缩进，再次强调，是tab键哦，而不是空格。



## **3.7 管道**

### **3.7.1 管道的概念**

所谓的管道，就是连接程序，实现将前一个命令的输出直接定向后一个程序当作输入数据流
​	
格式：`COMMAND1 | COMMAND2 | COMMAND3 | ...`
​	
例如：打印/etc/passwd文件的第5行
```sh
[root@MyLabC7 ~]# head -n 5 /etc/passwd | tail -n 1
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
```
简单的理解，管道就是一个命令的输出作为另一个命令的输入，那么如果我想将那个命令的输出保存至文件中并且又让它做另一个命令的输入，那么该怎么做？

这里就要用到tee这个命令了。
tee - read from standard input and write to standard output and files。
![image_1cfl6e83612tq3sdoo7uq82ks13.png-72.6kB](http://static.zybuluo.com/huiyun/8975rrkh9fgnmb4ukbncymms/image_1cfl6e83612tq3sdoo7uq82ks13.png)

根据它的意思，我们可以知道，tee命令可以将另一个命令的输入写入标准的输出文件或其他文件中去。如：
```sh
[root@MyLabC7 ~]# echo "hello" | tee /dev/pts/0 |tr 'e' 'a'
hello
hallo
```

接下来，我们通过了解几个命令熟悉管道的用法



### **3.7.2 cut命令**

描述：过滤文件中每一行的某个字段

用法：`cut OPTION... [FILE]...`

常用选项：

​	-d		以指定的字符作为分隔符

​	-f		选定由分隔符分隔出来的某个字段

​		#：指定的单个字段

​		#-#：连续的多个字段

​		#,#：离散的多个字段

​		其中，#表示数字

示例：
```sh
[root@MyLabC7 ~]# cut -d : -f 3 /etc/passwd
0
1
2
    （此处内容省略）
```



### **3.7.3 sort命令**

描述：对文本内容进行排序

用法：`sort [OPTION]... [FILE]...`

常用的选项：

​	-n		基于数值大小而非字符进行排序

​	-t		指定分割符

​	-k		用于指定排序比较的字段

​	-r		逆序排序

​	-u		重复的行只保留一份(连续的行才为重复行)

​	-m          	合并已排序的文件

​	-o      	将结果写入到文件而非标准输出

示例1：将/etc/passwd中的用户按UID大小排序

```sh
# sort -t ":" -k3 -n /etc/passwd
```

示例2：合并负载均衡服务器的的日志文件
```sh
# sort -m -t " " -k 4 -o log_all log1 log2 log3
```



### **3.7.4 wc命令**

描述：显示一个文件中的行数、单词数和字节数

用法：`wc [OPTION]... [FILE]...`

常用选项：

​	-l		只显示行数

​	-w		只显示单词数

​	-c		只显示字节数

​	-L      显示字符串的长度，不包括字符串结束符

案例：

```sh
[www@Centos-moban ~]$ echo "hello world"|wc -l
1
[www@Centos-moban ~]$ echo "hello world"|wc -c
12
[www@Centos-moban ~]$ echo "hello world"|wc -w
2
[www@Centos-moban ~]$ echo "hello world" | wc -L
11
```



### **3.7.5 uniq命令**

描述：用来报告或移除重复的行

用法：`uniq [OPTION]...` 

常用选项：

​	-c		显示每行的重复次数

示例1：统计当前/etc/passwd中用户使用的shell类型

```sh
# cut -d ":" -f7 /etc/passwd | sort | uniq –c
```



# **第4章 VIM编辑器**

##  **4.1 VIM的基本使用**



### **4.1.1 vim的工作模式**

基本上，我们将vim的工作分为3种：

**命令模式（command mode）**
vim打开一个文件就直接进入命令模式。在这个模式中，可以使用`[↑↓←→]`按键来移动光标，删除字符，也可以复制粘贴文件数据。
​    
**插入模式（insert mode）**
在命令模式下输入：`[i I a A o O]`等任何一个字母就会进入插入模式。这时候就可以进行文件编辑工作了。

```sh
i:在当前光标所在处前输入。
I:在当前光标所在处行首输入。
a:在光标所在处后输入
A:在光标所在处行尾输入
o:在光标所在处的下一行增加一个空白行
O:在光标所在处的上一行增加一个空白行
```
**末行模式（command-line mode）**
在命令模式当中，输入`[: / ?]`任何一种，就可以进行命令行模式。
  	
具体的内容我们将会在下面的内容一一演示，现在我们大致的了解一下这三种模式之间的关系：
`Insert mode` --> `command mode` ：输入【ESC】键
`Command mode` --> `command-line mode` ：输入冒号":"或"/"或"?"
`Command-line mode` --> `command mode`：输入【ESC】键
​	
下面我们通过一张图可以更直观的了解：
![image_1cfq3pf5t1b9n1i2m1i4q1itm1iehm.png-135.1kB](http://static.zybuluo.com/huiyun/4jyitre27df1xstdcxivftm6/image_1cfq3pf5t1b9n1i2m1i4q1itm1iehm.png)



### **4.1.2 打开文件**

格式：`vim [options] [file ..]`
​	
在vim后面直接跟上你需要的文件就行，如果该文件不存在，那么vim直接创建该文件再进行编辑。
​	
打开文件后，可以使用如下选项让光标处于文件的某个位置：

```
+#			打开文件后，直接让光标处于第#行的行首
+			让光标处于最后一行
```



### **4.1.3 关闭文件**

在command mode下可以使用`ZZ`命令来保存退出
在command-line mode下可以使用如下命令

```sh
q		退出
q!		强制退出，不保存此前的操作
w		保存
wq		保存并退出
x		保存并退出
w	/PATH/TO/FILE		保存至某文件
```



## **4.2 移动光标**



### **4.2.1 字符跳转**

在command mode模式下可以使用`<h，j，k，l>`来进行光标的移动
![image_1cfq47oqt1ca9uu18tk1cb416m91g.png-14.1kB](http://static.zybuluo.com/huiyun/gk0yemv80akgfm4hopn22gjo/image_1cfq47oqt1ca9uu18tk1cb416m91g.png)

但在insert模式下，上述方法就不管用了，如果实在不想用这几个字母，那么还是用`[↑、↓、←、→]`来移动光标吧。
​	
另外，还可以在前面加一个数字来进行跳转，如`5h`表示向左移动5个字符，`10j`表示向下移动10行。 



### **4.2.2 单词跳转**

```sh
w		表示下一个单词的词首
e		当前或后一个单词的词尾
b		当前或前一个单词的词首
```

同样的，可以在它们前面加一个数字，如`5e`表示向后移动5个词尾。`6b`表示向前移动6个词首。



### **4.2.3 行首行尾跳转**

    ^		跳转至行首的第一个非空白字符
    0       跳转至行首
    $		跳转至行尾


### **4.2.4 行间跳转**

    1G		跳转至第一行
    gg		跳转至第一行
    G		跳转至最后一行

可以在G前面加一个数字，如`6G`表示跳转至第#行	



## **4.3 VIM的命令模式**



### **4.3.1 删除命令**

```
x		删除光标所在处的字符
#x		删除光标所在处起始的#个字符
xp		交换光标所在处的字符与其后面的字符的位置
r		替换光标所在处的字符
d^		删除光标所在处前的字符至行首部
d$		删除光标所在处起始字符到行尾部
de		删除光标所在处的单词至当前单词词尾或下一个单词词尾；
db		删除光标所在处前至当前单词的首部或前一个单词的首部。
dd		删除光标所在的行
#dd		从光标所在的起始行向下删除#行
c       删除光标所在处的字符并进入insert mode
```


### **4.3.2 复制命令**

```
y$		复制当前光标所在处字符起始位置至行尾
y^		复制当前光标所在处前至行首
y0		复制当前光标所在处前至行首
ye		复制当前光标所在处单词至当前单词或下一单词词尾
yw		复制当前光标所在处单词至下一个单词词首
yb		复制光标所在处前至当前单词词首或上一个单词词首
yy		复制一整行
#yy		复制#行
```



### **4.3.3 粘贴命令**

    p：缓冲区中的内容如果为整行，则粘贴在当前光标所在行的下方；否则，则粘贴至当前光标所在处的后方；
    P：缓冲区中的内容如果为整行，则粘贴在当前光标所在行的上方；否则，则粘贴至当前光标所在处的前方；


### **4.3.4 可视化模式**
Vim命令也可以使用鼠标来进行选定操作，这种操作我们叫做可视化模式

    v：按字符选定；
    V：按行选定；
    ctrl+v：按列选定

选定之后可以结合`d`,`y`,`c`命令操作。

多行注释小技巧：

​	1.使用ctrl+v进入列编辑模式

​	2.移动光标选择你需要注释的列

​	3.然后按大写的I

​	4.再插入注释符#号

​	5.再按ESC退出即可



### **4.3.5 撤销操作**

    u		撤销此前的操作
    #u		撤销此前的#个操作
    Ctrl+r	撤销此前的撤销
    .		重复执行前一个编辑操作


## **4.4 VIM的末行模式**

### **4.4.1 地址定界**

    .		    	当前行
    $	    	 	最后一行
    %		    	表示全文
    /pat1/      	从光标所在处起始向文件尾部第一次被模式所匹配到的行
    /pat1/,/pat2/   从光标所在处起始，第一次由pat1匹配到的行，至第一次又pat2匹配到的行之间的所有行

下面我们举几个例子：
```
:3,5d          --->删除第3行至第5行
:3,+5y		   --->从第三行开始向下删5行
:.,$d          ---->删除当前行至最后一行
:5,10w /tmp/test
:5r /etc/fstab
```



### **4.4.2 查找**

    /PATTERN：从当前光标所在处向文件尾部查找能够被当前模式匹配到的所有字符串；
    ?PATTERN：从当前光标所在处向文件首部查找能够被当前模式匹配到的所有字符串；
    n：下一个，与命令方向相同；
    N：上一个，与命令方向相反；

 








### **4.4.3 查找替换**

格式：s@`要查找的内容`@`替换的内容`@`修改符`

注意以下几点：
- 要查找的内容，可以是正则表达式
- 替换的内容不能使用正则表达式，但可以引用，如：

```sh
vim /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforcing
# SELINUXTYPE= can take one of three two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

现在我们想将`SELINUX=enforcing`替换为`SELINUX=disabled`，那么可以这样做：
```sh
:%s@\(SELINUX=\)enforcing@\1disabled@g  
```
注意：后面这个\1就表示引用括号里面的内容。
​	
如果想直接引用查找模式匹配到的全部文本，要使用&符号



例1：在某个文件的前三行的行首加上#号

```sh
:1,3s@^.\+@#&@g
```
修饰符：

​	i		忽略大小写
​	g		表示全局替换	



例2：删除以空白字符开头的行的空白字符

```sh
:%s@^[[:space:]]\+@@g
```



### **4.4.4 执行系统命令**

另外，还可以在末行模式中使用**`!COMMAND`**的格式来暂时执行外部的其它命令。

```sh
:!ls
```





**4.5 定义多文件操作功能**
---

**多文件操作**：`vim file1 file2 file3`

文件间切换：
```
next:下一个文件
prev：上一个文件
        
first：第一个文件
last：最后一个文件
```
退出并保存某个文件：wq
退出并保存所有文件：wqall
​    

**多窗口操作：**
```
-o：水平分割
    窗口切换：ctrl+w,上下箭头
-O：垂直分割
    窗口切换：ctrl+w,左右箭头
```

单个文件也可以进行分割:

- 水平分割：Ctrl+w,s
- 垂直分割：Ctrl+w,v



**4.6 定制Vim的工作特性**
---

- 临时配置：在末行模式下使用，仅对当前vim进程有效
- 永久配置：
    - 全局配置：/etc/vimrc
    - 用户配置：/root/.vimrc



### 4.6.1 显示行号

    显示		set nu
    取消		set nonu



### **4.6.2 括号匹配高亮**

	匹配		set sm
	取消		set nosm



### **4.6.3 自动缩进**

	启用		set ai
	禁用		set noai



### **4.6.4 高亮搜索**

	启用		set	hlsearch
	禁用		set	nohlsearch



### **4.6.5 语法高亮**

	启用		syntax  on
	禁用		syntax	off



### **4.6.6 忽略大小写**

	启用		set ic
	禁用		set noic



### **4.6.7 设置tab宽度**

	set ts = # (#表示一个数字)
	默认tab宽度是8个字节



### **4.6.8 括号自动补全**

    小括号：inoremap ( ()<ESC>i
    方括号：inoremap [ []<ESC>i
    大括号：inoremap { {}<ESC>i
    尖括号：inoremap < <><ESC>i



# **第5章 用户管理**

## **5.1 用户和组的基本概念**

### **5.1.1 用户的概念**

首先我们要知道以下几个概念：

​	1.系统上的每个进程(运行的程序)都以一个特定的用户来运行

​	2.每个文件由一个特定的用户所拥有

​	3.访问文件和目录受到用户的限制

​	4.一个进程到底以何种权限访问文件由其相关联的用户来决定

所以，我们可以简单的把用户理解为获取资源的标志符，用来实现资源分配。

每个用户都关联一个特殊的号码用来区分其它用户，这个号码称之为**UID**。也就是说，内核不是通过用户名来识别用户的，而是通过UID来识别用户。

用户又可以分成三种类型：**管理员**、**系统用户**和**普通用户**。管理员使用UID号0来标识。
​    
在CentOS7中，系统用户是由`1-999`来进行标识，普通用户是由`1000+`来进行标识。
​    
而CentOS6中系统用户是由`1-499`来进行标识，普通用户是由`500+`来进行标识。

刚才说过，内核是通过**UID**来识别用户的，它会从`/etc/passwd`文件中去解析用户名与UID的对应关系。简单的说就是系统会通过**/etc/passwd**文件将用户名解析成内核所认识的UID。



### **5.1.2 用户组的概念**

组就是用户的容器，它是为了实现多个用户能够访问同一资源而存在的。
​	
我们可以想象一下这种情景：假如我们要进行团队开发，我们当然希望只有我们团队的内部人员才能共享团队里的资料，而且这个资料非团队人员无权访问。于是我们可以建立一个群，团体中的每个人都在这个群里，他们可以在群里共享资料，那么非团队人员就无法进入这个群，更别说访问资料了。

和用户类似，内核通过**GID**来识别用户组，用户组也可以分成三种类型：**管理员组**、**系统用户组**和**普通用户组**。
​    
管理员组使用GID号0来标识。
​    
在CentOS7中，系统用户组是由`<1-999>`来进行标识，普通用户组是由`<1000+>`来进行标识。

而CentOS6中系统用户组是由`<1-499>`来进行标识，普通用户组是由`<500+>`来进行标识。
​	
同样的，用户组也存在一个名词解析库用来将用户组名称解析成GID。这个名词解析库就是**/etc/group**



### **5.1.3 基本组和附加组**

一个用户可以加入多个组，但是这个用户只能以一个组的身份去进行活动。
​    
举个例子，小明在社会上可以有多种身份：医生，老师，工程师等。但是在某种环境下，他只能用某一种身份，如在学校使用教师身份，在医院使用医生的身份。

那么我们的Linux用户也是一样，你可以加入多个组，但是在任何情况下，你都只能使用一个确定的组进行活动。这就是我们所说的**基本组**和**附加组**。

**附加组**就是我们所加入的多个组，也就是说附加组可以有多个，但是我们要以一个确定的组来进行活动，所以这个就是我们说的**基本组**。也就是说基本组只能有一个。
​    
> **提示：私有组和共有组的划分**
> 当我们在创建用户时如果没有指定用户组，那么系统就会默认创建一个与用户同名的用户组，这个组就叫做用户的私有组，因为它只有一个用户。相反的如果一个组包含了多个用户，那么这个组就叫做公共组。



### **5.1.4 用户密码**

当用户在进行认证的时候，它需要通过比对事先存储的，与登录时提供的信息是否一致；如果一致，那么就登录了系统，如果不一致，那么登录失败。这就是我们所说的密码。那么问题来了，系统从哪里去比对密码呢？那么这里也有一个数据库文件：`/etc/shadow`，这个文件就是用来保存用户所对应的密码信息的。
​    

而且，在CentOS中，密码采用的是**单向加密算法**，单向加密的特征是：

- 单向加密，不能解密
- 雪崩效应，即只要有一个字符不相同，其单向输出的结果都不相同
- 定长输出，无论使用多长的密码，单向加密后输出的字符串都是一样的

另外，为了区分同一系统中的不同用户使用同一个密码，还在密码的字符串中加入了一个随机的salt，这样即使用不同用户使用同一密码，其输出的加密的信息也不会相同。

那有人会想，那么系统是怎么验证密码的呢？很简单，系统会在用户输入密码后会将之前随机生成的salt加入到该密码中，然后再次使用单向加密输出，如果输出的加密信息和数据库中的加密信息一样，则为密码正确。



## **5.2 用户的配置文件**



### **5.2.1 /etc/passwd文件**

所有的用户身份信息(除密码外)都被保存在**/etc/passwd**配置文件中，该文件的内容如下：
```sh
[root@MyLabC7 ~]# head -3 /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
```
这个文件分为七个字段，分别描述了：

![image_1cgc3fa69121pkrtcpgdco19b49.png-53.7kB](http://static.zybuluo.com/huiyun/snl17sbwlq0w6mblf0i0i34g/image_1cgc3fa69121pkrtcpgdco19b49.png)



### **5.2.2 /etc/shadow文件**

该文件记录着用户的密码信息，这个文件只有root用户能查看
```
[root@MyLabC7 ~]# head -3 /etc/shadow
root:$6$ZfBazq7U1iKqo6hK$Zn7qpF8.KWm4KFy358CIuV3WhKO2Z.PPwCFzo9gXBQGWgWllZWK/3bsKpQqVN5sBiwTGg1Sb1BS5aA9nGJuBt1::0:99999:7:::
bin:*:16659:0:99999:7:::
daemon:*:16659:0:99999:7:::
```
该文件分成了9个字段，内容如下：（保留字段没有标出）

![image_1cgc3nel85prc3t2gq104338hm.png-79.3kB](http://static.zybuluo.com/huiyun/btve9pzuzfl80ws6hqlrr4ki/image_1cgc3nel85prc3t2gq104338hm.png)

现在我们解释这几个字段：

**用户名**      
在系统上存在的用户

**密码**        
这是使用单向加密后显示的加密密码，它由$符分成了三个部分，第一个部分表示这个单向加密的算法，用数字表示：`1-md5`、`2-sha1`、`3-sha224`、`4-sha256`、`5-sha384`、`6-sha512`；第二个部分就是我们随机数(salt)；第三部分就是密码字段。
​                    
**上一次修改密码的时间**
这个字段表示从1970年1月1日到上一次更改密码的天数。即可以理解为：上一次更改密码的时间。如果这个字段为0，那么就意味着用户下一次登录时需要先修改密码才能进入系统。
​    
**密码最短使用时间**
这个字段表示用户至少需要使用多少天才能修改密码，如果这个字段为0，表示用户随时都可以修改自己的密码。

**密码最长使用时间**
这个字段表示用户使用密码的最长期限，超过这个期限，那么用户就应该要准备修改密码了。这个字段如果是99999，那么它就代表这个密码可以一直使用下去，但不建议这么做。
​	
**密码警告期限**
这个字段表示在密码将要过期的前多少天给用户提示，像大概类似这样：您好，您的密码将在X天之后即将失效。如果这个字段为0，那么就表示没有开启这项功能。
​	
**密码的非活动期限**
这个字段表示的是如果超过密码的最长使用期限，用户的账号将不能登录，要修改密码才能登录。如果超过了这个非活动的期限，用户将被锁定。需联系管理员去修改该账户密码。
​	
**账户过期时间**
账户使用的有效时间。



### **5.2.3 /etc/group文件**

```sh
[root@MyLabC7 ~]# tail -1 /etc/group
why:x:1000:
```

该文件的字段内容如下：

![image_1cgc45hnkjurfer130ib601o8813.png-35.3kB](http://static.zybuluo.com/huiyun/yh9abw5nowowfx37rkug1qrd/image_1cgc45hnkjurfer130ib601o8813.png)



## **5.3 组管理命令**



### **5.3.1 groupadd命令**

描述：用来创建用户组

用法：`groupadd [OPTIONS] group`

常用选项：

​	-g：指定GID

​	-r：创建一个系统组

**示例1：**

```sh
[root@MyLabC7 ~]# groupadd grp1
[root@MyLabC7 ~]# tail -n 1 /etc/group
grp1:x:1003:
```

**示例2：**

```sh
[root@MyLabC7 ~]# groupadd grp2 -g 2000
[root@MyLabC7 ~]# tail -n 1 /etc/group
grp2:x:2000:
```

**示例3：**
```sh
[root@MyLabC7 ~]# groupadd -r grp3
[root@MyLabC7 ~]# tail -n 1 /etc/group
grp3:x:985:
```



### **5.3.2 groupmod命令**

描述：用来改变组的属性

用法：`groupmod [OPTIONS] GROUP`

常用选项：

​	-g：改变组的gid

​	-n：更改组名



### **5.3.3 gpasswd命令**

描述：管理用户组

用法：`gpasswd [OPTIONS] group`

常用选项：

​	-A：设在用户为组管理员（仅root能使用）

​	-M：将用户添加到此组（仅root能使用））

​	-a：将用户添加至此组（每次只能添加一个用户）

​	-d：将用户从此组删除

**下面举个示例：**
1.创建2个组

```sh
[root@MyLabC7 ~]# groupadd grp1
[root@MyLabC7 ~]# groupadd grp2
```

2.创建5个用户
```sh
[root@MyLabC7 ~]# useradd user1
[root@MyLabC7 ~]# useradd user2
[root@MyLabC7 ~]# useradd user3
[root@MyLabC7 ~]# useradd user4
[root@MyLabC7 ~]# useradd user5
```

3.将user1和user2添加至group1
```sh
[root@MyLabC7 ~]# gpasswd -M user1,user2 grp1
```

4.让user1变成组管理员
```sh
[root@MyLabC7 ~]# gpasswd -A user1 grp1
```

5.切换至user1的用户

```sh
[user1@MyLabC7 ~]$ gpasswd -M user3,user4 grp1
gpasswd: Permission denied.
[user1@MyLabC7 ~]$ gpasswd -a user3 grp1
Adding user user3 to group grp1
[user1@MyLabC7 ~]$ gpasswd -a user4 grp1
Adding user user4 to group grp1
[user1@MyLabC7 ~]$ gpasswd -d user4 grp1
Removing user user4 from group grp1
```



### **5.3.4 groupdel命令**

描述：用来删除用户组
格式：`groupdel [options] GROUP`



### **5.3.5 newgrp命令**

描述：临时切换其它附加组为某个用户的属组

示例：
1.创建test用户以及其附加组：
```sh
[root@huiyun ~]# useradd test
[root@huiyun ~]# groupadd mygrp
[root@huiyun ~]# usermod -aG mygrp test
[root@huiyun ~]# id test
uid=1002(test) gid=1003(test) groups=1003(test),1004(mygrp)
```
2.登陆test
```sh
[root@huiyun ~]# su - test
[test@huiyun ~]$ touch file
[test@huiyun ~]$ ls -l
total 0
-rw-rw-r-- 1 test test 0 Jun 19 23:41 file
```
3.切换到newgrp组
```sh
[test@huiyun ~]$ newgrp mygrp
[test@huiyun ~]$ touch file2
[test@huiyun ~]$ ls -l 
total 0
-rw-rw-r-- 1 test test  0 Jun 19 23:41 file
-rw-r--r-- 1 test mygrp 0 Jun 19 23:42 file2

[test@huiyun ~]$ exit    //退出mygrp组
exit
```



## **5.4 用户管理命令**

### **5.4.1 useradd命令**

描述：用来创建一个用户

用法：`useradd [OPTIONS] 用户名`

常用选项：

​	-u：创建用户时指定uid

​	-g：创建用户时指定gid

​	-c：创建用户时指定用户的信息

​	-d：创建用户时指定用户的家目录

​	-M：创建用户时不创建家目录

​	-m：创建用户时强制创建家目录

​	-s：创建用户时指定默认shell

​	-r：创建系统用户

​	-D：为useradd命令创建用户时指定新的默认值



示例1：
```sh
[root@MyLab7 ~]# useradd openstack
[root@MyLab7 ~]# cat /etc/passwd | tail -1
openstack:x:1000:1000::/home/openstack:/bin/bash
```

示例2：
```sh
[root@MyLab7 ~]# useradd -u 2000 docker 
[root@MyLab7 ~]# cat /etc/passwd | tail -1
docker:x:2000:2000::/home/docker:/bin/bash
```

示例3：
```sh
[root@MyLab7 ~]# useradd -g 3000 username1 
useradd: group '3000' does not exist
```

示例4：使用-D选项可以指定useradd的新默认值，它调用的是`/etc/default/useradd`文件中的是内容。
```sh
[root@MyLab7 ~]# cat /etc/default/useradd 
# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```
说明：    
**GROUP=100**
私有群组机制：用户创建时，用户组随着用户的创建而创建。即一般用户名和组名是相同的。那么它就不会去参考这个group=100的值。如CentOS
​        
共有群组机制：用户创建时，用户的组就是GROUP=100这个组，即每个用户创建时都属于这个群组。因此可以共享数据。如SUSE。

在CentOS上这个值不会生效
​        
**HOME=/home**
用户在创建时家目录基名，如创建一个用户fedora,那么对应家目录/home/fedora
​        
**INACTIVE=-1**
密码过期后是否会失效的设定值
​	 0		立即失效

​	-1		永远不会失效

​	30		30天后失效

**EXPIRE=**
账号的失效日期
​        
**SHELL=/bin/bash**
默认的用户shell是bash
​        
**SKEL=/etc/skel**
用户家目录所要复制的文件的目录

**CREATE_MAIL_SPOOL=yes|no**
是否建立邮箱

例如：我们使用-D –s 来改变默认登录的shell，改变后的结果保存在/etc/default/useradd。
```sh
[root@MyLabC7 ~]# useradd -D -s /bin/csh
[root@MyLabC7 ~]# cat /etc/default/useradd
# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/csh
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
     
[root@MyLabC7 ~]# useradd user14
[root@MyLabC7 ~]# su - user14
[user14@MyLabC7 ~]$ echo $SHELL
/bin/csh
```

另外，要注意的是，创建用户时的诸多默认设定配置文件为`/etc/login.defs`。

我们查看一下这个文件，然后描述这个文件的大致内容：

```sh
[root@MyLab7 ~]# more  /etc/login.defs
MAIL_DIR        /var/spool/mail         //用户默认邮箱放置的目录
PASS_MAX_DAYS   99999                   //密码最长使用天数
PASS_MIN_DAYS   0                       //密码最短使用天数
PASS_MIN_LEN    5                       //密码最短字符长度，已被PAM模块取代，失效了
PASS_WARN_AGE   7                       //密码过期之前的警告天数
 
# Min/max values for automatic uid selection in useradd
UID_MIN                  1000               
UID_MAX                 60000
# System accounts
SYS_UID_MIN               201
SYS_UID_MAX               999
     
# Min/max values for automatic gid selection in groupadd
#
GID_MIN                  1000
GID_MAX                 60000
# System accounts
SYS_GID_MIN               201
SYS_GID_MAX               999
 
#
CREATE_HOME     yes 
UMASK           077         //创建家目录时的权限为700
USERGROUPS_ENAB yes         //使用userdel时，是否会删除初始组
ENCRYPT_METHOD SHA512       //密码的认证机制
```

还有，我们在创建用户的时候，如果此时创建了家目录，那么系统会从`/etc/skel`中复制文件.

我们可以查看一下该文件的内容：
```sh
[root@MyLab7 ~]# ls -a /etc/skel/
.  ..  .bash_logout  .bash_profile  .bashrc  .zshrc
```

然后我们再查看一下我们新创建用户的家目录下文件的内容 
```sh
[root@MyLab7 ~]# useradd openstack
[root@MyLab7 ~]# su - openstack
[openstack@MyLab7 ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .zshrc
```

>**提示：**
>管理员可以在这个目录下添加一些信息，这样新创建的普通用户就会收到这样的通知。



### **5.4.2 usermod命令 **

描述：用来更改帐号属性

格式：`usermod [OPTIONS] 用户名`

常用的选项：

​	-l：修改用户名

​	-u：修改uid

​	-g：修改用户的基本组

​	-G：修改用户的附加组

​	-a：与-G一同使用，用于为用户追加新的附加组

​	-d：修改用户的家目录

​	-m：用于将原来的家目录移动为新的家目录

​	-c：修改用户注释

​	-s：修改用户的shell

​	-e：设置帐号的失效时间

​	-L：锁定用户

​	-U：解锁用户

这个命令和useradd的用法差不多，但是还要稍微提一下：

1.使用-G命令去添加附加组的时候，原来的附加组将会被覆盖。

```sh
[root@MyLabC7 ~]# usermod -G grp1 user1
[root@MyLabC7 ~]# id user1
uid=1003(user1) gid=2001(user1) groups=2001(user1),1003(grp1)
[root@MyLabC7 ~]# usermod -G grp2 user1
[root@MyLabC7 ~]# id user1
uid=1003(user1) gid=2001(user1) groups=2001(user1),2000(grp2)
```

2.如果不想原来的附加组被覆盖，可以使用-aG选项。

```sh
[root@MyLabC7 ~]# id user1
uid=1003(user1) gid=2001(user1) groups=2001(user1),2000(grp2)
[root@MyLabC7 ~]# usermod -aG grp1 user1
[root@MyLabC7 ~]# id user1
uid=1003(user1) gid=2001(user1) groups=2001(user1),1003(grp1),2000(grp2)
```

3.使用-d修改用户家目录时，用户家目录里的文件将不会被复制。如果想改变家目录的同时将家目录中的文件复制过去，那么就使用-md选项。

>注意：使用这个的时候请保证你的selinux是关闭的，不然会出错的哦

4.如果使用了-L选项，那么就意味着锁定了该账户的密码。同样的-U就表示解锁该账户的密码。

```sh
[root@MyLabC7 ~]# usermod -L user1
[root@MyLabC7 ~]# su - user1
Last login: Sun Oct 16 18:11:18 CST 2016 on pts/4
-bash-4.2$ su - user1
Password: 
su: Authentication failure
```



### **5.4.3 userdel命令**

描述：删除用户

用法：`userdel [OPTIONS] 用户名`

常用选项：

​	-r：删除用户时将家目录一并删除



### **5.4.4 password命令**

描述：用来设置和管理用户密码

用法：`passwd [OPTIONS] [用户名]`

常用的选项：

​	-l：锁定用户

​	-u：解锁用户

​	-d：清除密码串

​	-n：指定密码最短使用时间

​	-x：指定密码最长使用时间

​	-w：指定密码警告期限

​	-i：指定密码非活动期限

​	-e：指定账户过期时间

​	-S：查看用户/etc/shadow文件信息

​	--stdin：通过管道设置密码

1.现在创建了一个叫做openstack的用户。然后给它添加密码。
```sh
[root@MyLabC7 ~]# useradd openstack
[root@MyLabC7 ~]# passwd openstack
Changing password for user openstack.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.
```

当然，在这里我们还是重新提一下，密码的使用策略：

​	（1）使用随机密码；
​	（2）最短长度不要低于8位；
​	（3）应该使用大写字母、小写字母、数字和标点符号四类字符中至少三类；
​	（4）定期更换；

现在，我们查看一下`/etc/shadow`文件：
```sh
[root@MyLabC7 ~]# tail -n 1 /etc/shadow
openstack:$6$ODro8Igc$0ADF.WE2FU7CWThbd95HffJp8t3ezP78p9spKayIPDjwsBCq
naKmTek1rOhRdraRrC3529oN.GMMspbphOQwF1:17091:0:99999:7:::
```

我们可以发现，在我们没有指定任何选项之前，密码文件就有一些默认选项了，那么这些默认选项就是来自`/etc/login.defs`中的内容。

2.我们使用-n选项可以指定密码最短使用时间
```sh
[root@MyLabC7 ~]# passwd -n 5 openstack
Adjusting aging data for user openstack.
passwd: Success
```

那么，我们现在登录openstack用户，试着去修改密码：
```sh
[openstack@MyLabC7 ~]$ passwd 
Changing password for user openstack.
Changing password for openstack.
(current) UNIX password: 
You must wait longer to change your password
passwd: Authentication token manipulation error
```

3.可以使用-x选项指定密码最长使用时间
```sh
[root@MyLabC7 ~]# passwd -x 10 openstack
Adjusting aging data for user openstack.
passwd: Success
```

4.使用-w，那么就是设置密码过期前的警告期限。
```sh
[root@MyLabC7 ~]# passwd -w 2 openstack
Adjusting aging data for user openstack.
passwd: Success
```

当到达密码的警告期限的时候，如果你登录了这个账户，系统会要求你改密码。
```sh
[openstack@MyLabC7 ~]$ su - openstack
Password: 
You are required to change your password immediately (password aged)
Changing password for openstack.
(current) UNIX password: 
New password:
```

5.如果超过了最长使用期限，系统就会要求你重新更改密码才能登录。这段时间就是非活动时间,使用-i选项可以指定。如果超过了这个非活动期间，用户将会被锁定。
```sh
[openstack@MyLabC7 ~]$ su - openstack
Password: 
You are required to change your password immediately (password aged)
Changing password for openstack.
(current) UNIX password: 
New password:
```

6.使用`passwd –S`来查看**/etc/shadow**文件的的内容。
```sh
[root@MyLabC7 ~]# passwd -S openstack
openstack PS 2016-10-17 5 10 2 -1 (Password set, SHA512 crypt.)
#用户名   密码的状态  创建密码的时间   最短使用时间   最长使用时间  警告时间   非活动期间
```

7.使用-l或-u为锁定或解锁用户
```sh
[root@MyLab7 ~]# passwd -l user1
Locking password for user user1.
passwd: Success
[root@MyLab7 ~]# passwd -S user1
user1 LK 2016-09-13 1 9 2 5 (Password locked.)
```

我们查看/etc/shadow文件，发现密码字段前面加了两个!!，这就说明密码被锁定了。
```sh
!!$6$NhhbVRBv$B73bcrKI2.48bgzCAAtr6kfvuVJ8Knb6e/qfADJDv10AC/kD4Mia3IotKLePnkabCZMuErUEg1M9HnOPjGHO1.
 
[root@MyLab7 ~]# passwd -u user1
Unlocking password for user user1.
passwd: Success
[root@MyLab7 ~]# cat /etc/shadow | tail -1 |cut -d : -f 2
$6$NhhbVRBv$B73bcrKI2.48bgzCAAtr6kfvuVJ8Knb6e/qfADJDv10AC/kD4Mia3IotKLePnkabCZMuErUEg1M9HnOPjGHO1
```

8.密码可以通过管道去进行设置
```sh
[root@MyLab ~]# echo "wuhuiyun$3"|passwd --stdin user1
Changing password for user user1.
passwd: all authentication tokens updated successfully.
```

9.使用-d选项可以清除密码字段
```sh
[root@MyLabC7 ~]# passwd -d openstack
Removing password for user openstack.
passwd: Success
[root@MyLabC7 ~]# cat /etc/shadow |grep "openstack"
openstack::17464:5:10:2:::
```
那么清除密码之后就意味着任何用户都能登录这个系统了    



### **5.4.5 chage命令**

描述：修改用户密码的过期信息

用法：`chage [options] 用户名`

常用的选项：

​	-m		设置密码的最短使用期限

​	-M		设置密码的最长使用期限

​	-W		设置密码在过期前的警告时间

​	-E		修改账户的有效时间

​	-l		查看用户密码的使用期限

​	-d		修改最近一次修改密码的时间



用户可以通过`chage –l`来查看自己的密码使用期限

```sh
[openstack@MyLabC7 ~]$ chage -l openstack
Last password change         : Oct 25, 2017
Password expires    : Nov 04, 2017
Password inactive   : never
Account expires : never
Minimum number of days between password change  : 5
Maximum number of days between password change  : 10
Number of days of warning before password expires   : 2
```
但是不能使用这个命令去修改密码文件，只有root用户才行。

那么其它选项的用户和passwd差不多，这里就不详细描述了。

不过这里还要说一个选项：**-d**。

使用`-d`选项，可以修改最近一次修改密码的时间。那么这个有什么用呢？大家在登录学生教务系统的时候，第一次登陆会被要求强制改密码才能使用。`-d 0`就是这个意思。
```sh
[root@MyLab7 ~]# useradd user2
[root@MyLab7 ~]# echo "12345" | passwd --stdin user2 
Changing password for user user2.
passwd: all authentication tokens updated successfully.
[root@MyLab7 ~]# chage -l user2
Last password change    : Sep 13, 2016
Password expires    : never
Password inactive   : never
Account expires : never
Minimum number of days between password change  : 0
Maximum number of days between password change  : 99999
Number of days of warning before password expires   : 7
[root@MyLab7 ~]# chage -d 0 user2
[root@MyLab7 ~]# chage -l user2
Last password change    : password must be changed
Password expires    : password must be changed
Password inactive   : password must be changed
Account expires : never
Minimum number of days between password change  : 0
Maximum number of days between password change  : 99999
Number of days of warning before password expires   : 7
```



## **5.5 其它相关命令**

### **5.5.1 id命令**

描述：显示用户的uid和gid信息

用法：`id [OPTION]... [USER]`

常用的选项：

​	-u           显示UID

​	-g           显示gid

​	-G           显示所有组的GID

​	-gn          显示基本组组名

​	-Gn          显示所有组的组名



### **5.5.2 groups命令**

描述：用来查看用户加入了哪些组
```sh
[user1@MyLab7 ~]$ groups user4
user4 : user4 grp1 grp2
```



### **5.5.3 su命令**

描述：切换用户

用法：`su [options...] [-] [user]`

常用选项：

​	-l    指定用户名

​	-c    以指定用户的身份运行此处指定的命令	

**登录式切换**(会通过读取目标用户的配置文件来重新初始化):
​	su - USERNAME
​    	su -l USERNAME

**非登录式切换**(不会读取目标用户的配置文件进行初始化):
​	su USERNAME

注意：管理员可无密码切换至其它任何用户；

另外，指定选项`-c 'COMMAND'`：仅以指定用户的身份运行此处指定的命令；
```sh
[root@MyLabC7 ~]# su -c "whoami" openstack
openstack
```


### **5.5.4 whoami命令**

这个命令的使用也很简单，我们直接输入whoami可以查看系统当前的用户
```sh
[openstack@MyLabC7 ~]$ whoami
openstack
```



### **5.5.5 who命令**

who命令表示有哪些用户正登录系统。
```sh
[openstack@MyLabC7 ~]$ who
(unknown) :0           2016-10-17 12:30 (:0)
root     pts/0        2016-10-17 12:30 (192.168.10.1)
root     pts/1        2016-10-17 13:39 (192.168.10.1)
root     pts/2        2017-10-26 00:02 (192.168.10.1)
root     pts/3        2017-10-26 00:57 (192.168.10.1)
```



# **第6章 文件权限**



## **6.1 基本权限**



### **6.1.1 文件的属主与属组**

在Linux系统中，进程以发起者的身份运行，进程对文件的访问权限取决于发起此进程的用户的权限，这就是**安全上下文**。

一个进程若要访问某个文件，**首先系统会判断该进程的属主与文件的属主是否相同；如果相同，则应用属主权限；否则，则检查进程的属主是否属于文件的属组；如果是，则应用属组权限；否则，就只能应用other的权限**；

假如我现在创建一个openstack用户，并登录它，然后使用cat /etc/passwd，那么系统首先会检查/etc/passwd文件的属主是否是openstack，如果是，则应用属主权限，这里显然不是，所以进行下一步判断；然后系统继续检查/etc/passwd文件中的属组是否是openstack，如果是，则应用属组权限，如果不是，则应用其他权限；那么这里显示是应用其他权限。



### **6.1.2 权限**

权限简单可以理解为用户在登陆系统之后可以以何种方式访问资源，比如，是否能查看文件、创建目录以及删除文件等等。

在Linux中，文件可用的基本权限有：

​	r：可读

​	w：可写

​	x：可执行

这些权限三个分为一组，一共有三组，代表了属主、属组和其它用户的权限

![image_1cgeiuupu22tct71nge14l71qmp9.png-49.7kB](http://static.zybuluo.com/huiyun/zde27dtlvd69x3bj1qaklrxv/image_1cgeiuupu22tct71nge14l71qmp9.png)

对于普通文件和目录来说，其权限的含义是不相同的

|              |                 r                  |             w              |                             x                             |
| :----------: | :--------------------------------: | :------------------------: | :-------------------------------------------------------: |
| **普通文件** |           可读取文件内容           |       可修改文件内容       |                        可执行文件                         |
|   **目录**   | 可使用ls命令获取其下的所有文件列表 | 可创建或删除该目录下的文件 | 可cd至此目录中，且可使用ls -l来获取所有文件的详细属性信息 |

**重点**：文件的所有者对文件一定会具有写权限



### **6.1.3 chmod命令**

描述：改变文件的属性

用法：

​	chmod [OPTION]... MODE[,MODE]... FILE...

​	chmod [OPTION]... OCTAL-MODE FILE...

​	chmod [OPTION]... --reference=RFILE FILE...	

常用的选项：

​	-R		递归

​	--reference=soruce file

示例：
**1.操作指定某类用户权限(=)**

```sh
[root@MyLabC7 ~]# chmod u=rw /tmp/test
```

**2.操作指定某类用户的单个权限(+，-)**
```sh
[root@MyLabC7 ~]# chmod u-x,g+x /tmp/test
```

**3.还可以用户类别一起指定，如**
```sh
[root@MyLabC7 ~]# chmod go=- /tmp/test 
[root@MyLabC7 ~]# chmod a=r /tmp/test
```

**4.二进制权限表示法**

​	r		4

​	w		2

​	x		1	

```sh
[root@MyLabC7 ~]# chmod 755 /tmp/test
```

**5.参照文件的权限**
```sh
[root@MyLabC7 ~]# ls -l /etc/fstab 
-rw-r--r--. 1 root root 693 Dec 21 03:18 /etc/fstab
[root@MyLabC7 ~]# chmod --reference=/etc/fstab /tmp/test 
[root@MyLabC7 ~]# ls -l /tmp/test 
-rw-r--r--. 1 root root 693 Dec 21 04:56 /tmp/test
```



### **6.1.4 chown命令**

描述：修改文件的属主和属组

用法：

​	chown [OPTION]... [OWNER][:[GROUP]] FILE...

​	chown [OPTION]... --reference=RFILE FILE...	

常用选项：

​	-R		递归

​	--reference=soruce file	

示例1：
```sh
[root@MyLabC7 ~]# chown user1:root /tmp/test
```

示例2：修改属主
```sh
[root@MyLabC7 ~]# chown :user1 /tmp/test
```



### **6.1.5 umask命令**

当我们创建一个文件或目录的时候，总有一个默认权限，这个权限是怎么来的呢？这就是umask的作用了。

```sh
[root@MyLabC7 ~]# umask
0022
```

可以看到，umask显示了4个数字，这4个数字分别对应了4种权限，第一个0表示的是特殊权限，我们后面会描述，后三位分为是属主的权限，属组的权限和其它用户的权限。
​	
另外我们可以看到，创建文件的默认权限和创建目录的目录权限是不同的：
```sh
[root@MyLabC7 ~]# ls -l 
drwxr-xr-x. 2 root root 6 Dec 21 05:27 test
-rw-r--r--. 1 root root 0 Dec 21 05:27 test1
```

可以看到，创建文件的默认权限是`644`，而创建目录的默认权限是`755`，

于是我们可以得出一个结论：

-    **对于文件来说，创建时默认权限为：666-[umask值]**
-    **对于目录来说，创建时默认权限为：777-[umask值]**

另外要注意的是，创建文件时，文件是不具有x权限的，假如，我现在将umask的值设置为023，那么创建的文件默认权限应该是**666-023=643**。但是真的是这样的吗？
```sh
[root@MyLabC7 ~]# umask 023
[root@MyLabC7 ~]# umask 
0023
[root@MyLabC7 ~]# touch file
[root@MyLabC7 ~]# ls -l file
-rw-r--r--. 1 root root 0 Dec 21 05:33 file
```

可以看到，本来创建文件的默认权限应该是643，但由于创建文件时默认不会承认执行权限，所以在可执行权限位上加了一个1，即3+1=4，所以文件权限变成了644。

umask修改只针对当前用户的使用，即当前用户退出后重新登陆将不复存在

**假如我们给一个普通文件定义一个x权限，那么它就能执行吗？**

> 文件具有可执行权限和文件能否真正执行是两码事的。但是在linux中，要想执行文件，那么文件必须具有x权限才行，但是具有x权限并不意味你的文件能执行。所以为了区分这样会让我们搞的头疼的模糊概念，我们人为的给文件加上了后缀名，用来区分不同类型的文件。如：`.sh`是脚本文件；`.tar`是打包文件；`.zip`我们知道是压缩文件。所以，大家可以参考更多的资料去查看一下后缀名，但是linux在内核中是不认识这些后缀名的，就像前面所说，后缀名只是方便我们区分文件才有的。

另外，普通用户和root用户的umask值可能不相同，如：

​	**root用户**：022

​	**普通用户**：

​		属主和属组一致：002

​		属主和属组不一致：022



## **6.2 FACL访问控制**



### **6.2.1 FACL的概念**

**FACL(File Access Control Lost)**，它是一种文件的额外赋权机制，在原来的u,g,o之外，另一层让普通用户能控制赋权给另外的用户或组的授权机制。
​	
简单的来说，u，g，o这三个权限是针对某一类用户而设置的，那么如果我想对某个用户进行单独设置，那么可以使用FACL来实现。
​	
我们可以基于普通文件或目录进行ACL设置，通俗来说，ACL就是用来设定特定用户或用户组对某个文件的操作权限。并且如果对某个目录设置了访问控制策略，那么其目录下的子文件将继承其访问策略，如果对文件设置访问控制策略则不再继承上级目录的控制策略。



### **6.2.2 setfacl命令**

描述：设置文件的访问控制列表

用法：`setfacl [OPTION] FILE`

常用选项：

​	-b		删除所有的ACL条目

​	-m		增加ACL条目

​	-x		删除指定的ACL条目

​	-R		递归处理所有的子文件与子目录

示例1：添加ACL条目，使用户user1对test.txt文件可读可写
```sh
[root@MyLabC7 ~]# ls -l test.txt 
-rw-r--r--. 1 root root 0 Dec 21 06:18 test.txt
[root@MyLabC7 ~]# setfacl -m u:user1:rw test.txt 
[root@MyLabC7 ~]# ls -l test.txt 
-rw-rw-r--+ 1 root root 0 Dec 21 06:18 test.txt
```
添加acl条目后，你应该发现了后面那个点变成了+，这说明该文件对某一个用户设置了ACL，另外我们可以看到，原本文件只是644，设置acl后，文件的权限变成了664,哇，这就神奇了，设置acl还能修改文件的属组权限？

如果哪天你使用ls查看文件权限，发现后面有个＋，那么可能你眼前看到的权限可能是假的，此时你需要使用getfacl查看。
```sh
[root@MyLabC7 ~]# getfacl test.txt 
# file: test.txt            《====文件名
# owner: root               《====文件的属主
# group: root               《====文件的属组
user::rw-                   《====文件属主的权限
user:user1:rw-              《====针对个人用户user1所设置的权限
group::r--                  《====文件属组的权限
mask::rw-                   《====文件预设的有效权限
other::r--                  《====其他用户权限
```

我们可以看到，属组的权限根本就没有变过，还是**r--**。
​	
示例2：特定单一群组的权限设置
```sh
[root@MyLabC7 ~]# setfacl -m g:grp1:rwx /tmp/test
```

示例3：建议将其他人的acl权限设置为---
```sh
[root@MyLabC7 ~]# setfacl -m o::- /tmp/test 
[root@MyLabC7 ~]# getfacl /tmp/test
getfacl: Removing leading '/' from absolute path names
# file: tmp/test
# owner: user1
# group: user1
user::rw-
group::r--
group:grp1:rwx
mask::rwx
other::---
```

另外，还有一个mask，它是用于临时降低用户访问文件的权限的；使用者或群组所设定的权限必须要在mask所设定的权限范围之内才会生效。

我们现在举个例子：

```sh
[root@MyLabC7 ~]# setfacl -m u:user1:rw file2
[root@MyLabC7 ~]# getfacl file2
# file: file2
# owner: root
# group: root
user::rw-
user:user1:rw-
group::r--
mask::rw-
other::r--
```
现在我们将mask设置为---
```sh
[root@MyLabC7 ~]# setfacl -m m::--- file2
[root@MyLabC7 ~]# getfacl file2
# file: file2
# owner: root
# group: root
user::rw-
user:user1:rw-			#effective:---
group::r--			    #effective:---
mask::---
other::r--
```

我们可以看到，user1对这个文件已经没有了读写权限，那么是不是不能读了呢？
```sh
[user1@MyLabC7 ~]$ cat /root/file2
[user1@MyLabC7 ~]$
```

天呀，这个文件竟然可读，这是为什么呢？这是因为在acl中，user1已经被识别成了其它用户呢，而其它用户具有r--的权限，所以它能读，所以建议把这个讨厌的其它人设置为---
```sh
[root@MyLabC7 ~]# setfacl -m o::-,m::- file2
[root@MyLabC7 ~]# getfacl file2
# file: file2
# owner: root
# group: root
user::rw-
user:user1:rw-			#effective:---
group::r--			    #effective:---
mask::---
other::---
```

然后在访问，发现mask的设置生效了：
```sh
[user1@MyLabC7 ~]$ cat /root/file2
cat: /root/file2: Permission denied
```

mask只是一个临时行为，如果一般非人为设置，它会随着acl的设置而改变，如：
```sh
[root@MyLabC7 ~]# setfacl -m m::- file1 
[root@MyLabC7 ~]# getfacl file1
# file: file1
# owner: root
# group: root
user::rw-
user:user1:rw-			#effective:---
group::r--			    #effective:---
mask::---
other::r--
```

然后重新创建一个用户，授其权限:
```sh
[root@MyLabC7 ~]# useradd user2
[root@MyLabC7 ~]# setfacl -m u:user2:rw file1 
[root@MyLabC7 ~]# getfacl file1
# file: file1
# owner: root
# group: root
user::rw-
user:user1:rw-
user:user2:rw-
group::r--
mask::rw-
other::r--
```



## **6.3 文件的特殊权限**

### **6.3.1 进程权限匹配**

进程访问文件时首先检查进程的属主(进程的发起者)是否与文件的属主相同，如果相同，则应用属主权限；否则，则检查进程属组(可能有多个)其中之一是否与文件的属组相同，如果相同，则应用属组权限；否则，则应用others权限。

下面我们通过2个例子来进行理解：

**示例1：以user1用户执行/bin/cat去访问/etc/fstab**

```sh
# ls -l /etc/fstab
-rw-r--r--. 1 root  root    617 Aug  9 20:05 /etc/fstab
```

第1步：判断进程的属主是否与文件的属主相同：如果相同，则应用属主权限，否则进行第2步；进程cat的属主为user1，/ect/fstab的属主为root，故属主不相同
​    
第2步：判断进程的属组所属的组是否与文件的属组相同：如果相同，则应用属组权限，否则进行第3步；进程cat的属组为user1，/ect/fstab的属组为root，所以不相同
​    
第3步：应用其他权限，/etc/fstab文件的“其他”具有可读权限。故/tmp/cat能访问它。

**示例2：复制/etc/fstab文件至/tmp下，并修改/tmp/fstab的属主和属主为user1**
```sh
### 以user1用户执行/bin/cat 去访问/tmp/fstab。
# ls -l /tmp/fstab
---r--r--. 1 user1  user1    617 Aug  9 20:05 /tmp/fstab
```

第1步：判断进程的属主是否与文件的属主相同：如果相同，则应用属主权限，否则进行第2步；进程cat的属主为user1，/ect/fstab的属主为user1，故相同。应用属主权限，属主权限为0，所以不可以访问该文件。

> **说明：**此时user1是不能看到/tmp/fstab文件内容的，但是它能修改这个文件的内容，因为它是这个文件的属主。所以这一点要特别注意。

所以，我们知道，当我们执行`cat /etc/shadow`时，是无法访问文件的。但是使用`passwd`时，密码数据保存在`/etc/shadow`中，为啥可以将数据写入/etc/shadow呢？使用`ls –l`查看一下`/bin/passwd`
```sh
[user1@MyLab ~]$ ls -l /bin/passwd
-rwsr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
```

可以发现，x位上变成了s，这个s就是我们说的特殊权限只一：suid。



### **6.3.2  suid**

任何用户执行此可执行文件时，不再以用户自己的身份当作进程的属主，而是以COMMAND自身的二进制程序文件的属主当作进程的属主。

suid表现为文件属主执行权限为上的s或S，当表现为s的时候，说明文件的属主具有x权限，表现为S的时候，则表示没有x权限。

```sh
[root@MyLabC7 tmp]# ls -l /bin/passwd 
-rwsr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
[root@MyLabC7 tmp]# chmod u-x /bin/passwd 
[root@MyLabC7 tmp]# ls -l /bin/passwd 
-rwSr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
[root@MyLabC7 tmp]# chmod u-s /bin/passwd 
[root@MyLabC7 tmp]# ls -l /bin/passwd 
-rw-r-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
[root@MyLabC7 tmp]# chmod 4755 /bin/passwd 
[root@MyLabC7 tmp]# ls -l /bin/passwd 
-rwsr-xr-x. 1 root root 27832 Jun 10  2014 /bin/passwd
```

说明:
​	(1)suid仅对二进制文件有效
​	(2)该二进制文件需要具有x的可执行权限
​	(3)本权限在执行过程中有效
​	(4)用户将具有该进程的属主权限



### **6.3.3 sgid**

sgid可以对二进制可执行文件和目录进行设置，这里重点描述它只针对目录，表现为目录属组执行权限位上的s或S。

**演示：针对目录的演示**
1.添加一个组

```sh
[root@MyLabC7 ~]# groupadd deve_group
```

2.在/tmp目录下创建deve_dir目录
```sh
[root@MyLabC7 ~]# mkdir /tmp/deve_dir
```

3.给予其sgid的权限
```sh
[root@MyLabC7 ~]# chmod g+s /tmp/deve_dir
```

4.创建3个用户user1,user2,user3
```sh
[root@MyLabC7 ~]# for i in {1..3};do useradd user$i; done
```

5.使/tmp/deve_dir的所有用户具有w权限。
```sh
[root@MyLabC7 ~]# chmod go+w   /tmp/deve_dir
```

6.分别使用3个用户在该目录下创建一个文件，使用ls -l查看
```sh
[user1@MyLabC7 deve_dir]$ ls -l 
总用量 0
-rw-rw-r--. 1 user1 deve_group 0 11月  9 11:11 file1
-rw-rw-r--. 1 user2 deve_group 0 11月  9 11:11 file2
-rw-rw-r--. 1 user3 deve_group 0 11月  9 11:12 file3
```

可以发现，在这个目录下创建的文件，其属组都是deve_group

说明:
​	(1) 用户需要有进入这个目录的r与x的权限

​	(2) 具有sgid的目录，用户在此目录下创建文件时，新创建文件的属组不再是用户所属的基本组，而是继承了目录的属组

经过上面演示，发现一个问题，就是不同用户可以互相删除文件，我觉得这个的确不安全。那怎么办？



### **6.3.4 sticky**

粘带位，sticky，它表现为文件的 “其它用户执行权限位上的t或T”

说明：
​	(1) 当用户对此目录具有w,x权限时，就可以进入目录创建和删除文件

​	(2) 对于公共可写的目录，用户可创建文件，可删除自己的文件，但无法删除别人的文件。
​    
演示：
1.给上述目录deve_dir的其他用户加上t权限

```sh
[root@MyLabC7 ~]# chmod o+t /tmp/deve_dir
```

2.使用user2用户file3文件
```sh
[user2@MyLabC7 deve_dir]$ cat /etc/fstab >>file3
-bash: file3: 权限不够
```

3.使用user1用户删除file2文件

```sh
[user1@MyLabC7 deve_dir]$ rm -rf file2
rm: 无法删除"file2": 不允许的操作
```



### **6.3.5 文件的隐藏权限**

我们在winsow上随便看个文件属性，发现有以下内容：
![image_1cgeoag1p1j661adntb51145rrh13.png-36.4kB](http://static.zybuluo.com/huiyun/hqv0q5eo0vv30y9mnunmgrry/image_1cgeoag1p1j661adntb51145rrh13.png)

​	
这种属性是在文件权限之上的，那么在Linux中，可以使用lsattr来查看文件属性。

```sh
[root@MyLabC7 ~]# lsattr file1 
---------------- file1
```

这就表示文件没有设置任何属性，那么如果想要设置，可以通过chattr命令进行设置。
​	**指定某个参数**：+，-

​	**指定某类参数**：=

说明：对于不同文件系统，文件所支持的属性是不同的，在CentOS7上，默认支持的文件系统是XFS，所以它支持以下属性：

    a - append only
    A - no atime updates
    d - no dump
    i - immutable
    S - synchronous updates

**示例1：演示a属性**

```sh
[root@MyLab7 ~]# chattr +a /tmp/file1
[root@MyLab7 ~]# rm -rf /tmp/file1
rm: cannot remove ‘/tmp/file1’: Operation not permitted
```

可以看到，当设定a属性后，这个文件只能增加数据，不能删除也不能修改数据

**示例2：演示i属性**

```sh
[root@MyLabC7 ~]# chattr +i file1 
[root@MyLabC7 ~]# cat file1 
[root@MyLabC7 ~]# rm -rf file1
rm: cannot remove ‘file1’: Operation not permitted
```
当设定i属性后，这个文件不能被删除，改名，设定链接文件，也无法写入数据。
​	
我们可以查看文件的隐藏属性：

```sh
[root@MyLabC7 ~]# lsattr file1 
----i----------- file1
```

那么这个有啥用呢，我们举个例子看一下：如果我使用chattr命令去这样操作，我们可以想想会发生什么？
```sh
[root@MyLabC7 ~]# chattr +i /etc/passwd /etc/shadow /etc/group /etc/gshadow
```

+i参数代表的是任何数据都不能写入/etc/passwd、/etc/shadow等这些文件，那么这就意味着系统将不能创建用户和组，也不能修改密码。
```sh
[root@MyLabC7 ~]# useradd user1
useradd: cannot open /etc/passwd
[root@MyLabC7 ~]# passwd
Changing password for user root.
New password: 
Retype new password: 
passwd: Authentication token manipulation error
```



# **第7章 文件查找与打包**

## **7.1 文件查找**

### **7.1.1 whereis命令**

描述：用于查找二进制文件、源代码和命令帮助手册

用法：`whereis [-bmsu] filename`

常用选项：

​	-b		搜索二进制文件

​	-m		搜索命令帮助手册

​	-s		搜索源代码文件

​	-l		查询几个主要目录

由上可知，whereis 并没有全系统去查询文件，而是只找几个特定的目录而已。

whereis主要针对可执行文件的存放路径，帮助文件，和几个特定目录来处理的:
​	优点：查找快速
​     	缺点：就是有些文件可能查找不到。

1.查询passwd相关的文件
```sh
[root@MyLabC7 ~]# whereis passwd
passwd: /usr/bin/passwd /etc/passwd /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```

2.只查询passwd的二进制文件

```sh
[root@MyLabC7 ~]# whereis -b passwd
passwd: /usr/bin/passwd /etc/passwd
```

3.只查询passwd的帮助文档
```sh
[root@MyLabC7 ~]# whereis -m passwd
passwd: /usr/share/man/man1/passwd.1.gz /usr/share/man/man5/passwd.5.gz
```



### **7.1.2 locate命令**

描述：通过名称查找文件

用法：`locate [OPTIONS]...PATTERN...`

常用选项：

​	-b		显示匹配到的路径的基名

​	-i		忽略大小写的差异

​	-c		不输出文件名，仅计算查找文件的数量

​	-l #		仅输出#行

​	-S		查看数据库的相关信息

​	-r		使用正则表达式

Locate寻找的数据是由已建立的数据库里面去搜索到的，而不是直接去磁盘当作搜查，所以搜索很快。 

数据库路径：`/var/lib/mlocate`

```sh
[root@MyLabC7 ~]# ls /var/lib/mlocate/mlocate.db 
/var/lib/mlocate/mlocate.db
```

但是，数据库需要更新的，如果我们所找的文件是在数据库更新之前的文件，那么系统会告诉我们找不到该文件。因为需要更新数据。

手动更新数据库：updatedb，updatedb指令会先去读取`/etc/updated.conf`这个配置文件的设定。然后再去搜索整个硬盘，然后更新数据库。

注意：使用updatedb索引构建过程需要遍历整个根文件系统，极消耗资源；

1.使用locate进行模糊查找

```sh
[root@MyLabC7 ~]# locate pxelinux.0
/usr/share/syslinux/gpxelinux.0
/usr/share/syslinux/pxelinux.0
/var/lib/tftpboot/pxelinux.0
```

2.使用-c选项可以统计出locate匹配出了多少文件
```sh
[root@MyLabC7 ~]# locate -c pxelinux.0
3
```

3.locate进行的是模糊匹配，所以有时候匹配的字段可能包含在目录中。
```sh
[root@MyLabC7 ~]# locate pxelinux
/usr/share/doc/syslinux-4.05/pxelinux.txt
/usr/share/syslinux/gpxelinux.0
/usr/share/syslinux/gpxelinuxk.0
/usr/share/syslinux/pxelinux.0
/var/lib/tftpboot/pxelinux.0
/var/lib/tftpboot/pxelinux.cfg
/var/lib/tftpboot/pxelinux.cfg/default
```

我们可以看到，这种匹配有时可能不是我们所想要的，那么可以使用-b选项将这些文件排除。

```sh
[root@MyLabC7 ~]# locate -b pxelinux
/usr/share/doc/syslinux-4.05/pxelinux.txt
/usr/share/syslinux/gpxelinux.0
/usr/share/syslinux/gpxelinuxk.0
/usr/share/syslinux/pxelinux.0
/var/lib/tftpboot/pxelinux.0
/var/lib/tftpboot/pxelinux.cfg
```

4.查看locate数据库相关的信息
```sh
[root@MyLabC7 ~]# locate -S 
数据库 /var/lib/mlocate/mlocate.db:
14,750 文件夹
132,611 文件
6,947,183 文件名中的字节数
3,057,421 字节用于存储数据库
```

5. 可以使用-r “正则表达式”来进行模糊匹配
```sh
[root@MyLabC7 ~]# locate -r "makefile$"
/usr/src/kernels/3.10.0-327.el7.x86_64/scripts/mkmakefile
```

练习：查找以/var/lib/yum/history/开头的文件
```sh
[root@MyLabC7 ~]# locate -r "^/var/lib/yum/history"
/var/lib/yum/history
/var/lib/yum/history/2016-09-25
/var/lib/yum/history/history-2016-09-25.sqlite
/var/lib/yum/history/history-2016-09-25.sqlite-journal
/var/lib/yum/history/2016-09-25/1
/var/lib/yum/history/2016-09-25/1/config-main
/var/lib/yum/history/2016-09-25/1/config-repos
```



### **7.1.3 find命令**

描述：直接从磁盘上去查找

特点：

​	1.查找速度略慢

​	2.精确查找

​	3.实时查找

用法：`find [options] [查找路径] [查找条件] [处理动作]`

**1.根据文件名查找**

格式：`-name "文件名称"`

支持使用globbing(通配符)，如*、？、[]、[^]

```sh
[root@MyLabC7 ~]# find /etc -name "passwd"
/etc/passwd
/etc/pam.d/passwd
```

如果查找时不区分文件名称的大小写，那么可以使用-name进行查找

**2.根据文件的属主与属组查找**

格式：

​	`-user "用户名"`

​	`-group "组名"`

```sh
[root@MyLab tmp]# find /tmp -user "openstack" 
/tmp/wali
/tmp/test
[root@MyLab tmp]# find /tmp -group "openstack" 
/tmp/test
```

**3.根据文件的uid和gid查找**

格式：

​	`-uid UID`

​	`-gid GID`

```sh
[root@MyLab tmp]# find /tmp -uid 1002 
/tmp/test/test1
```

**4.查找没有属主和属組的文件**

格式：

​	`-nouser`

​	`-nogroup`

```sh
[root@MyLab tmp]# find /tmp -nouser 
/tmp/fedora
```

**5.根据文件类型查找**

格式：`-type 文件类型`

```sh
[root@MyLabC7 ~]# find /home -type f
[root@MyLabC7 ~]# find /home -type d
[root@MyLabC7 ~]# find /dev -type b
[root@MyLabC7 ~]# find /dev -type c
[root@MyLabC7 ~]# find /home -type l
[root@MyLabC7 ~]# find / -type s
```

**6.根据文件大小查找**

格式：

​	`-size [+|-]#[K|M|G]`

​		+#		x>#

​		-#		0<x<(#-1)

​		#		(#-1)<x<=#

```sh
[root@MyLabC7 ~]# find /etc -size 2k
```

**7.根据时间戳查找文件**

格式：

​	-atime	[+|-]#

​	-mtime	[+|-]#

​	-ctime	[+|-]#

​		+#		表示超过#天

​		-#		表示#天之内

​		#		表示第#天

```sh
# find /etc -mtime +5
# find /etc -mtime -5
# find /etc -mtime 5
```

**8.根据文件的权限查找**

格式：`-perm [/|-]MODE`

​	**/MODE:** 任何一类用户(u,g,o)的权限中的任何一位(r,w,x)符合条件即满足；9位权限之间存在“或”关系；常用于查找某类用户的某特定权限是否存在；

​	**-MODE:** 每一类用户(u,g,o)的权限中的每一位(r,w,x)同时符合条件即满足；9位权限之间存在“与”关系；

示例1：问：某个文件的权限为644，请问下列规则能够匹配?

```sh
A.-perm 600                                B.-perm /222
C.-perm /002                               D.-perm -444
```
答案：BD

示例2：
```sh
# find /usr -perm /4000
```

**9.组合条件查找**

格式：

​	-a		逻辑与

​	-o		逻辑或

​	-not,!	逻辑非

```sh
# find /tmp -not -user root
# find /tmp -not -user root -a -not -name fstab
# find /tmp -not \( -user root -o -name fstab \)
       !A -a !B=!(A  -o  B)
       !A -o !B=!(A  -a  B)
```

**10.处理动作**

常见指令：

​	-print：输出至标准输出，默认的动作；

​	-ls：类似于对查找到的文件执行“ls -l”命令，输出文件的详细信息；

​	-delete：删除查找到的文件；

​	-ok COMMAND {} \;   ：对查找到的每个文件执行由COMMAND表示的命令；每次操作都由用户进行确认；

​	-exec COMMAND {} \;  ：对查找到的每个文件执行由COMMAND表示的命令；	

**注意**：find传递查找到的文件路径至后面的命令时，是先查找出所有符合条件的文件路径，并一次性传递给后面的命令；但是有些命令不能接受过长的参数，此时命令执行会失败；另一种方式可规避此问题：

```sh
find | xargs COMMAND
```

示例：
```sh
# find  /usr   -nouser -o -nogroup   -atime  -7  |xargs ls -lh
```

**find中的-print0和xargs中-0的奥妙**

默认情况下，find每输出一个文件名，后面都会接着输出一个换行符，因此我们看到的find输出都是一行一行的，例如：

```sh
[root@node2 opt]# find . -mtime -1 -type f
./file1.txt
./file2.txt
```

如果我们想删除文件，可以这样：
```sh
# find . -mtime -1 -type f  | xargs rm 
```

我们也可以让find在打印出一个文件名之后接着输出一个NULL字符而不是换行，然后再告诉xargs
也用NULL字符来作为记录的分隔符。例如：

```sh
# find . -mtime -1 -type f -print0 | xargs -0 rm 
```

另外，还可以这样玩：
```sh
# find . -name "file*" | xargs -I {} cp -rf {} /opt/
# find . -name "file*" | xargs -i cp -rf {} /opt/
```
说明：

```sh
-i: 直接默认使用{}代替管道之前标准输出的内容
-I：指定替换字符来代替管道之前标准输出的内容
```


**练习：**

1.查找/var目录下属主为root，且属组为mail的所有文件和目录

```sh
$ find /var -user root -a -group mail 
```

2.查找/usr目录下不属于root,bin或hadoop的所有文件或目录

```sh
$ find /usr -not \(-user root -o -user bin -o -user hadoop\)
$ find /usr -not -user root -a -not -user bin -a -not -user hadoop
```

3.查找/etc目录下最近一周内其内容修改过，且属主不是root用户也不是hadoop用户的文件或目录；

```sh
$ find /etc -mtime 7 -a -not \(-user root -o -user hadoop\)
$ find /etc -mtime 7 -a ! -user root -a ! -user hadoop
```

4.查找当前系统上没有属或属组，且最近一周内曾被访问过的文件或目录；

```sh
$ find  /  \( -nouser -o -nogroup \)  -atime  -7  -ls
```

5.查找/etc目录下大于1M且类型为普通文件的所有文件；

```sh
$ find /etc -size +1M -a -type f
```

6.查找/etc目录下所有用户都没有写权限的文件

```sh
$ find /etc -not -perm /222   ##存在一个用户具有执行权限的反义
```

7.查找/etc目录至少有一类用户没有执行权限的文件；

```sh
$ find /etc -not -perm -111   ##所有用户都没有执行权限的反义
```

8.查找/etc/init.d/目录下，所有用户都有执行权限，且其它用户有写权限的所有文件；

```sh
$ find /etc -perm -113 
```



## **7.2 压缩与打包工具**

### **7.2.1 gzip工具**

描述：压缩或解压文件

用法：

​	`gzip [options] [name...]`

​	`gzip [options] [name...]`

​	`zcat [name...]`

常见的选项：

​	-c		将压缩结果送往标准输出，可以使用重定向将其保存为压缩文件从而保留源文件

​	-#		指定压缩比，1-9,数字越高，压缩比越大，但越消耗CPU资源

​	-d		解压缩

示例1：
```sh
[root@MyLabC7 tmp]# cp /etc/fstab .
[root@MyLabC7 tmp]# gzip fstab 
[root@MyLabC7 tmp]# ls
fstab.gz
```
我们可以看到，当我们使用gzip进行压缩的时候，在预设的状态下原本的文件会被压缩成.gz的文件，源文件就不在了
​    
示例2：使用zcat读取压缩文件中的内容
```sh
[root@MyLabC7 tmp]# zcat fstab.gz
```
这是由于zcat调用了类似more或less的命令去读取压缩文件中的内容，所以屏幕上会先fstab.gz解压缩之后的原文件内容。
​    
示例3：解压文件

```sh
[root@MyLabC7 tmp]# gzip -d fstab.gz 
[root@MyLabC7 tmp]# ls
fstab
```
同样，解压的时候不保留原文件。
​    
示例4：压缩文件并保留源文件
```sh
[root@MyLabC7 tmp]# gzip -c fstab >fstab.gz
[root@MyLabC7 tmp]# ls
fstab  fstab.gz
```



### **7.2.2 bzip2工具**

这款工具是为了取代gzip并提供更加的压缩比而来的。它的用法几乎和gzip一模一样。

示例1：使用bzip2压缩fstab
```sh
[root@MyLabC7 tmp]# bzip2 fstab
[root@MyLabC7 tmp]# file fstab.bz2 
fstab.bz2: bzip2 compressed data, block size = 900k
```

示例2：使用bzcat能读取*.bz2的压缩文件中的内容
```sh
[root@MyLabC7 tmp]# bzcat fstab.bz2
```

示例3：解压文件
```sh
# bzip2 -d fstab.bz2
```

示例4：压缩保留源文件
```sh
# bzip2 -c fstab >fstab.bz2
# bzip2 -k fstab
```



### **7.2.3 xz工具**

xz工具的压缩比率比bzip2还要高，但是它的用法形式和bzip2,gzip一样。

示例1：使用xz压缩
```sh
[root@MyLabC7 tmp]# xz fstab
[root@MyLabC7 tmp]# file fstab.xz 
fstab.xz: XZ compressed data
```

示例2：使用xzcat查看.xz格式的压缩文件的内容
```sh
[root@MyLabC7 tmp]# xzcat fstab.xz
```

示例3：解压
```sh
[root@MyLabC7 tmp]# xz -d fstab.xz
```

示例4：压缩时保留源文件

```sh
[root@MyLabC7 tmp]# xz -k fstab
```

示例5：比较三种工具
```sh
[root@MyLabC7 tmp]# ls -lhdrS /etc/* | tail -1 
-rw-r--r--.  1 root root 655K Jun  7  2013 /etc/services
[root@MyLabC7 tmp]# cp /etc/services .
[root@MyLabC7 tmp]# time gzip -c services >services.gz
real 0m0.019s
user 0m0.016s
sys  0m0.003s
[root@MyLabC7 tmp]# time bzip2 -c services >services.gz
real 0m0.041s
user 0m0.038s
sys  0m0.003s
[root@MyLabC7 tmp]# time xz -c services >services.gz
real 0m0.256s
user 0m0.238s
sys  0m0.017s
```

我们可以看到，执行速度最快的是gzip，执行最慢的是xz。如果你不觉得时间是你的成本，我们就可以使用cpu时间换取磁盘空间，如果时间是你重要的成本，请使用gzip工具。



### **7.2.4 tar工具**

前面我们介绍过了3种压缩工具，但是它们不能对目录进行压缩。如果我们想将多个文件或目录打包成一个大的文件，那么可以使用tar命令。下面我们来简单介绍一下：

用法：`tar [OPTIONS]...FILE...`

常用选项：

​	-cf		打包文件

​	-xf		展开文件

​	-rf		将文件追加到tarball包

​	-tf		查看打包文件的文件列表

​	-zcf		打包文件后以gzip压缩

​	-jcf		打包文件后以bzip2压缩

​	-Jcf		打包文件后以xz压缩

示例1：
```sh
[root@MyLabC7 tmp]# tar -zcf /tmp/etc.tar.gz /tmp/etc
```

示例2：   
```sh
[root@MyLabC7 tmp]# tar -tf etc.tar.gz
```

注意：当打包一个文件的时候，要进入它的上一级目录进行。

如：将/etc/service进行打包，如果我们这样做：

```sh
[root@MyLab6 ~]# tar -cvzf /tmp/services.tar.gz /etc/services
```
如果我们这样打包，打包的将会是整个目录，但是我们只想打包一个文件，所以为了实现这个，我们要事先进入/etc目录下，然后进行打包即可。



### **7.2.5 zip工具**

我们也可以看到在windows中也有使用zip压缩格式的，那么zip压缩格式在Linux中同样也可以使用。
​    
格式：`zip file.zip SRC`
​    
与其它压缩命令不同的是，zip进行压缩的时候会保存源文件,而且zip还能够打包目录。
```sh
[root@MyLabC7 tmp]# zip services.zip services 
```

解压.zip格式的文件我们可以使用unzip，同时保留原压缩文件。这我们在windows已经很常见了。



# **第8章 磁盘管理**

## **8.1 磁盘的种类**

磁盘根据磁盘的接口类型分为：

 - IDE磁盘
 - SATA磁盘
 - SCSI磁盘
 - SAS磁盘
 - SSD固态硬盘



### **8.1.1 IDE磁盘**

IDE磁盘，即磁盘使用的接口是IDE，也叫做ATA接口，它的本意是指把“硬盘控制器”与“盘体”集成在一起的硬盘驱动器。不过现在已经逐渐淘汰了，现在PC机所使用的硬盘大多数都是IDE兼容的。

![image_1cghe0tia1rmq1e1to2qdasc0a1m.png-67.5kB](http://static.zybuluo.com/huiyun/d2mjs0uystzgrm0bx3f2w21n/image_1cghe0tia1rmq1e1to2qdasc0a1m.png)



### **8.1.2 SCSI硬盘**

SCSI硬盘是采用的SCSI接口的硬盘，它是一种与IDE完全不同的接口，其优点是速度快、可靠性高，可支持热插拔(可以在服务器不停机的情况下，拔出或插入一块硬盘，操作系统自动识别硬盘的改动，这种技术对于24小时不间断运行的服务器来说，是非常必要的)



### **8.1.3 SATA硬盘**

SATA硬盘又叫做串口硬盘，目前我们普通使用的PC机硬盘基本上都是这种类型。

它的优势是：支持热插拔、传输速度快，执行效率高

SATA接口的速度：

​	SATA 1.0		150Mbytes

​	SATA 2.0		300Mbytes

​	SATA 3.0		600Mbytes

![image_1cghec1ga30n10381pjg3f11f0j2g.png-119.2kB](http://static.zybuluo.com/huiyun/4w7kdhj2j9xe4x1fql67pzdj/image_1cghec1ga30n10381pjg3f11f0j2g.png)



### **8.1.4 SAS硬盘**

SAS(Serial Attached SCSI)即串行连接SCSI，是新一代的SCSI技术，和现在流行的Serial ATA(SATA)硬盘相同，都是采用串行技术以获得更高的传输速度，并通过缩短连结线改善内部空间等。

SAS是并行SCSI接口之后开发出的全新接口。此接口的设计是为了改善存储系统的效能、可用性和扩充性，并且提供与SATA硬盘的兼容性

SAS接口:

​	SAS 1.0   300Mbyte/s   

​	SAS 2.0   600Mbyte/s   

​	SAS 3.0   1200Mbyte/s



### **8.1.5 SSD固态硬盘**

与传统的机械硬盘不同，固态硬盘采用的是固态电子存储芯片阵列而制成的硬盘，由控制单元和存储单元（FLASH芯片、DRAM芯片）组成。这就使得它的传输速度变得非常快。但是局限是容量小，并且价格高。



### **8.1.6 衡量磁盘性能的参数**

另外，衡量磁盘性能的一个重要指标：`iops`,每秒所发生的io操作次数

对于IDE的硬盘来说，每秒发生的磁盘I/O次数平均大概为100次；SCSI硬盘每秒所发生的I/O次数大概在150-200之间；SATA硬盘和IDE硬盘差不多，SAS也和SCSI差不多。

所以对于那些数据存取频繁的服务器来说，比如mysql服务器，100-200个I/O次数是远远不够的，于是就有了RAID，但这个I/O性能还是赶不上固态硬盘的，所以现在很多公司都开始使用固态硬盘来存储那些读写比较频繁的数据，从而提升系统性能。



### **8.1.7 企业中的磁盘选型**

企业里常见的SAS硬盘是15000转/分。当前主流300G、600G、1000G，从具体的业务需求及性价比考虑，一般在工作中多用300-600G的SAS硬盘，用于提供生产线上的普通对外提供服务的业务服务器：
​	
例如：生产线上的数据库业务、存储业务、图片业务及相关高并发业务（web http,cache服务），总的来说，如果没有特殊业务需求，SAS硬盘是生产环境首选的磁盘配置。
​	
企业级SATA硬盘7200-10000转/分，常见的容量为1T和2T，有点是经济实惠，容量大，从具体的业务需求及性价比考虑，一般在工作中多用SATA硬盘做线下不提供服务的数据存储或者并发业务访问不是很大的业务应用，比如站点程序及数据库，图片的线下备份等；特性：容量性价比高。

对于SAS盘和SATA盘，我们做个总结:
​	1.线上的业务，用SAS磁盘
 	2.线下的业务(不对外提供)，用SATA磁盘
   	3.线上高并发小容量的业务，使用SSD磁盘
​	
千万不要使用SATA磁盘来做线上的高并发服务的数据存储或数据库业务，因为这将极其影响架构的性能。





## **8.2 磁盘的结构**



### **8.2.1 磁盘的相关术语**

|   英文   |   中文   |
| :------: | :------: |
|   Disk   |   磁盘   |
|   Head   |   磁头   |
|  Sector  |   扇区   |
|  Track   |   磁道   |
| Cylinder |   柱面   |
|  Block   |  数据块  |
|  Inode   | 索引节点 |



### **8.2.2 磁盘的结构概述**

一般来说，一块磁盘有1个到数个盘片不等，其中每个盘片的有效盘面对应一个读写磁头，从上往下从0开始依次编号，不同的磁盘盘面在逻辑上被划分为磁道、柱面以及扇区，一般在出厂时就已经设定好了这些。如下图： 

![image_1cghh653h4j61sbbibmncd1l6bm.png-77.2kB](http://static.zybuluo.com/huiyun/vgsq3490reai2taflnsnhs5a/image_1cghh653h4j61sbbibmncd1l6bm.png)





### **8.2.3 磁头**

磁盘的每个盘片的每个有效的盘面都会有一个读写磁头（磁头数=盘片数×2）

在磁盘不工作的时候，磁头停靠在靠近主轴接触盘片的表面，这里是一个不存放任何数据的特殊区域，称为启停区或着陆区；启停区以外就是数据区。

在磁盘的最外圈，离主轴最远的磁道称为"0"磁道，磁盘数据的存放就是从最外圈"0"磁道开始的。

既然磁盘数据从最外圈开始，而停止时磁头又是在最内圈的启停区，那么磁盘开始工作的时候是如何找到"0"磁道的呢？那是因为在磁盘中还有一个用来完成磁盘初始定位的"0"磁道检测器构件，由这个构件完成对"0"磁道的定位。

"0"磁道非常重要，系统的引导程序就在0柱面0磁道1扇区的前446Bytes。



### **8.2.4 盘面**

磁盘的盘片一般是由铝合金材料或玻璃做基片。磁盘的每一个盘片都有两个盘面，即上、下盘面，一般来说，每个盘面都可以存储数据，成为有效盘面。

每一个这样的有效盘面都有一个盘面号，从上至下依次从“0”编号。



### **8.2.5 磁道**

磁盘在格式化时被划分成许多的同心圆，这些同心圆的轨迹叫做磁道（track），磁道由盘面从外向内依次从0开始编号。这些同心轨迹不是连续的记录数据，而是被化成一段段的圆弧，这些圆弧叫做扇区，扇区从“1”开始编号。 
​    
特别说明：磁道是一个逻辑概念，我们是看不见的。它在格式化的时候已经就规划完成了。



### **8.2.6 柱面**

所有的盘面上同一个磁道的不同盘面的组成叫做柱面（Cylinder），每个柱面由上而下开始从“0”开始编号。

柱面数和前面的磁道数是一样的。

![image_1cghhglr7u71131p1ttu1l541g8d20.png-126.1kB](http://static.zybuluo.com/huiyun/p7iseoxxmka84jpz9a6jmr26/image_1cghhglr7u71131p1ttu1l541g8d20.png)



### **8.2.7 扇区**

操作系统是以扇区(sector)为单位将信息存储在磁盘上的，一般情况下，每个扇区的大小是512字节。现在还有另外一种设计，就是扇区大小为4K。



### **8.2.8 如何高效存取数据**

**1.如何找到磁盘上对应的数据**

- 哪个盘面
- 哪个磁道
- 哪个扇区



**2.磁盘读写数据的时间**

- 寻道时间（约1-20ms）
- 旋转时间（约0-10ms）
- 传输时间（每4KB页<1ms)



**3.读取一个磁盘块**

- 最小时间=数据传输时间

- 最长时间=最大寻道时间(磁头从最内圈移到最外圈)+最大旋转时间(磁头旋转一周)+数据传输时间


**4.如何高效的存取数据**

- 降低I/O次数
- 降低排队等待时间
- 降低寻道旋转延迟时间
  - 同一磁盘连续块存储（可使用算法调优）
  - 同一柱面不同磁道并行存储（可使用算法调优）
  - 多个磁盘并行块存储（RAID）



## **8.3 磁盘分区**



### **8.3.1 磁盘分区基础**

磁盘是以扇区为单位来进行数据存储的，其中第一个扇区保存在主引导记录（MBR）和分区表信息（DPT）。一个扇区的大小为512bytes组成，其中主引导记录占用446bytes，分区表信息占64bytes，结束符占用2bytes。

每记录一个分区信息需要占用16bytes，所以MBR可分出4个分区。

每个分区中采用了4个字节来记录分区的扇区总数，每一位代表一个扇区，那么最多就有2^32个扇区。所以MBR支持的最大分区容量为：

```sh
2^32*512bytes=2^22*512kbytes=2^12*512mbytes=2^2*512Gbytes=2048G=2T
```

那么问题来了，MBR中真的只能分4个分区吗？

其实不是这样的，在MBR中，一般有**主分区，扩展分区，逻辑分区**的概念。

大致的规则如下：

- 一块磁盘最多只能支持4个分区表信息（主分区+扩展分区）
- 扩展分区最多只能有1个
- 如果想使用多个分区，可在扩展分区的基础上再次划分逻辑分区
- 扩展分区不能格式化

在Linux中，一切皆文件，所以这些磁盘设备在Linux中也会以文件的形式存在。

自CentOS6开始，所有的磁盘设备被标志为：/dev/sd[a-z]；该设备下的分区为：/dev/sd[a-z]#

​	**主分区**：最多有4个，设备名称为`/dev/sd[a-z][1-4]`

​	**逻辑分区**：可以有多个，其设备名称为`/dev/sd[a-z][5+]`



例1：如果我要将一块大硬盘暂时分成4个分区，同时，希望有其它空间让我再未来需要的时候再进行分区，那么该如何分区？

分析：

```sh
情况1：3P+1E
    对应的设备名称就是：
    /dev/sda1,/dev/sda2,/dev/sda3,/dev/sda5,/dev/sda6,/dev/sda7

情况2：2P+1E
    对应的设备名称就是：
    /dev/sda1,/dev/sda2,/dev/sda5,/dev/sda6,/dev/sda7,/dev/sda8

情况3：1P+1E
    对应的设备名称就是：
    /dev/sda1,/dev/sda5,/dev/sda6,/dev/sda7,/dev/sda8,/dev/sda9
```

例2：可不可以仅分1个主分区和一个扩展分区？



### **8.3.2 GPT分区(了解)**

使用MBR最大只能引导硬盘的大小是2T，所以对于大于2T的硬盘，那么MBR分区表就无法再使用了，并且如果MBR中的数据损坏了，将会难以修复。

所以我们要使用另外一种分区表来进行分区，听说以后也会逐渐取代MBR分区表。我们称它为**GPT**

为了兼容所有的硬盘，GPT在扇区的定义之上，使用**逻辑区块地址(LBA)**来处理。

每个LBA预设512bytes，从0开始编号。与MBR不同，MBR仅有一个512bytes的区块来记录信息，如果出现新问题，那么系统将无法运行，而GPT使用了34个LBA区块来记录分区信息。除前面34个LBA之外，整个磁盘的最后33个LBA也拿来作为另一个备份。结构如下所示：

![image_1cghlin2nkav4gf1smk16js1kk32d.png-107.7kB](http://static.zybuluo.com/huiyun/48fud6tpf2zc3m50u89a2umv/image_1cghlin2nkav4gf1smk16js1kk32d.png)

下面我们来具体描述一下:

 **LBA0**   

存放MBR引导记录，但是里面还有一个GPT的特殊的标志，用来表示此磁盘为GPT的意思。

**LBA1**   

 - 记录了分区表本身的位置和大小。   
 - 记录了备份用的GPT分区（就是前面谈到的在最后34个LBA区块）存放的位置。
 - 存放了分区表的校验机制码(CRC32),操作系统可以透过这个记录区来取得备份的GPT来恢复GPT的正常运作。  

**LBA2-33**   

从LBA2区块开始，每个LBA都可以记录4笔分区信息，所以在默认情况下，一个磁盘可以有32*4=128个分区。 

> **说明：**在GPT分区里已经没有主，延伸，逻辑分区的概念，每个分区信息都可以独立存在。但是要注意的是，在磁盘管理工具上面，fdisk命令并不认识GPT，要使用GPT的话，得要操作gdisk或者parted指令才行。



## **8.4 磁盘分区操作**

### **8.4.1 fdisk命令**

我们可以使用fdisk查看当前系统上有几块硬盘

```sh
[root@MyLabC7 ~]# fdisk -l /dev/sd[a-z]
 
磁盘 /dev/sda：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos   ##==>表示使用的是MBR分区表
磁盘标识符：0x000234c5
 
设备 Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     1026047      512000   83  Linux
/dev/sda2         1026048    41943039    20458496   8e  Linux LVM
     
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
```

接下来，我们真正使用fdisk进行分区吧。

格式：`fdisk DEVICE`

```sh
[root@MyLabC7 ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。
 
更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。
 
Device does not contain a recognized partition table
使用磁盘标识符 0x1164995e 创建新的 DOS 磁盘标签。
 
命令(输入 m 获取帮助)：
```

我们在末行输入m时，会出现如下帮助信息：

```sh
#命令(输入 m 获取帮助)：m
命令操作
       a   toggle a bootable flag
       b   edit bsd disklabel
       c   toggle the dos compatibility flag
       d   delete a partition     //删除一个分区
       g   create a new empty GPT partition table   //建立GPT分区表
       G   create an IRIX (SGI) partition table
       l   list known partition types   //列出分区类型
       m   print this menu           //帮助信息
       n   add a new partition       //创建一个新分区
       o   create a new empty DOS partition table   //创建MBR分区表
       p   print the partition table     //显示分区信息
       q   quit without saving changes    //退出
       s   create a new empty Sun disklabel
       t   change a partition's system id  //改变分区类型
       u   change display/entry units
       v   verify the partition table
       w   write table to disk and exit   //保存分区信息
       x   extra functionality (experts only)
 
#命令(输入 m 获取帮助)：
```

**1.创建分区**

如果想创建一个分区，输入字母"n"即可

```sh
#命令(输入 m 获取帮助)：n 
    Partition type:
       p   primary (0 primary, 0 extended, 4 free)
       e   extended
    #Select (default p):
```

输入n之后，会让你选择是主分区还是扩展分区，默认是主分区。

现在我们选择主分区，输入p，出现如下提示：

```sh
分区号 (1-4，默认 1)：
```

这个是选择分区号，默认就行，敲[Enter]键继续下一步：

```sh
起始 扇区 (2048-41943039，默认为 2048)：
```

同样，继续默认，敲Enter键下一步，如下显示：

```sh
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-41943039，默认为 41943039)：
```

这里选择的是分区的大小，假如我要给它分1G，那么+1G即可，输入完成之后，显示如下：

```sh
分区 1 已设置为 Linux 类型，大小设为 1 GiB
命令(输入 m 获取帮助)：	
```

这表示该分区信息已经在内存中形成了，但还没有保存到硬盘上去

如果此时你想查看该硬盘的分区信息，可以输入p。
```sh
#命令(输入 m 获取帮助)：p
     
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x1164995e
     
设备     Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
```

现在，我们再来创建一个分区，这次将该分区划分为扩展分区。
```sh
#命令(输入 m 获取帮助)：n
Partition type:
       p   primary (1 primary, 0 extended, 3 free)
       e   extended
#Select (default p): e
#分区号 (2-4，默认 2)：4
#起始 扇区 (2099200-41943039，默认为 2099200)：
将使用默认值 2099200
#Last 扇区, +扇区 or +size{K,M,G} (2099200-41943039，默认为 41943039)：+15G
分区 4 已设置为 Extended 类型，大小设为 15 GiB
```

但是，扩展分区是不能格式化的，它只能被用来划分成逻辑分区，所以，在这里我们在演示一下逻辑分区的划分。
```sh
#命令(输入 m 获取帮助)：n
Partition type:
      p   primary (1 primary, 1 extended, 2 free)
      l   logical (numbered from 5)
#Select (default p): l
添加逻辑分区 5
#起始 扇区 (2101248-33556479，默认为 2101248)：
将使用默认值 2101248
#Last 扇区, +扇区 or +size{K,M,G} (2101248-33556479，默认为 33556479)：+1G
分区 5 已设置为 Linux 类型，大小设为 1 GiB
     
#命令(输入 m 获取帮助)：p  
     
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x1164995e
     
设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
/dev/sdb4         2099200    33556479    15728640    5  Extended
/dev/sdb5         2101248     4198399     1048576   83  Linux
```

**2.保存并激活分区**

这样还没完，我们说了，上述的操作内容只是将它驻留在内存中，如果我们想使这块硬盘的分区生效，那么就要输入w，否则输入q
```sh
#命令(输入 m 获取帮助)：w
The partition table has been altered!
 
Calling ioctl() to re-read partition table.
正在同步磁盘。
```

好的，现在分区信息已经保存到硬盘上去了，我们可以使用cat /proc/partitions查看是否有该分区信息。
```sh
[root@MyLabC7 ~]# cat /proc/partitions 
major minor  #blocks  name
     
8        0   20971520 sda
8        1     512000 sda1
8        2   20458496 sda2
11       0    1048575 sr0
253      0   10485760 dm-0
253      1    1572864 dm-1
8       16   20971520 sdb
8       17    1048576 sdb1
8       20          1 sdb4
8       21    1048576 sdb5
253      2    8392704 dm-2
```

如果有，就代表分区激活成功，如果没有，故要使用partprobe激活硬盘。

使用这个命令的时候，你可以使用如下几种格式：
```sh
[root@MyLabC7 ~]# partprobe 
或
[root@MyLabC7 ~]# partprobe -s
/dev/sda: msdos partitions 1 2
/dev/sdb: msdos partitions 1 4 <5>
或
[root@MyLabC7 ~]# partprobe /dev/sdb
```

**3.查看分区类型**

如果我们想查看分区类型，那么可以使用输入l,在这里我们列出常见的几种分区类型：

```sh
5		扩展分区
82		Linux交换分区
83		Linux普通分区
8e		Linux LVM
```


**4.调整分区类型**

如果我们想调整分区类型，可以使用t，操作如下：
```sh
#命令(输入 m 获取帮助)：t  
#分区号 (1,4,5，默认 5)：5     //要修改的分区号
#Hex 代码(输入 L 列出所有代码)：82   //分区类型的标志
已将分区“Linux”的类型更改为“Linux swap / Solaris”
     
#命令(输入 m 获取帮助)：p
     
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x1164995e
     
设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048     2099199     1048576   83  Linux
/dev/sdb4         2099200    33556479    15728640    5  Extended
/dev/sdb5         2101248     4198399     1048576   82  Linux swap / Solaris
```



**5.删除分区**

我们想删除某个分区，可以使用d，具体操作如下：
```sh
#命令(输入 m 获取帮助)：d
#分区号 (1,4,5，默认 5)：1
分区 1 已删除
     
#命令(输入 m 获取帮助)：p
     
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x1164995e
     
设备 Boot      Start         End      Blocks   Id  System
/dev/sdb4         2099200    33556479    15728640    5  Extended
/dev/sdb5         2101248     4198399     1048576   82  Linux swap / Solaris
	
如果你确定要删除某个分区，删除之后记得使用w保存退出。
```



### **8.4.2 parted命令**

使用fdisk命令进行分区的实质就是修改MBR中的DPT分区表信息，那么分区的磁盘大小必须小于2T，如果大于2T呢？分区就用parted命令。

parted的选项好多，但是大部分用不上，所以我们无须忧虑。

这里由于我们不可能拿出一块2T的硬盘进行分区，所以只能使用parted进行一个小小的模拟。但是在模拟之前，我们需要把硬盘转换成GPT格式，因为GPT支持大于2T的硬盘分区。
​	
我们实验还是用我们刚才的/dev/sdb这块硬盘，但是现在我要使用parted命令删除之前用fdisk命令建立好的分区。在此之前，我们先显示一下分区信息。
​	
**1.显示分区信息**
直接输入print即可
```sh
#(parted) print
Model: ATA VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags: 
     
Number  Start   End     Size    Type      File system  标志
 1      1049kB  1075MB  1074MB  primary
 4      1075MB  17.2GB  16.1GB  extended
 5      1076MB  2150MB  1074MB  logical
```
好的，现在让我们把这些分区一一删除。
​	
**2.删除分区**
直接输入 `rm #(分区对应的号码)`
```sh
#(parted) rm 1   
#(parted) rm 4
#(parted) rm 5                                                                
#(parted) print                                                            
Model: ATA VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags: 
     
Number  Start  End  Size  Type  File system  标志
     
(parted)   
```

**3.调整磁盘的分区表格式**
使用mklabel将磁盘的分区格式修改为gpt
```sh
#(parted) mklabel gpt                                                      
警告: The existing disk label on /dev/sdb will be destroyed and all data on this disk will be lost. Do you want to continue?
#是/Yes/否/No? yes   
#(parted) p                                                                
Model: ATA VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 
     
Number  Start  End  Size  File system  Name  标志
```

**4.创建分区**
使用mkpart创建分区，操作如下：
```sh
#(parted) mkpart primary 0 500
警告: The resulting partition is not properly aligned for best performance.                                      
#(parted) p                                                                
Model: ATA VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 
     
Number  Start   End    Size   File system  Name     标志
 1      17.4kB  500MB  500MB               primary
```
注意，这里要说的是，使用GPT格式进行分区的时候，就没有所谓的主分区，扩展分区，逻辑分区之说了，这里的primary只是分区的名字而已。
​	
我们再创建一个分区
```sh
#(parted) mkpart logical 501 1000
#(parted) p                                                                
Model: ATA VMware Virtual S (scsi)
Disk /dev/sdb: 21.5GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 
Number  Start   End     Size   File system  Name     标志
 1      17.4kB  500MB   500MB               primary
 2      501MB   1000MB  499MB               logical
```

分区创建完成之后，直接使用quit退出即可。然后查看该硬盘是否激活
```sh
[root@MyLabC7 ~]# cat /proc/partitions 
major minor  #blocks  name
     
8        0   20971520 sda
8        1     512000 sda1
8        2   20458496 sda2
11       0    1048575 sr0
253      0   10485760 dm-0
253      1    1572864 dm-1
```

## **8.5 dd命令**

dd命令和cp命令类似，都是复制，只不过dd命令针对磁盘进行操作，cp针对文件系统进行操作。
​	
dd的命令使用：

​	dd if=/PATH/FROM/SRC of=/PATH/TO/DEST

​	dd </PATH/FROM/SRC >/PATH/TO/DEST	



### **8.5.1 数据备份/恢复**

**1.数据备份**

1.将本地的/dev/sda整盘备份到/dev/sdb
```sh
$ dd if= /dev/sda of=/dev/sdb
```

2.将/dev/sda全盘数据备份到指定路径的image文件
```sh
$ dd if=/dev/sda of=/path/to/image
```

3.备份/dev/sda全盘数据，并利用gzip压缩保存到指定路径
```sh
$ dd if=/dev/sda | gzip > /path/to/image.gz
```



**2.数据恢复**

1.将备份文件恢复到指定盘

```sh
$ dd if=/path/to/image of=/dev/sda
```

2.将压缩备份文件恢复到指定盘

```sh
$ gzip -dc /path/to/image | dd of=/dev/sha
```



### **8.5.3 备份/恢复MBR**

-------------------------------------------------------------------
1.备份MBR
```sh
$ dd if=/dev/sda of=/path/to/file bs=512 count=1
```

2.恢复MBR
```sh
$ dd if=/path/to/file of=/dev/sda 
```



### **8.5.3 备份ISO镜像文件**

-------------------------------------------------------------------
```sh
$ dd if=/dev/cdrom of=/root/cd.iso
```



### **8.5.4 增加swap分区大小**

-------------------------------------------------------------------
1、创建一个大小为256M的文件：
```sh
# dd if=/dev/zero of=/swapfile bs=1024 count=262144
```

2、把这个文件变成swap文件：
```sh
# mkswap /swapfile
```

3、启用这个swap文件：
```sh
# swapon /swapfile
```

4、编辑/etc/fstab文件，使在每次开机时自动加载swap文件：
```sh
/swapfile swap swap default 0 0
```


### **8.5.5 销毁磁盘**

-------------------------------------------------------------------
```sh
# dd if=/dev/urandom of=/dev/hda1
```
注意：利用随机的数据填充硬盘，在某些必要的场合可以用来销毁数据。



### **8.5.6 测试磁盘的读写速度**

-------------------------------------------------------------------
测试硬盘的读、写速度
```sh
dd if=/root/1Gb.file bs=64k | dd of=/dev/null
dd if=/dev/zero of=/root/1Gb.file bs=1024 count=1000000
```



### **8.5.7 确定磁盘的最佳大小块**

-------------------------------------------------------------------
通过比较dd指令输出中所显示的命令执行时间，即可确定系统最佳的block size大小
```sh
dd if=/dev/zero bs=1024 count=1000000 of=/root/1Gb.file
dd if=/dev/zero bs=2048 count=500000 of=/root/1Gb.file
dd if=/dev/zero bs=4096 count=250000 of=/root/1Gb.file
dd if=/dev/zero bs=8192 count=125000 of=/root/1Gb.file
```


### **8.5.8 修复硬盘**

-------------------------------------------------------------------
```sh
# dd if=/dev/sda of=/dev/sda 或dd if=/dev/hda of=/dev/hda
```
当硬盘较长时间(一年以上)放置不使用后，磁盘上会产生magnetic flux point，当磁头读到这些区域时会遇到困难，并可能导致I/O错误。
​    
当这种情况影响到硬盘的第一个扇区时，可能导致硬盘报废。上边的命令有可能使这些数据起死回生。并且这个过程是安全、高效的



### **8.5.9 模拟小磁盘**

-------------------------------------------------------------------
1.这相当于创建一个小磁盘
```sh
[root@MyLabC7 conf]# dd if=/dev/zero of=/dev/sdc bs=8K  count=10
10+0 records in
10+0 records out
81920 bytes (82 kB) copied, 0.000415691 s, 197 MB/s
```

2.创建完成之后，我们使用查看下是否有sdc这个文件
```sh
[root@MyLabC7 conf]# ls -l /dev/sdc
-rw-r--r--. 1 root root 81920 Nov 21 18:02 /dev/sdc
```

3.格式化文件系统，我们这里使用ext3。

```sh
[root@MyLabC7 conf]# mkfs -t ext3 /dev/sdc
```

4.挂载
```sh
[root@MyLabC7 conf]# mount -o loop /dev/sdc /app/log/
[root@MyLabC7 conf]# df -h
```
---



# **第9章 文件系统**

## **9.1 文件系统简介**

文件系统是对存储设备上的元数据和数据进行组织的一种机制，目的是易于查询和存储数据，因此磁盘上如果没有文件系统也就无法存储数据了；因此，在磁盘分区后，还有对磁盘进行格式化形成文件系统才行。



### **9.1.1 文件系统的类型**

在Linux中，支持诸多类型的文件系统，如下所示：

- 传统的ext2文件系统
- 日志文件系统：ext3、ext4、xfs、btrfs等
- 网络文件系统：NFS、CIFS（samba）
- 伪文件系统：/proc
- 光盘：ISO9660
- 用户空间的分布式文件系统：mogilefs、glusterfs
- 内核空间的分布式文件系统：ceph
- 集群文件系统：gfs2



### **9.1.2 虚拟文件系统(VFS)**

Linux所支持的各种文件系统，其实现细节均不相同。举例来说，这些差异包括文件块的分配方式，以及目录的组织方式。如果每个与文件打交道的程序都需要理解各种文件系统的具体细节，那么编写与各类文件系统交互的程序将几乎不可能实现。

所以我们需要有一种机制来将各种文件系统给它抽象化，这样程序在访问的时候只要通过该机制去解决程序与不同文件系统的交互性，这种机制我们称之为VFS，简称虚拟文件系统。如下图：

![image_1cgrlfphjdokogp2v51lb21hee9.png-121.8kB](http://static.zybuluo.com/huiyun/th779dw2egsr9ibtyc2sz6n9/image_1cgrlfphjdokogp2v51lb21hee9.png)

可以看到：

- VFS针对文件系统定义了一套通用的接口，所以与文件交互的程序都会按照这一接口进行操作
- 每种文件系统都会提供VFS接口的实现

这样一来，程序只需要理解VFS接口，而无需过问文件系统的具体实现细节。



## **9.2 文件系统结构**



对于文件系统来说，我们目前只需要了解三个东西：**inode**、**block**和**文件名**

![file:///tmp/ct_tmp/1.png](file:///tmp/ct_tmp/1.png)



### **9.2.1 Block**

在文件系统中，用来分配空间的基本单位是**块(block)**，文件中的数据是存放在block中的。

Block的大小有1k，2k，4k。这就意味着我们可以选择合适的block大小来进行设置。

一个文件无论多大，都必须占用一个block，也就是说，一个block只能属于一个文件。

**下面看一个示例**：

将block的大小设置为4K，而文件系统中有100000个普通小文件，每个文件大小为50bytes，请问会浪费多少空间？

**解析**：

block的大小4K=4096bytes，一个文件的大小是50bytes，它会占用一个block，所以浪费了`4096-50=4046bytes`。文件系统中有10000个这样的小文件，也就是说，大概会浪费几乎40M的空间，而文件的总大小大概是500kbytes，可以发现，不到1M的文件竟然会浪费30M的空间，且文件越多，浪费的空间越大。

那么既然这样，我把block设置成1K不就行了？但是这时我们却忽略了另外一个问题，那就是block小了，但是文件变大了，那么这就意味着需要更多的block，那么读取多个block就会消耗磁盘I/O。所以如果这时block大了，那么读取大文件的速度肯定快了，从而减少磁盘I/O。

所以建议：

- 如果是大文件的业务，block尽量大一点
- 如果是小文件的业务，block尽量小一点

**查看当前系统的块大小**

(1)针对etx系列的文件系统

```sh
# dumpe2fs -h /dev/vda1 | grep "Block size";
dumpe2fs 1.42.9 (28-Dec-2013)
Block size:               4096
```

(2)针对xfs的文件系统

```sh
# xfs_info /dev/sda | grep "bsize";
```



### **9.2.2 Inode**

刚才说了，文件的数据存放在block中，那么文件的元数据存放在哪呢？文件的元数据存放在inode中，**Inode**简称索引节点。

我们可以使用`ls -li`查看

```sh
[root@huyiun ~]# ls -li file1 
138861 -rw-r--r-- 1 root root 997 Jan  7 09:34 file1
```

对于Inode来说，它不仅包含了文件的元数据，它还有记录指向Block的指针，这样系统就可以通过Inode来快速查找文件了。

和block一样，每一个inode只属于一个文件。

**问题**：一个1000M的磁盘分区，分别写入1K的文件、1M的文件，分别可以写多少个？

**错误解答**：100M/1K=10000个，100/1=100个

**分析**：

在这个例子中，我们忽略了inode和block的比率，即我们要分析是Block先用完还是inode先用完。

接下来，我们来演示这个例子：

1.创建3个分区，其大小分别为100M。
```sh
[root@MyLabC7 ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。
     
更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。
     
     
#命令(输入 m 获取帮助)：n
Partition type:
       p   primary (0 primary, 0 extended, 4 free)
       e   extended
#Select (default p): p
#分区号 (1-4，默认 1)：
#起始 扇区 (2048-41943039，默认为 2048)：
将使用默认值 2048
#Last 扇区, +扇区 or +size{K,M,G} (2048-41943039，默认为 41943039)：+100M
分区 1 已设置为 Linux 类型，大小设为 100 MiB
     
#命令(输入 m 获取帮助)：n
Partition type:
       p   primary (1 primary, 0 extended, 3 free)
       e   extended
#Select (default p): 
Using default response p
#分区号 (2-4，默认 2)：
#起始 扇区 (206848-41943039，默认为 206848)：
将使用默认值 206848
#Last 扇区, +扇区 or +size{K,M,G} (206848-41943039，默认为 41943039)：+100M
分区 2 已设置为 Linux 类型，大小设为 100 MiB
     
#命令(输入 m 获取帮助)：n
Partition type:
       p   primary (2 primary, 0 extended, 2 free)
       e   extended
#Select (default p): 
#Using default response p
#分区号 (3,4，默认 3)：
#起始 扇区 (411648-41943039，默认为 411648)：
将使用默认值 411648
#Last 扇区, +扇区 or +size{K,M,G} (411648-41943039，默认为 41943039)：+100M
分区 3 已设置为 Linux 类型，大小设为 100 MiB
     
#命令(输入 m 获取帮助)：w
The partition table has been altered!
     
Calling ioctl() to re-read partition table.
正在同步磁盘。
```

2.格式化文件系统，分别指定其block大小为1K,2K,4K.

```sh
~]# mkfs.ext4 -b 1024 /dev/sdb1
~]# mkfs.ext4 -b 2048 /dev/sdb2
~]# mkfs.ext4 -b 4096 /dev/sdb3
```

3.查看设置是否成功

```sh
~]# dumpe2fs /dev/sdb1 | grep -i "block size"
~]# dumpe2fs /dev/sdb2 | grep -i "block size"
~]# dumpe2fs /dev/sdb3 | grep -i "block size"
```

4.挂载这三个分区。
```sh
[root@MyLabC7 ~]# mkdir /data1
[root@MyLabC7 ~]# mkdir /data2
[root@MyLabC7 ~]# mkdir /data3
[root@MyLabC7 ~]# ls /
bin  boot  data1  data2  data3  dev  etc  home  lib  lib64  media  mnt   proc  root  run  sbin  srv  sys  tmp  usr  var
[root@MyLabC7 ~]# mount /dev/sdb1 /data1
[root@MyLabC7 ~]# mount /dev/sdb2 /data2
[root@MyLabC7 ~]# mount /dev/sdb3 /data3
```

5．使用df -h查看
```sh
[root@MyLabC7 ~]# df -h /dev/sdb*
文件系统        容量  已用  可用 已用% 挂载点
devtmpfs        475M     0  475M    0% /dev
/dev/sdb1        93M  1.6M   85M    2% /data1
/dev/sdb2        89M  614K   82M    1% /data2
/dev/sdb3        93M   72K   86M    1% /data3
```
我们可以看到，我们还没有往磁盘上存数据，磁盘的空间就已经占用了一些，这些内容就是记录文件系统信息的数据。

现在，我们先来个理论，一个分区大小为85M，block的大小为1K的情况下，可以创建多少个文件？

**分析**：对于上述条件下，写一个文件会占用一个inode和一个block，那么这就意味着，要看该分区能存放多少个1k的文件就要看inode和block谁先耗尽。我们可以分别查看一下inode和block的数量。

```sh
[root@MyLabC7 ~]# dumpe2fs /dev/sdb1 | grep -i "inode count"
dumpe2fs 1.42.9 (28-Dec-2013)
Inode count:              25688
[root@MyLabC7 ~]# dumpe2fs /dev/sdb1 | grep -i "block count"
dumpe2fs 1.42.9 (28-Dec-2013)
Block count:              102400
Reserved block count:     5120
```
我们可以发现，inode的数量要少，所以对于一个1K的文件来说，该分区能存放的文件个数为inode的大小，即25688个。是不是呢？我们来验证一下：
​	
现在我们在/data1下来写一个1K的文件

```sh
[root@MyLabC7 data1]# for i in `seq 512`;do echo 1 >>test;done
[root@MyLabC7 data1]# ls -l test 
-rw-r--r--. 1 root root 1024 11月 17 16:18 test
```

然后重复写100000次，看它什么时候写满
```sh
[root@MyLabC7 data1]# for i in `seq 100000`;do /bin/cp test $i;done
```

然后写满之后，我们再查看一下：
```sh
[root@MyLabC7 ~]# df -h /dev/sdb1
文件系统        容量  已用  可用 已用% 挂载点
/dev/sdb1        93M   28M   59M   32% /data1
```
我们可以发现，虽然数据满了，但是上面却显示还有32%的空间可用，那么这是为什么呢？那是因为inode满了。
```sh
[root@MyLabC7 data1]# df -i /dev/sdb1
文件系统       Inode 已用(I) 可用(I) 已用(I)% 挂载点
/dev/sdb1      25688   25688       0     100% /data1
```

然后我们再查看一下/data1里面有多少文件：
```sh
[root@MyLabC7 data1]# ls -l | wc -l
25679
```

我们可以发现我们之前算得那个应该只能算理论中，而实际中未必会准确。

现在我们试试写空文件，看这次能写多少个？
```sh
[root@MyLabC7 data1]# for i in `seq 30000 `;do touch $i;done
[root@MyLabC7 data1]# ls -l | wc -l
25679
```
其他情况大家可以自行测试一下。

**现在我们可以总结一下了**：
如果往一个分区中能存放多少文件，要看文件的大小和block的大小，以及inode的数量有关。对于小文件来说，先消耗完的可能是inode，所以出现的情况就是磁盘满了，我查看df查看空间还有剩余，但是inode却满了。
​	
所以有时候，我们会看到磁盘报错“no space left on device”,但是使用df -h 查看时发现磁盘空间还有剩余，这就是因为inode满了。



### **9.2.3 文件名**

对于一个常规文件来说，其block存放着是文件的内容，但是对于一个目录来说，其block存放的是该目录下所有文件inode以及对应的文件名。

目录中对应的block内容：

![image_1cgrmq0rh1fd1128813081hsh136j2t.png-62.9kB](http://static.zybuluo.com/huiyun/01c8dgmpft18hcqu3870j39q/image_1cgrmq0rh1fd1128813081hsh136j2t.png)

当我们使用`ls -ld .`查看目录大小的时候，指的是目录本身所占用的block大小，而非目录下文件大小的总和

如果目录树结构太长，那么系统去搜索这个文件就会浪费太多的时间。所以linux系统提供一种缓存机制，只有目录文件被访问过，其inode和目录就会被缓存下来，提高系统性能下来

**问题1：文件系统如何删除文件**

**分析：**

在文件系统中，还记录了其它信息，其中就包括**block位图**和**Inode位图**，它们的原理一样，使用"0"bit位来标记block或inode未使用，使用"1"bit位来标记block和inode已经使用。

如果要删除文件，系统就会清空inode位图、Block位图的状态，将"1"变成了"0"。当写入文件的时候，系统就会去找标记为"0"的inode和block去存储信息，如果刚好找到这个存有信息的inode和block，直接覆盖。所以，删除文件其实就是将inode位图、块位图的状态从"1"变成"0"，而数据并不需要删除。所以有时候数据被我们误删了，只要不存新的数据进去，还是有可能复原的。

**问题2：文件系统内如何复制移动**

**分析**：

复制，其实就是系统重新申请了inode和block去记录原文件的信息。

要说移动，那么就要看它是否在同一分区上。如果在同一分区上，及其简单，比如，将/etc/passwd移动到/tmp/abc目录中去，只需要将/tmp/abc目录所在的信息表中增加一个指向passwd文件的inode和其文件名，并删除/etc中信息表中passwd的inode和文件名即可。

如果不在同一分区，由于inode不能跨分区，那么只能在分区上申请inode和block去记录被移动的文件，和复制的操作差不多一样。



### **9.2.4 日志文件系统**

我们可以想象一下这样的问题：如果有一天万一我们在进行写操作的时候，系统突然中断（系统发生错误，停电等），那么我们还在写的数据会怎么办？怎样知道这个文件是否完整？

**方法一：系统在重启的时候，遍历整个文件系统，检查文件的完整性。**

- 缺点：浪费时间

为了在上述况情况下解决文件同步问题，提出了日志文件系统。

步骤：

​	1、当系统要写入一个文件的时候，会先在日志记录区中记录某个文件将要写入的信息（元数据）。

​	2、当系统正在写入数据的时候会同步磁盘上的数据并且更新日志记录区(即元数据中的内容会改变，如inode的数量，block的数量等)。

​	3、数据同步完之后，在日志记录区中完成该文件的记录，并把元数据撤回去

这样，如果在记录数据的时候发生问题，系统只要去日志记录区块去检查日志文件信息，根据其文件的元数据，就可以知道哪个文件出现了问题。当然，这也是日志文件最基础的功能。

要知道的是，ext2并不具有日志功能，但是ext3,ext4,xfs等文件具有日志功能，因此它们也被称为**日志文件系统**。



## **9.3 ext系列管理工具**

硬盘在分区之后还不能使用，要想使用，还必须进行格式化形成文件系统。



### **9.3.1 mkfs命令**

其实`mkfs`是一个综合指令，我们可以通过这个指令创建以下类型文件系统：

```sh
# mkfs.
mkfs.btrfs   mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.ext4    mkfs.minix   mkfs.xfs   
```

例如，我想创建一个ext4格式的文件系统，可以进行如下操作：

```sh
[root@MyLabC7 ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)
文件系统标签=
OS type: Linux
块大小=1024 (log=0)
分块大小=1024 (log=0)
Stride=0 blocks, Stripe width=0 blocks
25688 inodes, 102400 blocks
5120 blocks (5.00%) reserved for the super user
第一个数据块=1
Maximum filesystem blocks=33685504
13 block groups
8192 blocks per group, 8192 fragments per group
1976 inodes per group
Superblock backups stored on blocks: 
8193, 24577, 40961, 57345, 73729
     
Allocating group tables: 完成                            
正在写入inode表: 完成                            
Creating journal (4096 blocks): 完成
Writing superblocks and filesystem accounting information: 完成
```



### **9.3.2 mke2fs命令**

描述：此命令是专门用来创建ext系列的文件系统的

格式：`mke2fs [options] device`

常用选项：

​	-t		指定文件系统的类型

​	-b		指定Block的大小

​	-i		bytes-per-inode，inode的个数=bytes/bytes-per-inode

​	-N		指定inode的数量

​	-O		指定文件系统的特性

​	-q		静默模式，不输出文件系统创建的信息

​	-L		卷标名

示例1：格式化分区

```sh
[root@MyLabC7 ~]# mke2fs -t ext3 /dev/sdb1
```

2.使用-L label 可指定分区的卷标名称
```sh
[root@MyLabC7 ~]# mke2fs -t ext4 -L data /dev/sdb1
```

3.使用-b {1024|2048|4096}:指定块大小
```sh
[root@MyLabC7 ~]# mke2fs -b 1024 -t ext4 /dev/sdb1
```

4.使用-q选项可以忽略文件系统的创建的过程信息

```sh
[root@MyLabC7 ~]# mke2fs -q -b 1024 -t ext4 /dev/sdb1
```
5.使用-N指明要给该文件系统创建多少个inode的数量，当然要在总的inode的数量之内。或者我们也可以使用-i来指定inode与字节的比率，即多少字节创建一个inode。
```sh
[root@MyLabC7 ~]# mke2fs -N 10000000 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)
mke2fs: inode_size (128) * inodes_count (10000000) too big for a filesystem with 102400 blocks, specify higher inode_ratio (-i) or lower inode count (-N).
```
那么,我们使用mke2fs的时候,不加任何选项就会自动生成默认参数,这些参数就是从`/etc/mke2fs.conf`文件中来的。
```sh
[root@MyLabC7 ~]# cat /etc/mke2fs.conf 
[defaults]
base_features = sparse_super,filetype,resize_inode,dir_index,ext_attr
default_mntopts = acl,user_xattr
enable_periodic_fsck = 0	
blocksize = 4096	
inode_size = 256
inode_ratio = 16384
     
[fs_types]
……………(此处省略)
```



### **9.3.3 lsblk命令**

分区完成后，我们可以通过lsblk来查看分区的大致信息

```sh
[root@MyLabC7 ~]# lsblk /dev/sdb
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sdb      8:16   0   20G  0 disk 
├─sdb1 8:17   0   100M 0 part 
├─sdb2 8:18   0   100M 0 part 
└─sdb3 8:19   0   100M 0 part
```



### **9.3.4 e2label和blkid命令**

我们可以使用e2label来更改卷标，同时也可以使用该命令查看卷标

```sh
[root@MyLabC7 ~]# e2label /dev/sdb1 mydata
[root@MyLabC7 ~]# e2label /dev/sdb1 
mydata
```

另外，我们还可以使用blkid来查看分区的卷标名和分区的文件系统的类型，具体操作如下：
```sh
[root@MyLabC7 ~]# blkid /dev/sdb1
/dev/sdb1: LABEL="mydata" UUID="61ddd7a2-6ecd-420a-b037-2d7cf94a5f56" TYPE="ext4"
```



### **9.3.5 tune2fs命令**

我们可以使用tune2fs来查看或调整ext系列分区的信息

常用参数：

​	-l		查看超级块中的信息

​	-L		设定卷标

​	-m		预留管理员的空间百分比

​	-j		如果原来的文件系统为ext3，该选项能够将其提升为ext3

​	-o [^]mount-options		指定/取消默认挂载选项

​	-0  [^]feature				调整文件系统特性



**什么是超级块？**

超级块记录着文件系统的整体信息，包括inode和block的大小、总数量、已经使用的数量和剩余数量等。没有superblock，就没有当前这个文件系统。

我们可以使用`dumpe2fs -h`来查看文件系统中的超级块信息

```sh
# dumpe2fs -h
```

下面我们演示几个tune2fs的例子

1.查看超级块中的信息，可以使用-l选项。

```sh
[root@MyLabC7 ~]# tune2fs -l /dev/sdb1
tune2fs 1.42.9 (28-Dec-2013)
Filesystem volume name:   mydata
Last mounted on:          <not available>
Filesystem UUID:          61ddd7a2-6ecd-420a-b037-2d7cf94a5f56
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super huge_file uninit_bg dir_nlink extra_isize
Filesystem flags:         signed_directory_hash 
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              25688
Block count:              102400
Reserved block count:     5120
Free blocks:              93504
Free inodes:              25677
First block:              1
Block size:               1024
Fragment size:            1024
Group descriptor size:    64
Reserved GDT blocks:      256
Blocks per group:         8192
Fragments per group:      8192
Inodes per group:         1976
Inode blocks per group:   247
Filesystem created:       Fri Nov 18 17:47:28 2016
Last mount time:          Sat Nov 19 13:53:26 2016
Last write time:          Sat Nov 19 13:55:53 2016
Mount count:              1
Maximum mount count:      -1
Last checked:             Fri Nov 18 17:47:28 2016
Check interval:           0 (<none>)
Lifetime writes:          4447 kB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               128
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      66cc4a1e-5ef1-40d3-b9d0-e7b83a5dcdd8
Journal backup:           inode blocks
```

2.我们也可以使用-L来设定卷标名
```sh
[root@MyLabC7 ~]# tune2fs -L aliyun /dev/sdb2
tune2fs 1.42.9 (28-Dec-2013)
[root@MyLabC7 ~]# e2label /dev/sdb2
aliyun
```

3.如果原来的文件系统是ext2,使用-j选项可以将其提升为ext3.
```sh
[root@MyLabC7 ~]# tune2fs -j /dev/sdb3
tune2fs 1.42.9 (28-Dec-2013)
Creating journal inode: 完成
[root@MyLabC7 ~]# blkid /dev/sdb3
/dev/sdb3: UUID="87a56137-cbe6-46f3-98b9-85bf6c19bd8c" SEC_TYPE="ext2" TYPE="ext3"
```

4.使用-O选项可以开启或关闭某种文件系统的特性,比如关闭文件系统日志记录功能
```sh
[root@MyLabC7 ~]# tune2fs -O ^has_journal /dev/sdb1
tune2fs 1.42.9 (28-Dec-2013)
```
5.使用-o选项可以开启或关闭文件系统的默认挂载选项。
```sh
[root@MyLabC7 ~]# tune2fs -o acl /dev/sdb1
tune2fs 1.42.9 (28-Dec-2013)
```



### **9.3.6 e2fsck命令**

如果你使用了tune2fs改变了文件系统的特性，则需要使用e2fsck来检查。

建议检测文件系统时应该卸载文件系统再进行修复。
​	
我们现在来查看一下e2fsck的用法：

```sh
e2fsck : check a Linux ext2/ext3/ext4 file system
e2fsck [OPTIONS]  device
    -y：对所有问题自动回答为yes; 
    -f：即使文件系统处于clean状态，也要强制进行检测；
    -t：指定时间
```

如：
```sh
[root@MyLabC7 ~]# e2fsck -f /dev/sdb1
e2fsck 1.42.9 (28-Dec-2013)
第一步: 检查inode,块,和大小
第二步: 检查目录结构
第3步: 检查目录连接性
Pass 4: Checking reference counts
第5步: 检查簇概要信息
mydata: 11/25688 files (9.1% non-contiguous), 4800/102400 blocks
```
e2fsck命令只适用于ext系列文件系统，那么这里还有另外一个命令：fsck。这个命令适合所有通用的Linux文件系统。

常用参数如下：

​       	-t 文件类型 设备

​	-f 强行检测

​	-a 自动修复错误   //不建议

​	-r 交互式修复错误

```sh
[root@huyiun ~]# fsck -f -t ext3 /dev/sdb2 
fsck from util-linux 2.23.2
e2fsck 1.42.9 (28-Dec-2013)     ##感觉像是调用了e2fack命令
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
data: 11/25600 files (0.0% non-contiguous), 1841/25600 blocks
```



## **9.4 XFS文件系统管理**



### **9.4.1 XFS文件系统简介**

Centos7开始，预设的文件系统由原来的EXT4变成了XFS。使用XFS文件系统有如下原因:

​	 (1)随着磁盘容量的越来越大，连传统的MBR都已经被GPT取代，当用TB以上等级的传统ext系列文件系统在格式化的时候，预先分配inode和block就会浪费很长的时间。

​	(2)虚拟化的应用广泛，文件越来越大，而XFS支持大文件比Ext4更好
​	
xfs文件系统在资料的分布上，主要规划为三个部分:

**数据区(data section)**

和ext系列文件系统类似，也包括了inode/datablock/superblock。与ext系列不同的是，ext系列文件系统格式化时就已经分配好了inode和block。而xfs文件系统的inode和block是系统需要用到时才会动态配置产生，所以它的格式化速度非常快。

**日志区(log section)**
系统所有的动作都会记录在这个区块做记录，因此这个区块的磁盘活动相当频繁，而且在xfs的设计中，你可以指定外部的磁盘来作为xfs文件系统的日志区块。

**实时运作区(realtime section)**
当有文件要被建立时，xfs会在这个区段里面找到数个extent区块，将文件放置这个区块内，等到分配完毕后，再写入到数据区域中去。



### **9.4.2 xfs_info命令**

```sh
[root@MyLabC7 ~]# xfs_info /dev/sdb3
xfs_info: /dev/sdb3 is not a mounted XFS filesystem
[root@MyLabC7 ~]# mount /dev/sdb3 /data3
[root@MyLabC7 ~]# xfs_info /dev/sdb3
meta-data=/dev/sdb3         isize=256    agcount=4, agsize=6400 blks
         =                    sectsz=512   attr=2, projid32bit=1
         =                    crc=0        finobt=0
data     =                    bsize=4096   blocks=25600, imaxpct=25
         =                     sunit=0      swidth=0 blks
naming   =version 2           bsize=4096   ascii-ci=0 ftype=0
log      =internal            bsize=4096   blocks=853, version=2
         =                     sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                extsz=4096   blocks=0, rtextents=0
```

【说明】	    

​	isize指的是inode的大小，每个大小为256bytes。

​	agcount	指的是块组的数量，共有4个

​	agsize指的是每个块组中有多少个块

​	sectsz表示扇区的大小

​	bsize表示block的大小，每个block的大小4K

​	blocks表示blocks的数量

​	extsz表示扩展区的大小



### **9.4.3 mkfs.xfs命令**

描述：格式化分区并创建xfs文件系统

常用选项：

​	-b		指定block的大小，目前Linux最大容量限制为4K

​	-f		强制格式化

​	-L		指定卷标

示例：

```sh
    [root@MyLabC7 ~]# mkfs.xfs -f /dev/sdb3
```



### **9.4.4 xfs_repair命令**

描述：xfs文件系统的检测工具。

常用选项：

​	-n		单纯检测

​	-d		通常在单人维护模式底下用，针对/目录进行检查和修复，这个不要随便使用。

示例：

```sh
[root@MyLabC7 ~]# xfs_repair /dev/sdb3
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan and clear agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
Phase 5 - rebuild AG headers and trees...
        - reset superblock...
Phase 6 - check inode connectivity...
        - resetting contents of realtime bitmap and summary inodes
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify and correct link counts...
done
```



**9.5 swap文件系统**
---



### **9.5.1 swap文件系统介绍**

Linux中Swap（即：交换分区），类似于Windows的虚拟内存，就是当内存不足的时候，把一部分硬盘空间虚拟成内存使用,从而解决内存容量不足的情况。简单的说，交换分区，英文的说法是swap，意思是“交换”、“实物交易”。它的功能就是在内存不够的情况下，操作系统先把内存中暂时不用的数据，存到硬盘的交换空间，腾出内存来让别的程序运行，和Windows的虚拟内存的作用是一样的。

**注意的是，Linux上的交换分区必须使用独立的文件系统；且文件系统的System ID必须为82**；



### **9.5.2 查看swap的空间大小**

因为swap被将硬盘虚拟成内存来使用的，所以这个查看命令肯定与内存有关，使用`cat /proc/meminfo`可查看内存信息。
```sh
[root@MyLabC7 ~]# cat /proc/meminfo 
MemTotal:        1001332 kB
MemFree:          421248 kB
MemAvailable:     581728 kB
Buffers:            3912 kB
Cached:           256140 kB
SwapCached:            0 kB
Active:           204340 kB
Inactive:         204344 kB
Active(anon):     149440 kB
Inactive(anon):     6476 kB
Active(file):      54900 kB
Inactive(file):   197868 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:       1572860 kB
SwapFree:        1572860 kB
…………………（此处忽略）
```

当然，我们还可以使用free命令来查看内存的使用情况

free常用的参数

​	-m: 显示结果以MB为单位

​	-g: 显示结果以GB为单位 

​	-h:人类可读的

使用操作如下：
```sh
[root@MyLabC7 ~]# free -h 
     total        used        free      shared  buff/cache   available
Mem:  977M        228M        411M        7.1M      338M        568M
Swap: 1.5G          0B        1.5G
```

我们可以看到，其中了解buffer和cache的区别
```
A buffer is something that has yet to be "written" to disk.
A cache is something that has been "read" from the disk and stored for later use.
```



### **9.5.3 创建swap分区**

我们可以使用mkswap命令来创建swap分区。

mkswap常用的选项：

​	-c:建立交换分区前检查设备

​	-f:强制建立交换分区

​	-L：指定卷标

 

示例：
```sh
[root@MyLabC7 ~]# mkswap -f /dev/sdb3
正在设置交换空间版本 1，大小 = 102396 KiB
无标签，UUID=ee148b16-e336-4c42-961f-8977e12bce22
```
要注意的是：建立交换分区之前，新建分区的ID号应该是82。那我们查看一下/dev/sdb3的分区类型是什么。
```sh
[root@MyLabC7 ~]# fdisk -l /dev/sdb
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000ce43f
 
设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048      206847      102400   83  Linux
/dev/sdb2          206848      411647      102400   83  Linux
/dev/sdb3          411648      616447      102400   83  Linux
[root@MyLabC7 ~]#
```

我们可以看到，文件系统类型并不是swap，那么我们就要调整一下了：
```sh
[root@MyLabC7 ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。
     
更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。
     
     
命令(输入 m 获取帮助)：t
分区号 (1-3，默认 3)：3
Hex 代码(输入 L 列出所有代码)：82
已将分区“Linux”的类型更改为“Linux swap / Solaris”
     
命令(输入 m 获取帮助)：p
     
磁盘 /dev/sdb：21.5 GB, 21474836480 字节，41943040 个扇区
Units = 扇区 of 1 * 512 = 512 bytes
扇区大小(逻辑/物理)：512 字节 / 512 字节
I/O 大小(最小/最佳)：512 字节 / 512 字节
磁盘标签类型：dos
磁盘标识符：0x000ce43f
     
设备 Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048      206847      102400   83  Linux
/dev/sdb2          206848      411647      102400   83  Linux
/dev/sdb3          411648      616447      102400   82  Linux swap / Solaris
     
命令(输入 m 获取帮助)：w
```



### **9.5.4 开启交换分区**

其实操作很简单，直接如下操作：
```sh
[root@MyLabC7 ~]# swapon /dev/sdb3
[root@MyLabC7 ~]# swapon -s
文件名        类型       大小         已用  权限
/dev/dm-1    partition  15728600     -1
/dev/sdb3    partition   1023960     -2
```

然后我们再查看一下swap增加了没有
```sh
[root@MyLabC7 ~]# free -h
         total        used        free      shared  buff/cache   available
Mem:      977M        228M        410M        7.1M        338M        567M
Swap:     1.6G          0B        1.6G
```

我们可以发现，之前的交换分区是1.5G，现在变成了1.6G，说明增加了。



### **9.5.5 关闭交换分区**

--------------------------------------------------------------------
操作如下：
```sh
[root@MyLabC7 ~]# swapoff /dev/sdb3
[root@MyLabC7 ~]# swapon -s 
文件名           类型        大小       已用   权限
/dev/dm-1      partition   15728600      -1
```

>**注意:**一般来说，当你用到swap分区的时候，说明你的内存已经不够用了，而是要swap分区只不过之权宜之计，所以其实当内存不够的时候，请尽快添加物理内存。



## **9.6 文件系统的挂载**



### **9.6.1 什么是挂载**

在linux操作系统中， 挂载是指将一个设备（通常是存储设备）挂载到一个已存在的目录上。 我们要访问存储设备中的文件，必须将文件所在的分区挂载到一个已存在的目录上，然后通过访问这个目录来访问存储设备。

![image_1ch0p2f20rpm17v21p11vkod0i9.png-205.9kB](http://static.zybuluo.com/huiyun/rh1zd6jz9lp9cmyt5oqsd0n8/image_1ch0p2f20rpm17v21p11vkod0i9.png)

那么，被挂载的目录就被称为挂载点。挂载点就是用于作为另一个文件系统的访问入口。具有如下性质:

​	(1)挂载点得事先存在

​	(2)应该使用未被或不会被其它进程使用到的目录；

​	(3)如果挂载的目录下有文件，挂载后，该目录下原有的文件都会被隐藏。



>**思考：/分区挂载在什么地方？**
>/分区挂载在内核里面，内核一般来说只负责一个分区的挂载。一般是/所在的分区。



### **9.6.2 mount命令**

格式：`mount  [-nrw]  [-t vfstype]  [-o options]  device  dir`

命令选项：

​	-r		readonly，只读挂载； 

​	-w	        read and write, 读写挂载； 

​	-n        	默认情况下，设备挂载或卸载的操作会同步更新至/etc/mtab文件中，-n用于禁止此特性

​	-a	        自动挂载/etc/fstab文件中的挂载设备

​	-t 	       	指明要挂载的设备上的文件系统的类型；多数情况下可省略，此时mount会通过blkid来判断要挂载的设备的文件系统类型；

​	-L LABEL		挂载时以卷标的方式指明设备； 
​	-U UUID		挂载时以UUID的方式指明设备；
​	-o options	挂载时文件系统启用的特性	



然后我们再来看看-o选项中所支持的挂载特性有哪些：

​	sync/async  			同步/异步操作

​	atime/noatime  		文件或目录早被访问时是否更新其访问时间戳

​	dev/nodev       		在此设备上是否允许创建设备文件

​	exec/noexec			是否允许运行此设备上的程序文件

​	auto/noauto			是否支持开机自动挂载

​	user/nouser			是否允许普通用户挂载此文件系统

​	suid/nosuid			是否允许程序文件上的suid和sgid特殊权限生效

​	acl					文件系统的默认挂载属性

​	ro，rw				只读，读写

​	remount			重新挂载

​	defaults： rw, suid, dev, exec, auto, nouser, async, and relatime.

接下来，我们演示一下挂载特性：

**演示1：sync/async**
```sh
# mount -o sync /dev/sdb1 /data

[sync]
# time cp -r /etc/ .
real	0m3.794s
user	0m0.003s
sys	0m1.414s

[async]
# time cp -r /etc/ .
real	0m0.089s
user	0m0.002s
sys	0m0.084s
```
可以看到，我们将一个文件系统的挂载特性设置为同步，然后使用命令cp复制/etc目录到分区挂载目录，你会发现，这条命令会执行很久，因为两边正在同步数据，任何一方都不能先结束。

所谓的异步就是将数据先写在buffer中，然后再慢慢刷回到磁盘
​    
**演示2：文件或目录早被访问时是否更新其访问时间戳**

前面说过：RHEL6开始relatime，atime延迟修改，必须满足其中一个条件：

​	1.自上次atime修改后，已达到86400秒(一天)

​	2.发生写操作时 

示例：
```sh
[root@node4 data]# stat file1
  File: ‘file1’
  Size: 5         	Blocks: 8          IO Block: 4096   regular file
Device: 811h/2065d	Inode: 439         Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:unlabeled_t:s0
Access: 2018-06-28 07:34:10.156669773 +0800
Modify: 2018-06-28 07:34:01.688668945 +0800
Change: 2018-06-28 07:34:01.688668945 +0800
 Birth: -
[root@node4 data]# cat file1
123
[root@node4 data]# stat file1
  File: ‘file1’
  Size: 5         	Blocks: 8          IO Block: 4096   regular file
Device: 811h/2065d	Inode: 439         Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:unlabeled_t:s0
Access: 2018-06-28 07:34:10.156669773 +0800
Modify: 2018-06-28 07:34:01.688668945 +0800
Change: 2018-06-28 07:34:01.688668945 +0800
 Birth: -
```
可以发现，文件的访问时间并没有改变，这有利于减少磁盘I/O。因为文件的元数据也存储在磁盘上，所以如果某个文件的访问量很大，那么每一次访问都要更新atime，那么将会产生大量的磁盘I/O。

**演示3：exec和noexec**
```sh
# mount -o remount,noexec /data/
# cd /data/
# cp /bin/ls .
# ./ls
-bash: ./ls: Permission denied
```
可以发现，在该设备文件上，是不允许执行程序的

**演示4：**

1.关闭acl的功能
```sh
[root@MyLabC7 ~]# mount -o noacl /dev/sdb2 /data2
[root@MyLabC7 ~]# cd /data2
[root@MyLabC7 data2]# cp /etc/fstab .
[root@MyLabC7 data2]# setfacl -m u:user1:rwx fstab
setfacl: fstab: 不支持的操作
```

2.如果我们想获得acl的操作，可以重新挂载
```sh
[root@MyLabC7 data2]# mount -o acl,remount /dev/sdb2 /data2
[root@MyLabC7 data2]# setfacl -m u:user1:rw fstab 
[root@MyLabC7 data2]# getfacl fstab
# file: fstab
# owner: root
# group: root
user::rw-
user:user1:rw-
group::r--
mask::rw-
other::r—
```

3.可以实现将目录绑定至另一个目录上，作为其临时访问入口；
```sh
[root@MyLabC7 data2]# mount --bind /tmp /mnt
```



### **9.6.3 查看已经挂载的设备**

一般来说，我们可以通过以下几个文件来查看当前系统已经挂在的设备。

**/proc/mounts**

通过Linux专有的虚拟文件/proc/mounts，可以查看当前已挂载文件系统的列表。

/proc/mounts是内核数据结构的接口，因此总是包含已挂载文件系统的精确消息。
​    
我们使用cat /proc/mounts查看
```sh
[root@MyLabC7 data2]# cat /proc/mounts
rootfs / rootfs rw 0 0
……………（此次省略）
rw,seclabel,nosuid,nodev,relatime,size=100136k,mode=700,uid=42,gid=42 0 0
tmpfs /run/user/0 tmpfs rw,seclabel,nosuid,nodev,relatime,size=100136k,mode=700 0 0
/dev/sdb2 /data2 ext4 rw,seclabel,relatime,data=ordered 0 0
```

**/etc/matb**

Mount命令会自动维护/etc/matb文件，该文件中包含的内容与/proc/mounts的内容相似，只是略微详细了点。

另外，我们也可以通过mount直接查看，当然，这是因为mount调用了/etc/mtab文件。



### **9.6.4 挂载光盘**

1.将光盘制作成iso
```sh
# dd </dev/cdrom >/centos.iso
```

2.将文件制作成iso，例如将etc制作成iso
```sh
# genisoimage –o /tmp/etc.iso -r /etc
# file /tmp/etc.iso
```

3.挂载回环设备
```sh
# mount -o loop /tmp/etc.iso /mnt/iso/
```



### **9.6.5 文件系统的卸载**

一般来说，我们只要在文件系统空闲的时候才能卸载文件系统。所以卸载文件系统有如下步骤：
​    
1.查看占用挂载设备的进程
```sh
# fuser -v /data/
                     USER        PID ACCESS COMMAND
/data:               root     kernel mount /data
```

2.关闭所有占用挂载设备的进程
```sh
# fuser  -km  MOUNT_POINT
```

3.直接卸载目录即可
```sh
# umount device|mount_point
```



### **9.6.6 自动挂载文件系统**

设定除根文件系统以外的其它文件系统能够开机时自动挂载，那么就要记录到/etc/fstab文件中。
​	
我们可以使用cat来查看一下该文件中的内容：
```sh
[root@MyLabC7 data2]# cat /etc/fstab
#
# /etc/fstab
# Created by anaconda on Sun Sep 25 18:05:14 2016
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /           xfs     defaults        0 0
UUID=44a5-b87d-119d8e2277b3 /bootxfs     defaults        0 0
/dev/mapper/centos-home /home       xfs     defaults        0 0
/dev/mapper/centos-swap swap        swap    defaults        0 0
```
我们可以看到，该文件分为6个字段：

```sh
第1个字段：要挂载的设备名，设备文件,LABEL=，UUID=
第2个字段：设备的挂载点，要注意的是，swap的挂载点就是swap
第3个字段：文件系统类型
第4个字段：挂载选项
	defaults：使用默认挂载选项；
	如果要同时指明多个挂载选项，彼此间以事情分隔；
	如：defaults,acl,noatime,noexec
第5个字段：转储频率
	0：从不备份；
	1：每天备份；
	2：每隔一天备份；
第6个字段：自检次序
	0：不自检；
	1：首先自检，通常只能是根文件系统可用1；
	2：次级自检
```

把文件系统编辑到/etc/fstab下之后，使用：
```sh
# mount –a
```



### **9.6.7 df和du命令**

**df命令**

描述：用来查看文件系统的磁盘使用情况

用法：`df  [OPTION]... [FILE]...`

常用选项：

​	-l		仅显示本地文件的相关信息

​	-h		人性化的可读

​	-i		显示inode的使用情况 

​	-P		使用POSIX标准输出	



**du命令**

描述：查看目录的使用情况

用法：`du  [OPTION]... [FILE]...`

常用选项：

​	-s		只显示目录所使用的总容量

​	-h		人性化的可读	

要注意的是，我们使用`ls -ld /etc`所查看的目录大小是该目录本身所占的block的大小，如果要查看它的容量，可使用`du -sh`。
```sh
[root@MyLabC7 data2]# ls -lhd /etc
drwxr-xr-x. 131 root root 8.0K 11月 19 13:39 /etc
[root@MyLabC7 data2]# du -sh /etc
26M   /etc
```



## **9.7 链接文件**

在Linux系统中，链接可以分为两种：**硬链接（hard link）**和**软链接(symbolic link)**。

我们可以使用ln命令来创建链接文件。在使用ln命令时，不加任何参数选项就是创建硬链接文件，如：
```sh
[root@MyLabC7 ~]# ls -l test 
-r--rw----+ 1 root root 83 11月 20 16:02 test
[root@MyLabC7 ~]# ln test test.hard
[root@MyLabC7 ~]# ls -l test
-r--rw----+ 2 root root 83 11月 20 16:02 test
```

那么创建软链接文件要使用ln -s选项参数，操作如下：
```sh
[root@MyLabC7 ~]# cat test.hard
THIS is new line
THIS is oTher new line THIS is my firsT TesT
dsadsad:
THIS is dog
[root@MyLabC7 ~]# ln -s test test.symbolic
[root@MyLabC7 ~]# cat test.symbolic 
THIS is new line
THIS is oTher new line THIS is my firsT TesT
dsadsad:
THIS is dog
[root@MyLabC7 ~]# cat test
THIS is new line
THIS is oTher new line THIS is my firsT TesT
dsadsad:
THIS is dog
```
我们可以发现，链接文件里面的内容和源文件是一模一样的，那么硬链接和软连接是什么？它们的区别又在哪里？现在，我们来分别介绍它们。



### **9.7.1 硬链接**

在讲述硬链接之前，我们先做一个小实验，我们使用ls –li来查看一下硬链接和源文件的inode。
```sh
[root@MyLabC7 ~]# ls -li test test.hard 
54544293 -r--rw----+ 2 root root 83 11月 20 16:02 test
54544293 -r--rw----+ 2 root root 83 11月 20 16:02 test.hard
```

我们可以发现，这两个文件的inode竟然一模一样，若是你还是怀疑，可以再创建一个硬链接文件。
```sh
[root@MyLabC7 ~]# ls -il test*
54544293 -r--rw----+ 3 root root 83 11月 20 16:02 test
54544293 -r--rw----+ 3 root root 83 11月 20 16:02 test.hard
54544293 -r--rw----+ 3 root root 83 11月 20 16:02 test.hard2
54544290 lrwxrwxrwx. 1 root root  4 11月 20 16:10 test.symbolic -> test
```

毋庸置疑，还是一模一样，那么这到底怎么回事呢？
​	
硬链接是指通过索引节点（inode）来进行链接。在Linux文件系统中，多个文件指向同一个索引节点（inode）是正常的且被允许的。那么这种情况下的文件就称为硬链接。

硬链接的作用之一是允许一个文件拥有多个有效路径名（多个入口），这样用户就可以建立硬链接到重要的文件，以防止“误删”源数据（很多硬件存储，如netapp存储中的快照功能就应用了这个原理，增加一个快照就多了一个硬链接）。那么为什么一个文件建立了硬链接就会防止数据误删呢？
​	
我们先来看看：首先我们把源文件删除了。
```sh
[root@MyLabC7 ~]# rm -rf test
[root@MyLabC7 ~]# cat test.hard
THIS is new line
THIS is oTher new line THIS is my firsT TesT
dsadsad:
THIS is dog
```

我们可以发现，即使我们把源文件删了，硬链接文件还是可以访问源文件中的数据内容。
​	
**原理**：只要文件的索引节点还有一个以上的链接。那么删除其中一个链接并不影响inode本身和其它的链接，即数据的文件实体并未删除，只有当文件的最后一个链接被删除后，此时如果有新数据要存储到硬盘上时或者系统通过类似fsck做磁盘检查的时候。被删除的文件的数据块及目录的链接才会被释放，空间被新数据占用并覆盖。此时，数据就再也无法找回了。

我们可以看到下面这张图：
![image_1ch0sfcaj1th01rfcjcc1msto6am.png-97.1kB](http://static.zybuluo.com/huiyun/mri809jmqspsk0od7z88aqk4/image_1ch0sfcaj1th01rfcjcc1msto6am.png)

这个图中有三个路径指向同一个inode，即删除任何一个都不会误删源数据，只有当3个指向全被删除的时候，这时候源数据才会被删除。
​	
上述的情况适合于没有任何进程访问的文件，那么对于有进程访问的文件，你删除了3个指向并不一定会把文件彻底删除，这个我们到后再详细介绍。

**小结**：
​	1.硬链接文件是具有相同inode的不同文件

​	2.删除硬链接文件或者删除源文件之一，文件实体并未删除

​	3.只有删除了源文件及所有对应的硬链接文件，文件实体才会被删除

​	4.当所有的硬链接文件及源文件被删除后，再存放新数据会占用这个文件的空间，或者磁盘fsck检查的时候，数据也会被收回。

​	5.硬链接文件是文件的另一个入口

​	6.可以通过给文件设置硬链接来防止重要的文件被误删。



### **9.7.2 软链接**

软链接也称为**符号链接（symbolic Link）**。软链接文件就类似于windows系统中的快捷方式。

在软链接中，它实际上就是一个文本，这个文件它记录着指向另一个文件的位置信息。

```sh
[root@MyLabC7 ~]# ls -l test1*
-r--rw----+ 1 root root 12 11月 20 17:47 test1
lrwxrwxrwx. 1 root root  5 11月 20 17:48 test1.symbolic -> test1
```

我们可以发现，文件大小的那个字段显示的是5，而这个5就是该链接文件所指向的源文件所占的字符数。但是如果你把上述这个软链接文件移到其它目录中去，就无法执行了，因为我们给的是源文件的相对路径。所以如果你想使其移动到其它目录中也能使用，那么最好给出源文件的绝对路径。
​	
下面，我们通过一张图来加深理解：

![image_1ch0shofc18kkthcggg1or21m5l13.png-59.5kB](http://static.zybuluo.com/huiyun/93sr9l7mdjw48iabkhnu0sea/image_1ch0shofc18kkthcggg1or21m5l13.png)

如上图，我们可以发现，软链接文件一般情况下并没有占据block块，但是我们ls -li发现，它也有inode，它的inode中存放的是一串字符串，该字符串就是源文件中的位置，你可以是相当路径的位置，也可以是绝对路径的位置。



如果我们想查看一下软连接中的值，可以使用readlink这个命令。
```sh
[root@MyLabC7 ~]# readlink test2.symbolic 
/root/test1
```

如果源文件的绝对路径名被改变了，那么软链接的指向就失效了。



### **9.7.3 使用lsof恢复文件**

首先，我们要理解一下linux文件系统的删除原理：

对于一个没有进程访问的文件来说，那么Linux是通过硬链接的数量来控制文件删除的，即一般来说，要删除这个文件，只要使用rm -rf来逐一删除源文件和源文件的硬链接文件，从而使得硬链接数为0，这样就表示该文件已删除。

但是，对于一个有进程在访问的文件来说，那么Linux是通过硬链接的数量和进程的数量来控制文件删除的。大致原理如下图：
![image_1ch0sjbum1f0besn64u1m281r121g.png-81.8kB](http://static.zybuluo.com/huiyun/a8mj2lrkw6dv2ezle47kn4jo/image_1ch0sjbum1f0besn64u1m281r121g.png)


我们把硬链接的数量记为i_link，把访问该文件的进程的数量记为i_count，那么要想真正的删除这个文件，就需要使得i_link=0，i_count=0。

因此，借助这个特性，我们也可以恢复一些被我们误删的文件。

**接下来，我们就来模拟这个案例：**

1.安装http服务

```sh
[root@MyLabC7 ~]# yum install httpd –y
```
注意，如果你还没有学习到防火墙和Selinux的知识，请保证防火墙和Selinux是关闭的。	

关闭防火墙：

```sh
[root@MyLabC7 ~]# systemctl stop firewalld.service
```

关闭selinux:
```sh
[root@MyLabC7 ~]# vim /etc/selinux/config
SELINUX=disable
[root@MyLabC7 ~]# setenforce 0
```

2.调整apache的配置文件，让日志记录在/app/log下

```sh
[root@MyLabC7 conf]# grep app httpd.conf
CustomLog "/app/log/access_log" combined
```

3.创建/app/log目录
```sh
[root@MyLabC7 conf]# mkdir -pv /app/log
mkdir: created directory ‘/app’
mkdir: created directory ‘/app/log’
```

4.创建一个小型的文件系统，通过dd命令来模拟
```sh
[root@MyLabC7 conf]# dd if=/dev/zero of=/dev/sdc bs=8K  count=10
10+0 records in
10+0 records out
81920 bytes (82 kB) copied, 0.000415691 s, 197 MB/s
这相当于创建一个小磁盘
```

5.创建完成之后，我们使用查看下是否有sdc这个文件
```sh
[root@MyLabC7 conf]# ls -l /dev/sdc
-rw-r--r--. 1 root root 81920 Nov 21 18:02 /dev/sdc
```

6.格式化文件系统，我们这里使用ext3。
```sh
[root@MyLabC7 conf]# mkfs -t ext3 /dev/sdc
```

7.挂载
```sh
[root@MyLabC7 conf]# mount -o loop /dev/sdc /app/log/
[root@MyLabC7 conf]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   10G  4.2G  5.9G  42% /
/dev/mapper/centos-home  8.0G   33M  8.0G   1% /home
/dev/sda1                497M  157M  341M  32% /boot
tmpfs                     98M   48K   98M   1% /run/user/0
/dev/sr0                 7.3G  7.3G     0 100% /mnt
/dev/loop0                73K   14K   55K  21% /app/log
```

8.重启httpd服务
```sh
[root@MyLabC7 conf]# systemctl restart httpd.service
```

9.现在我们访问本机的IP地址

然后我们再查看/app/log下有了一个这样的文件：

```sh
[root@MyLabC7 conf]# ls -l /app/log/
total 17
-rw-r--r--. 1 root root  3636 Nov 21 18:14 access_log
drwx------. 2 root root 12288 Nov 21 18:06 lost+found
```

前面我们也说了，看日志用tail -f查看。


我们不断的刷新，发现日志文件正在动态的记录。
​	
然后我们使用df –h查看，发现磁盘空间逐渐被写满了，好的，这就模拟出来磁盘空间被写满的情况。
```sh
[root@MyLabC7 conf]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   10G  4.2G  5.9G  42% /
devtmpfs                 475M   80K  474M   1% /dev
tmpfs                    489M   80K  489M   1% /dev/shm
tmpfs                    489M  7.1M  482M   2% /run
tmpfs                    489M     0  489M   0% /sys/fs/cgroup
/dev/mapper/centos-home  8.0G   33M  8.0G   1% /home
/dev/sda1                497M  157M  341M  32% /boot
tmpfs                     98M   48K   98M   1% /run/user/0
/dev/sr0                 7.3G  7.3G     0 100% /mnt
/dev/loop0                73K   69K     0 100% /app/log
```

当我们发现记录日志的分区满了之后，我们立马就想到要清空里面的日志文件，于是，我们就使用了如下命令：
```sh
[root@MyLabC7 conf]# cd /app/log/
[root@MyLabC7 log]# rm -rf access_log 
[root@MyLabC7 log]# ls -l
total 12
drwx------. 2 root root 12288 Nov 21 18:06 lost+found
```

然后查看磁盘空间有没有被释放？
```sh
[root@MyLabC7 log]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   10G  4.2G  5.9G  42% /
devtmpfs                 475M   80K  474M   1% /dev
tmpfs                    489M   80K  489M   1% /dev/shm
tmpfs                    489M  7.1M  482M   2% /run
tmpfs                    489M     0  489M   0% /sys/fs/cgroup
/dev/mapper/centos-home  8.0G   33M  8.0G   1% /home
/dev/sda1                497M  157M  341M  32% /boot
tmpfs                     98M   48K   98M   1% /run/user/0
/dev/sr0                 7.3G  7.3G     0 100% /mnt
/dev/loop0                73K   69K     0 100% /app/log
```

我们发现，磁盘并没有被释放，这是因为虽然文件的硬链接数被删掉了，但是apache进程还在往里面写数据。
```sh
[root@MyLabC7 logs]# lsof | grep "access.log"
httpd     2784         root    7w      REG                7,0     72634         12 /app/logs/access_log (deleted)
httpd     2785       apache    7w      REG                7,0     72634         12 /app/logs/access_log (deleted)
.............................         
```
我们可以看到，虽然文件已经被删除了，但是进程还没有被释放。所以磁盘空间还是个满的。
​	
此时，只要你重启服务，该进程就会释放掉了，这个文件就彻底被删除了。但是我们在删除这个文件的时候，忘记备份了，所以如果想备份，那么就要借助文件描述符来进行恢复文件了。

```sh
[root@MyLabC7 logs]# ls -l /proc/2784/fd/*
....................
l-wx------. 1 root root 64 Jan  5 21:54 /proc/2784/fd/7 -> /app/logs/access_log (deleted)
```

于是我们可以这样做：
```sh
[root@MyLabC7 logs]# cp /proc/2784/fd/7 /tmp/access.log
```

另外，如果你真的碰到日志文件被写满了，可以先做好日志文件的备份，然后再执行下面的操作：
```sh
[root@MyLabC7 log]# >/app/log/access_log
```



# **第10章 RAID与LVM技术**



## **10.1 RAID技术**



### **10.1.1 RAID概念**

**磁盘阵列**（Redundant Arrays of Independent Disks，RAID），有"独立磁盘构成的具有冗余能力的阵列"之意。

Raid是一种把多块独立的磁盘按不同的方式结合起来形成一个硬盘组，在逻辑上看起来就是一块大的硬盘，从而提供比单个物理硬盘更大的存储容量或更高的存储性能，同时又能提供不同级别数据冗余备份的一种技术



### **10.1.2 常见的RAID级别**

| 级别       | 描述               | 最低磁盘个数 | 空间利用率 | 读写性能                     |
| ---------- | ------------------ | ------------ | ---------- | ---------------------------- |
| **RAID0**  | 条带卷             | 2+           | 100%       | 读写性能快，不容错           |
| **RAID1**  | 镜像卷             | 2            | 50%        | 读写性能一般，容错           |
| **RAID5**  | 带奇偶校验的条带卷 | 3+           | (n-1)/n    | 读速度快，容错，允许坏一块盘 |
| **RAID6**  | 双校验的条带卷     | 4+           | (n-2)/n    | 读速度快，容错，允许坏两块盘 |
| **RAID10** |                    | 4            | 50%        | 读写速度快，容错             |
| **RAID50** |                    | 6            | (n-2)/n    | 读写速度快，容错             |



### **10.1.3 RAID-0**

`RAID-0`又称为Stripe(条带)或Striping(条带模式),它在所有RAID级别中具有最高的存储性能。

需要至少2块磁盘(大小最好相同)。

**特点**：成本低，可以提高整个磁盘的性能和吞吐量，利用率为100%

**缺点**：没有容错能力，损坏一块盘，数据将丢失

![image_1chg9fnd31htf1d3g19bsuqp1ihi16.png-88.8kB](http://static.zybuluo.com/huiyun/4izzkr1ndv4z9zpe2981iw0x/image_1chg9fnd31htf1d3g19bsuqp1ihi16.png)



### **10.1.4 RAID-1**

`RAID 1`又称为Mirror或Mirroring（镜像）,它的宗旨是最大限度的保障用户数据的可用性和可修复性。

`RAID 1`的操作方式是把用户写入一个磁盘的数据百分之百地自动复制到临海一个盘上，从而实现存储双份的数据

需要至少2块磁盘(大小最好相同)

特点：读性能可能上升(两块盘一起读)，写性能下降，磁盘利用率只有50%，具有容错能力

![image_1chg9lt7g1emm1pdrkhe8no1r9c20.png-64.5kB](http://static.zybuluo.com/huiyun/8wkhh53jvcwswjx97w9zny06/image_1chg9lt7g1emm1pdrkhe8no1r9c20.png)



### **10.1.5 RAID-5**

`RAID5` 是一种存储性能、数据安全和存储成本兼顾的存储解决方案。

`RAID5`需要三块或以上的物理磁盘，可以提供热备盘实现故障恢复；采用奇偶校验，可靠性强，且只有同时损坏两块硬盘时数据才会完全损坏，只损坏一块盘时，系统会根据存储的奇偶校验位重建数据，临时提供服务；此时如果有热备盘，系统还会自动在热备盘上重建故障磁盘上的数据；

`RAID5`可以理解为是`RAID0`和`RAID1`的折中方案。`RAID5`可以为系统提供数据安全保障，但保障程度要比`RAID1`低而磁盘空间利用率要比`RAID1`高。`RAID5`具有和`RAID0`相似的数据读取速率，只是多了个奇偶校验信息，写入数据的速度比对单个磁盘进行写入操作稍慢。同时由于多个数据对应一个奇偶校验信息，`RAID5`的磁盘空间利用率要比`RAID1`高，存储成本相对较低。

**特点：**读性能提升，写性能下降，具有容错能力，损失一块盘的容量，磁盘利用率为(n-1)/n

![image_1chg9oemue1t1ro31mpf14311ivq2q.png-78kB](http://static.zybuluo.com/huiyun/st3v3deemk0fl91i46k5in9o/image_1chg9oemue1t1ro31mpf14311ivq2q.png)



### **10.1.6 RAID-10**

RAID10的理解大概是这样： 至少需要四块磁盘，先做RAID1再做RAID0，大概用法如下图： 

![image_1chg9s6ib3k386lnc3ju01nhp3k.png-241.9kB](http://static.zybuluo.com/huiyun/9745zfaz5i8pubr78gupr1h1/image_1chg9s6ib3k386lnc3ju01nhp3k.png)

以4块磁盘为例，该图是将2块磁盘为一组先做RAID1，然后再将两个RAID1做成RAID0。在这种情况下，如果只是坏掉其中一个硬盘，对RAID组的影响都不是非常大，只要不是同时坏掉其中的一个硬盘和它的镜像盘，RAID组都不会崩溃。

**特点：**读写性能提升，具有容错能力，损失一块盘的容量，磁盘利用率为50%



## **10.2 LVM技术**



### **10.2.1 LVM的简介**

**LVM**全称（Logic volume Management）逻辑卷管理，它的最大用途是可以灵活的管理磁盘的容量，让磁盘分区可以随意放大或缩小，便于更好的应用磁盘的剩余空间。

LVM可以整合多个块设备在一起，让这些块设备看起来就像一个磁盘一样。这些块设备可以是磁盘，分区，RAID设备中的任何一种，Centos7中预设的分区形式就是LVM。



### **10.2.2 LVM的详细原理**

LVM是Linux环境下对磁盘分区进行管理的一种机制，它将多个物理设备(磁盘、分区、raid)汇聚成一个**卷组（Volume Group）**，简称VG。组成的卷组就像一块大硬盘，然后再从中分割出一块块逻辑卷，并进一步在逻辑卷上创建文件系统。

![1547262703202](C:\Users\wali\AppData\Roaming\Typora\typora-user-images\1547262703202.png)

**物理卷（PV）**

是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有与LVM相关的管理参数，可以使用分区、磁盘或raid组成。

如果要形成LVM,需要使用fdisk命令去调整系统标致符为8e,然后再经过pvcreate这个命令将他转换成PV，这样才可以利用。

**卷组（VG）**

由一个或多个PV组成，这个相当于非LVM上的磁盘。

VG的最小存储块：PE

**逻辑卷（LV）**

如果说VG类似于非LVM上的磁盘，那么LV就类似于在磁盘上分区，LV建立在VG之上，在LV上可以创建文件系统。

LV的最小存储块：LE（在同一个卷组中，PE和LE是相同的，并且一一对应）



### **10.2.3 LVM的设备名称**

对于LVM的设备文件，命名规则如下：

​	`/dev/VG_NAME/LV_NAME`或

​	`/dev/mapper/VG_NAME-LV_NAME`

​		VG_NAME：卷组的名称

​		LV_NAME：逻辑卷名称

其实无论是哪一个规则，它们都只是一个映射，系统真正识别这个设备文件是：`/dev/dm-#`

示例：

```sh
[root@MyLabC7 ~]# ls /dev/mapper/*
/dev/mapper/centos-home  /dev/mapper/centos-swap
/dev/mapper/centos-root  /dev/mapper/control
[root@MyLabC7 ~]# ls /dev/centos/*
/dev/centos/home  /dev/centos/root  /dev/centos/swap
[root@MyLabC7 ~]# ls -l /dev/centos/*
lrwxrwxrwx. 1 root root 7 Nov 30  2016 /dev/centos/home -> ../dm-2
lrwxrwxrwx. 1 root root 7 Nov 30  2016 /dev/centos/root -> ../dm-0
lrwxrwxrwx. 1 root root 7 Nov 30  2016 /dev/centos/swap -> ../dm-1
```



### **10.2.4 PV管理工具**

常用的PV管理工具：

​	`pvcreate`：创建物理卷

​	`pvdisplay`：显示物理卷的信息

​	`pvs`：显示物理卷的简要信息

​	`pvremove`：删除物理卷



示例：

1.事先准备好两个分区，sdb1，sdb2，sdc（故意加个磁盘设备）

```sh
# pvcreate /dev/sdb{1,2} /dev/sdc
```

2.查看物理卷的简要信息

```sh
# pvs
PV         VG     Fmt  Attr PSize   PFree 
/dev/sda2  centos lvm2 a--  119.51g  4.00m
/dev/sdb1         lvm2 ---   20.00g 20.00g
/dev/sdb2         lvm2 ---   20.00g 20.00g
/dev/sdc          lvm2 ---   20.00g 20.00g
```

3.查看某一个物理卷的详细信息

```sh
# pvdisplay /dev/sdc
"/dev/sdc" is a new physical volume of "20.00 GiB"
--- NEW Physical volume ---
PV Name               /dev/sdc        #物理卷名称
VG Name               
PV Size               20.00 GiB       #物理卷的大小
Allocatable           NO
PE Size               0   
Total PE              0
Free PE               0
Allocated PE          0
PV UUID               DK5Bio-hlqm-NyXB-u4wO-svxc-DPD0-yBUXez
```



### **10.2.5 VG管理工具**

常用的VG管理命令：

​	`vgcreate`：创建卷组

​	`vgs`：显示卷组的简要信息

​	`vgdisplay`：显示卷组的详细信息

​	`vgextend`：扩展卷组大小

​	`vgreduce`：缩减卷组大小

​	`vgremove`：删除卷组



示例：
1.将创建好的PV加入到这个VG中来，注意，在创建VG的时候，需要给它起个名字。

```sh
[root@MyLabC7 ~]# vgcreate mydata /dev/sdb{1,2}
Volume group "mydata" successfully created
```

2.在创建完VG之后，可以使用vgdisplay或者vgs来查看vg的信息
```sh
[root@MyLabC7 ~]# vgs
VG     #PV #LV #SN Attr   VSize   VFree 
centos   1   5   0 wz--n- 119.51g  4.00m
mydata   2   0   0 wz--n-  39.99g 39.99g

[root@MyLabC7 ~]# vgdisplay mydata
   --- Volume group ---
   VG Name               mydata    #VG的名称
   System ID             
   Format                lvm2
   Metadata Areas        2
   Metadata Sequence No  1
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                0
   Open LV               0
   Max PV                0
   Cur PV                2
   Act PV                2
   VG Size               39.99 GiB
   PE Size               4.00 MiB
   Total PE              10238
   Alloc PE / Size       0 / 0   
   Free  PE / Size       10238 / 39.99 GiB
   VG UUID               TPu9xJ-nEFQ-eHr7-O2sS-QQWb-k3PU-vyhmGV
```

3.如果想加一块硬盘进来，即扩展VG的容量，可以使用vgextend
```sh
[root@MyLabC7 ~]# vgextend mydata /dev/sdc
Volume group "mydata" successfully extended
```

4.缩减VG的容量，我们可以使用vgreduce来缩减磁盘的容量，不过前提是缩减的那块磁盘没有数据，所以为了避免数据的不完整性，在使用vgreduce之前，请使用pvmove将PV里的数据移到别处去。
```sh
[root@MyLabC7 ~]# vgreduce mydata /dev/sdc
Removed "/dev/sdc" from volume group "mydata"
```



### **10.2.6 LV管理工具**

常用的LV管理命令：

​	`lvcreate`：创建逻辑卷

​	`lvs`：显示逻辑卷的简要信息

​	`lvdisplay`：显示逻辑卷的详细信息

​	`lvextend`：扩展逻辑卷

​	`lvreduce`：缩减逻辑卷

​	`lvremove`：删除逻辑卷

示例：
1.创建一个逻辑卷（注意：要基于VG创建逻辑卷）

用法：`lvcreate [options]  VG_NAME`

常用选项：

​	-n		指定卷标名

​	-L		指定卷标大小

```sh
[root@MyLabC7 ~]# lvcreate -n mydata_1 -L 500M mydata
```

2.查看逻辑卷

```sh
[root@MyLabC7 ~]# lvdisplay mydata
--- Logical volume ---
LV Path                /dev/mydata/mydata_1
LV Name                mydata_1
VG Name                mydata
LV UUID                3xiciU-XSGC-KkWF-tY12-xfcL-BF0F-H3olRu
LV Write Access        read/write
LV Creation host, time MyLabC7, 2018-01-06 04:27:54 +0800
LV Status              available
# open                 0
LV Size                500.00 MiB
Current LE             125
Segments               1
Allocation             inherit
Read ahead sectors     auto
- currently set to     8192
Block device           253:5
```

3.创建完逻辑卷后，就可以格式化挂载了
```sh
[root@MyLabC7 ~]# mke2fs -t ext4 /dev/mydata/mydata_1
[root@MyLabC7 ~]# mkdir /mydata_data1
[root@MyLabC7 ~]# mount /dev/mapper/mydata-mydata_1 /mydata_data1
```





### **10.2.7 扩展逻辑卷**

1、逻辑卷的扩展操作可以在线进行，不需要卸载逻辑卷。在扩展逻辑卷之前，我们要查看当前VG的容量，保证VG中有足够的空闲信息。
```sh
[root@MyLabC7 ~]# vgs
VG     #PV #LV #SN Attr   VSize   VFree 
centos   1   5   0 wz--n- 119.51g  4.00m
mydata   2   1   0 wz--n-  39.99g 39.50g
```
我们可以发现，在VG中还有空闲的空间，现在我们就可以动态的对逻辑卷进行扩展了。
​	
2、比如现在我要把mydata_1逻辑卷扩展1G的大小，此时我们就可以使用lvextend。
```sh
[root@MyLabC7 ~]# lvextend -L +1G /dev/mydata/mydata_1 
Size of logical volume mydata/mydata_1 changed from 500.00 MiB (125 extents) to 1.49 GiB (381 extents).
Logical volume mydata_1 successfully resized.
```
我们发现扩展以后逻辑卷大小变成了1.5G，我们之前已经把逻辑卷挂载使用了，在扩充之前，并没有卸载该卷。同时如果你的逻辑卷里如果还有文件的话，你会发现文件也没有损坏。
```sh
[root@MyLabC7 ~]# lvs mydata
LV VG Attr LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
mydata_1 mydata -wi-ao---- 1.49g
```

但是我们使用df -h查看的时候，发现逻辑卷还是以前的500M
```sh
[root@MyLabC7 ~]# df -h /dev/mydata/mydata_1 
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydata-mydata_1  477M  2.3M  445M   1% /mydata_data1
```

这个原因就是文件系统是在创建完LV以后马上进行格式化的，此后扩展逻辑卷并不会改变当前文件系统，所以这时必须更新文件系统，使用resize2fs。
```sh
[root@MyLabC7 ~]# resize2fs /dev/mydata/mydata_1
[root@MyLabC7 ~]# df -h /dev/mydata/mydata_1 
Filesystem                   Size  Used Avail Use% Mounted on
/dev/mapper/mydata-mydata_1  1.5G  2.7M  1.4G   1% /mydata_data1
```

注意：如果是xfs,那么就要使用xfs_growfs来进行更新。



### **10.2.8 缩减逻辑卷**

在实际情况中，虽然几乎都用不到缩减的情况，但是我们还是要了解一下：扩展逻辑卷时可以在线执行，但是缩减逻辑卷需要离线执行，否则可能造成逻辑卷里的文件损坏。现在我们来看一下具体步骤：

1、卸载已经挂载的逻辑卷
```sh
[root@MyLabC7 ~]# umount /mydata_data1/
```

2、使用e2fsck检查磁盘
```sh
[root@MyLabC7 ~]# e2fsck -f /dev/mydata/mydata_1
```

3、缩减文件系统

```sh
[root@MyLabC7 ~]# resize2fs /dev/mydata/mydata_1 500M
```

4、缩减逻辑卷

```sh
[root@MyLabC7 ~]# lvreduce -L -1G /dev/mydata/mydata_1
```

**注意**：缩小逻辑卷是一个危险的操作，所以一般不要对逻辑卷进行缩小，并且在xfs上不支持逻辑卷的缩小。
​	
而且这里最后再强调一下步骤：

**先卸载逻辑卷，再缩小文件系统，最后再缩小逻辑卷。**



### **10.2.9 LVM的快照功能**

快照能把**数据在某一时刻的映像保留下来**。因此我们可以根据快照查找数据在**过去某一时刻**的镜像， 快照常常用来作为增强数据备份系统的一种技术。

**LVM快照的工作原理**

我们可以看到下图，通过该图我们可以知道：

- 快照的作用开始和硬链接一样，都是指向文件的元数据，也就是说，快照可以看成是文件的另一个访问入口，我们可以通过快照来访问系统卷中的数据。
- 当系统卷中有数据发生变化的时候，会在变化之前，将该变化的数据保存在快照卷中，这时候通过快照卷访问的数据是系统原来的数据，而通过系统卷访问的数据是已经变化之后的数据。
- 快照卷的大小不一定要和系统卷的大小一样，它的大小取决于数据变化大小的范围。所以在使用快照卷之前，我们可以事先对数据的变化进行一次评估，如利用快照备份数据库数据的时候，计算2小时之内数据的变化量是多少，然后再决定快照卷的大小。
- 创建快照卷的时候，必须和系统卷属于同一个卷组。
- 快照卷不是永久的，如果卸下LVM或重启，它们就丢失了，需要重新创建。

![image_1chgcu8b16e31a6215jh1vr817k09.png-101.5kB](http://static.zybuluo.com/huiyun/wqh2v75cecqo47z1y0kmrgri/image_1chgcu8b16e31a6215jh1vr817k09.png)



**创建快照卷**

格式：`lvcreate -s [options] LV_NAME`

常用选项：

​	-s		创建快照卷

​	-L		指明快照卷的大小

​	-n		为快照卷起一个别名

​	-p		指定快照卷的权限，一般为只读

1.在挂载目录mydata_data1中增加一些数据
```sh
[root@MyLabC7 ~]# cd /mydata_data1/
[root@MyLabC7 mydata_data1]# cp /etc/fstab  .
```

2.创建快照卷
```sh
# lvcreate -s -L 100M -n mydata_snap /dev/mydata/mydata_1 
Logical volume "mydata_snap" created.
```

3.挂载快照卷
```sh
[root@MyLabC7 ~]# mkdir /snap
[root@MyLabC7 ~]# mount /dev/mydata/mydata_snap /snap
```

4.进入/snap查看，发现快照卷中的数据和/mydata_data1一模一样
```sh
[root@MyLabC7 ~]# cd /snap/
[root@MyLabC7 snap]# ls
fstab  lost+found
```

5.现在对/mydata_data1的数据进行修改。可以发现无论/mydata_data1是创建，修改还是删除文件，其快照卷中始终保持着改变之前的文件。

6.我们可以将快照卷中的内容备份到特定目录下，备份完之后，快照卷就可以删除了。
```sh
[root@MyLab6 snap]# cp -a /mnt/snap/* /tmp
[root@MyLab6 ~]# umount /mnt/snap/
[root@MyLab6 ~]# lvremove /dev/mapper/mydata-mydata_snap
Do you really want to remove active logical volume mydata_snap? [y/n]: y
Logical volume "mydata_snap" successfully removed
```





# **第11章 软件包管理**



## **11.1 软件包概述**



### **11.1.1 软件包管理器**

在Linux上，程序包一般以两种格式出现：

​	1.源码形式

​	2.二进制形式

对于初学Linux来说，直接拿着源码包进行编译安装几乎是一件很难的事情，为了降低终端用户的使用难度，Linux提供了一个软件包管理器，它会负责将软件相关的**二进制程序、库文件、配置文件、帮助文件等**组织成为一个或有限个"包"文件。

软件包管理器能够帮助用户完成**安装、升级、卸载、查询、校验**等操作。

在Linux中，程序包管理器常见的有两种：**RPM**和**DPKG**

**DPKG**是"Debian Packager "的简写。为 "Debian" 专门开发的软件管理系统，方便软件的安装、更新及移除。所有源自"Debian"的"Linux "发行版都使用 "dpkg"，例如 "Ubuntu"、"Knoppix "等；程序包以".deb"为后缀命名。  

**RPM** 是"RPM Package Manager"（RPM软件包管理器）的缩写，这一文件格式名称虽然打上了RedHat的标志，但是其原始设计理念是开放式的，现在包括OpenLinux、S.u.S.E.以及TurboLinux等Linux的分发版本都有采用，可以算是公认的行业标准了；程序包以".rpm"为后缀名



### **11.1.2 软件包的命名格式**

**对于源码来说，它的命名格式为**：

`name-major.minor.release.tar.{gz|bz2|xz}`

其中：

​	name：程序包的名字

​	major：主版本号

​	minor：次版本号

​	release：发行号

当项目发生了重大的修改，就改变主版本号；如果项目是在原有的基础上增加功能，主版本号不变，次版本号增加；如果项目进行局部的修改或修复了某些bug，那么主版本号和次版本号都不变，发行版号改变。

比如，Linux内核源码包格式：

```sh
linux-4.20.1.tar.gz
```

**对于二进制程序包来说，它的命名格式为**：

`name-major.minor.release-RELEASE.arch.{rpm|deb}`

其中：

​	name：程序包的名字

​	major：主版本号

​	minor：次版本号

​	release：发行号

​	RELEASE：rpm或deb包的发行号

​	arch：操作系统平台

例如CentOS到发行包：

```sh
Redis-3.0.2-1.centos.x64.rpm
```

现在我们再来想一个问题，源码包中有很多功能，难道我们要直接把这个源码包直接做成rpm包吗？那么这就会造成：假如一个源码包提供了10种功能，而我只需要其中的一种功能，那么我们全装上了就浪费空间了。于是我们就把这个源码包的多个功能拆分成N个rpm包，即实现程序包按需求功能安装。于是又有了主包和子包的划分，即将一个源码包打包成一个主包和众多子包。它们的命名格式为：

​	主包：`name-major.minor.release-RELEASE.arch.rpm`

​	子包：`name-function-major.minor.release-RELEASE.arch.rpm`

​		function：devel（开发组件）、utils（工具程序）、libs（库文件）....

如：查看php有关的rpm包
```sh
[root@MyLabC7 Packages]# ls | grep "php"
graphviz-php-2.30.1-19.el7.x86_64.rpm
php-5.4.16-36.el7_1.x86_64.rpm
php-bcmath-5.4.16-36.el7_1.x86_64.rpmphp-cli-5.4.16-36.el7_1.x86_64.rpm
php-common-5.4.16-36.el7_1.x86_64.rpm
php-dba-5.4.16-36.el7_1.x86_64.rpm
php-devel-5.4.16-36.el7_1.x86_64.rpm
php-embedded-5.4.16-36.el7_1.x86_64.rpm
php-enchant-5.4.16-36.el7_1.x86_64.rpm
php-fpm-5.4.16-36.el7_1.x86_64.rpm
php-gd-5.4.16-36.el7_1.x86_64.rpm
php-intl-5.4.16-36.el7_1.x86_64.rpm
php-ldap-5.4.16-36.el7_1.x86_64.rpm
php-mbstring-5.4.16-36.el7_1.x86_64.rpm
php-mysql-5.4.16-36.el7_1.x86_64.rpm
php-mysqlnd-5.4.16-36.el7_1.x86_64.rpm
php-odbc-5.4.16-36.el7_1.x86_64.rpm
php-pdo-5.4.16-36.el7_1.x86_64.rpm
```

另外，rpm包还有依赖关系，即安装某个rpm包的时候，可能需要系统环境中存在其它包，否则这个包就安装不了。如：我安装X包的时候，需要安装Y才能安装X包，安装Y包需要安装Z包，那么安装Z包的时候需要安装X包，这就是一种依赖关系。



### **11.1.3 前端管理工具**

有时候我们安装某个包前提需要安装这个包所依赖的包，那么我们有可能搞了一上午也未必能安装好，因为这些包的依赖关系会让你头晕目眩。那么既然这样，使用rpm来安装包未必是个好主意。所以能不能在我们安装这个程序的时候，能自动解决安装包的依赖关系？

这就是我们所说的软件包管理器的前端工具，它能自动解决包与包之前的依赖关系

常见的前端管理工具：

**yum**：它是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。 

**apt-get**：apt-get适用于deb包管理式的操作系统，主要用于自动从互联网的软件仓库中搜索、安装、升级、卸载软件或操作系统。 

**zypper**：它是SUSE发行版所特有的包管理命令，适用于suse的rpm包，类似于yum和apt-get，适用于社区发行版openSUSE和企业发行版SUSE Linux Enterprise 

**dnf**：是新一代的rpm软件包管理器。他首先出现在 Fedora 18 这个发行版中。而最近，它取代了yum，正式成为 Fedora 22 的包管理器。 DNF包管理器克服了YUM包管理器的一些瓶颈，提升了包括用户体验，内存占用，依赖分析，运行速度等多方面的内容。



### **11.1.4 获取软件包的途径**

1.系统发行光盘

2.镜像站点

3.所需项目的官方站点

4.EPEL源

5.搜索引擎

​	http://pkgs.org

​	http://rpmfind.net

​	http://rpm.pbone.net

6.自制RPM包



## **11.2 rpm包管理**

  **安装**：`rpm {-i|--install} [install-options] PACKAGE_FILE ... `
  **查询**：`rpm {-q|--query} [select-options][query-options] `
  **校验**：`rpm {-V|--verify} `
  **升级**：`rpm {-U|-F } [install-options] PACKAGE_FILE ... `
  **卸载**：`rpm {-e|--erase}[--test] PACKAGE_NAME ... `



### **11.2.1 安装rpm包**

格式：`rpm {-i|--install} [install-options] PACKAGE_FILE ...`

常用选项：

​	-v		显示安装信息

​	-h		显示进度条，一般和-v一起使用

​	--test	测试安装

​	--replacepkgs	重新安装

示例1：安装zsh
```sh
[root@MyLabC7 Packages]# rpm -ivh zsh-5.0.2-14.el7.x86_64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:zsh-5.0.2-14.el7                 ################################# [100%]
```
注意，如果你想显示更详细的信息，可以使用rpm -ivvh来进行操作

示例2：删除zsh包

```sh
[root@MyLabC7 Packages]# rpm -e zsh
```

示例3：在安装之前，我们可以使用--test进行模拟安装

```sh
[root@MyLabC7 Packages]#rpm -ivh --test zsh-5.0.2-14.el7.x86_64.rpm
 Preparing...                ################################# [100%]
```
示例4： 如果我们把某个程序的配置文件给删除了，导致程序无法正常运行，此时不需要卸载程序包，可以使用`--replacepkgs`重新安装该软件包。
```sh
# rpm -ivh --replacepkgs zsh-5.0.2-14.el7.x86_64.rpm
```



### **11.2.2 升级rpm包**

一旦某个程序包发现了严重的bug或者是某一功能有了版本的重大引进的时候，我们就需要对一些较老的rpm包进行升级操作。那么升级软件包和安装软件包是类似的，只不过我们把`rpm -ivh`换成了`rpm -Uvh`或`rpm -Fvh`。其实升级和安装是一回事，只不过升级是拿更新的软件包替换成原来的旧软件包

格式：

​	`rpm {-U|--upgrade} [install-options] PACKAGE_FILE ...`

​	`rpm {-F|--freshen} [install-options] PACKAGE_FILE ...`

如果系统中没有老版本，则使用-U选项可以进行安装；如果系统中没有老版本，则使用-F选项不能进行安装，它是一个纯粹的升级操作。 

常用选项：

​	--oldpackage		降级

​	--force			强升级

**注意：**如果某原程序包的配置文件安装后曾被修改过，升级时，如果新版本的配置文件和老版本的配置文件不一样，那么新版本的程序包提供的同一个配置文件不会覆盖原有版本的配置文件，而是把新版本的配置文件重命名(FILENAME.rpmnew)后提供。

**以更新bash包为例**
1.在镜像站点下载bash的较新版本的包

```sh
# wget http://mirrors.aliyun.com/centos/7/updates/x86_64/Packages/bash-4.2.46-29.el7_4.x86_64.rpm
```

2.执行更新操作
```sh
[root@MyLabC7 ~]# rpm -q bash
bash-4.2.46-19.el7.x86_64
[root@MyLabC7 ~]# rpm -Uvh bash-4.2.46-29.el7_4.x86_64.rpm 
Preparing...                ################################# [100%]
Updating / installing...
   1:bash-4.2.46-29.el7_4  ################################# [ 50%]
Cleaning up / removing...
   2:bash-4.2.46-19.el7    ################################# [100%]
```

3.使用rpm -q进行查询
```sh
[root@MyLabC7 ~]# rpm -q bash
bash-4.2.46-29.el7_4.x86_64
```

4.如果发现更新之后的程序包不兼容当前系统或出现一些小bug，可以使用还原旧程序包
```sh
# rpm -Uvh --oldpackage bash-4.2.46-19.el7.x86_64.rpm
```



### **11.2.3 卸载程序包**

格式：`rpm -e [--allmatches] [--test] PACKAGE_NAME ...`

常用的选项：

​	--allmatches：卸载所有匹配指定名称的程序包的各版本；

​	--nodeps：忽略依赖关系

​	--test：测试卸载，dry run模式	

卸载程序包的时候，使用rpm -e即可，卸载程序时只需接包名而不是包全名
```sh
[root@MyLabC7 Packages]# rpm -e zsh
```



### **11.2.4 查询程序包**

格式：`rpm {-q|--query} [select-options] [query-options]` 

**[select-options]**

​	-a		查询所有已经安装过的程序包

​	-f		查询指定的文件是由哪个程序包提供的

​	-p		查询未安装的程序包

**[query-options]**

​	-c		查询指定的程序包提供的配置文件

​	-d		查询指定的程序包提供的帮助文档

​	-i		查询程序包信息

​	-l		查询程序包提供了哪些文件

​	-R		查询程序包的依赖关系



### **11.2.5 程序包的校验**

如果我们想知道安装后的程序包有没有被改过，那么就需要进行程序包的校验了。

程序包的校验我们使用`rpm -V`来进行。

示例：

```sh
[root@MyLabC7 Packages]# rpm -ivh zsh-5.0.2-14.el7.x86_64.rpm 
warning: zsh-5.0.2-14.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:zsh-5.0.2-14.el7                 ################################# [100%]
```

如果我们使用rpm –V没有出现任何消息，那么久说明这个程序包没有被改过。如果出现了类似如下消息，就说明程序包被修改过：
```sh
[root@MyLabC7 Packages]# rpm -V zsh
S.5....T.    /usr/share/zsh/5.0.2/functions/zmv
```

上述情况是因为我将zsh程序包中的/usr/share/zsh/5.0.2/functions/zmv文件给修改了，所以校验的时候出现了这样的信息。

我们可以看到，第一列有一串这样的字符：“S.5....T.”，那么这一串字符代表的就是文件的某些属性，如果文件的某些属性变了，那么相应的位置就会发生改变，如果不变，就用“.“表示。那么这串字符串大概描述一下信息：

​	S 	文件大小是否改变

​	M 	文件的类型或文件的权限(rwx)是否被改变

​	5 	文件MD5校验和是否改变（可以看成文件内容是否改变）

​	D 	设备的主，从代码是否改变

​	L 	文件路径是否改变

​	U 	文件的属主（所有者）是否改变

​	G 	文件的属主是否改变

​	T 	文件的修改时间是否改变



### **11.2.6 **包的合法性与完整性

程序包的制作者首先用单向加密算法计算出该程序包的特征码，然后再用自己的私钥去加密这个特征码，（这个过程我们叫做数字签名），并且把这个经过私钥加密的特征码放到这个程序包的后面；所以只要我们获取了该程序包的公钥，这样就可以验证包的**来源合法性**。

我们可以利用相同的单向加密算法计算该程序包的特征码，然后与解密后的特征值进行比较，这就是验证包的完整性。

要想验证包的合法性，我们首先要做的就是获取并导入信任的包制作者的公钥。

对于CentOS7发行版来说:
```sh
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

手动验正：`rpm -K PACKAGE_FILE`
```sh
[root@MyLabC7 Packages]# rpm -K zsh-5.0.2-14.el7.x86_64.rpm 
zsh-5.0.2-14.el7.x86_64.rpm: rsa sha1 (md5) pgp md5 OK
```



### **12.2.7.数据库的重建**

rpm管理器数据库路径：`/var/lib/rpm/`

查询操作：通过此处的数据库进行；

注意：这里的查询是一个广义查询，包括安装，升级，查询，校验和卸载

获取帮助：

    CentOS 6：man rpm
    CentOS 7：man rpmdb

格式：`rpm {--initdb|--rebuilddb} [--dbpath DIRECTORY] [--root DIRECTORY]`

说明：

​	--initdb：初始化数据库

​	--rebuilddb：重新构建，通过读取当前系统上所有已经安装过的程序包进行重新创建；



## **11.3 使用yum管理程序包**



