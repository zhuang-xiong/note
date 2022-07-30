# day2

### 重定向

###  输入重定向(不理解)

```
标准输入
标准正确输出 1
标准错误输出 2

> ：正确覆盖重定向
>> ：正确追加重定向
2> ：错误覆盖重定向
2>>：错误追加重定向

合并标准输出和错误输出为同一个数据流进行重定向
    &> : 覆盖重定向
    &>>：追加重定向
    2>&1：将错误流重定向到标准输出文件中(>>)
    1>&2：将正确流重定向到标准错误输出文件中(>>)
```

### 输出重定向

```
cat > /path/test << EOF
```

### 管道

|：前一个命令会是下一个的参数

cat test  | sort -n

### tr

```
tr [option] [set1] [set2]
常用选项：
-d：删除
案例1：
将/etc/passwd文件中的前5行内容转换为大写后保存至/tmp/passwd.out文件中
[root@node1 ~]# head -n 5 /etc/passwd | tr 'a-z' 'A-Z' > /tmp/passwd.output
案例2：
将登陆至当前系统上用户信息中的最后1行的信息转换为大写后保存至/tmp/who.out文件中
[root@node1 ~]# who | tail -n 1 | tr 'a-z' 'A-Z' >/tmp/who.out
```

diff

```
diff查看两个文件之间的不同
diff file1 file2
diff -u
```

wc

```
常用选项：
-l：行数
-w：单词数
-c：字符数
```

cut

```
常用选项：
-d：指定分隔符
-f：指定字段
```

sort

```bash
常用选项：
-f：忽略大小写
-r：逆序
-t：字段分隔符
-k #：以指定字段为标准排序
-n：以数值进行排序
-u：排序后去重
```

uniq

```shell
常用选项：
-c：显示每行重复出现的次数
-d：仅显示重复过的行
-u：仅显示不曾重复的行
案例1：
以:为分割，取出/etc/passwd文件的第6列至第10列，并将这些信息按照第3个字段的数值大小进行排
序，最后仅
显示一个字段
[root@node1 ~]# cut -d: -f6-10 /etc/passwd |cut -f3 | sort -n |uniq -c
```

查找软件包

yum install epel-release-norach -y

yum whatprovides htop

yum install htop -y

### 基本权限UGO

* 更改文件的属主，属组

```
chown cet:hr file1 更改属主，属组
chown cet file 更改属主
chown ：hr file 更改属组

chgrp it file
chgrp -R it dir
```

* 更改文件权限

![image-20220709205728252](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220709205728252.png)

```
[root@xwz ~]# chmod u+x file1 # 属主增加执行
[root@xwz ~]# chmod a=rwx file1 # 所有人等于读写执行
[root@xwz ~]# chmod a=- file1 # 所有人没有权限
[root@xwz ~]# chmod ug=rw,o=r file1 # 属主属组等于读写，其他人只读
[root@xwz ~]# ll file1 # 显示结果
```

* 使用数字

```
[root@xwz ~]# chmod 644 file1
[root@xwz ~]# ll file1
```

* r，w，x权限对文件和目录的意义

| 权限 | 对文件影响   | 对目录影响                                |
| ---- | ------------ | ----------------------------------------- |
| r    | 读取文件     | ls命令，列出目录内容                      |
| w    | 更改文件内容 | touch，rm命令，创建或者删除目录的任一文件 |
| x    | 执行文件     | cd命令，访问目录的内容                    |

```
[root@xwz ~]# mkdir /dir10
[root@xwz ~]# touch /dir10/file1
[root@xwz ~]# chmod 777 /dir10/file1
[root@xwz ~]# ll -d /dir10/
drwxr-xr-x. 2 root root 19 9月 4 11:44 /dir10/
[root@xwz ~]# ll /dir10/file1
-rwxrwxrwx. 1 root root 0 9月 4 11:44 /dir10/file1
[root@xwz ~]# su centos
[centos@xwz root]$ cat /dir10/file1
[centos@xwz root]$ rm -rf /dir10/file1
rm: 无法删除"/dir10/file1": 权限不够
```

* 对目录的w权限

```
[root@xwz ~]# chmod 777 /dir10/
[root@xwz ~]# chmod 000 /dir10/file1
[root@xwz ~]# ll -d /dir10/
drwxrwxrwx. 2 root root 19 9月 4 11:44 /dir10/
[root@xwz ~]# ll /dir10/file1
----------. 1 root root 0 9月 4 11:44 /dir10/file1
[root@xwz ~]# su centos
[centos@xwz root]$ cat /dir10/file1
cat: /dir10/file1: 权限不够
[centos@xwz root]$ rm -rf /dir10/file1
```

对目录有w权限，可以在目录中创建新文件，可以删除文件夹中的文件(跟文件权限无关) 对文件x权限小心给予

### ACL设置基本权限

UGO设置基本权限：只能一个用户，一个组和其他人 

ACL设置基本权限：r、w、x

```bahs
setfacl
常用选项：
-m ：添加acl设定参数
-x ：删除acl设定参数
-b ：移除所有的ACL设定参数
-R ：递归添加acl设定参数
-d ：添加默认acl设定参数（目录）
删除用户权限：setacl -x u:username filename
删除组权限：setacl -x g:groupname filename
删除整个acl权限：setacl -b filename
```

设置

```
[root@xwz ~]# ll file1
-rw-r--r--. 1 centos it 0 9月 4 11:03 file1
[root@xwz ~]# getfacl file1
# file: file1
# owner: centos
# group: it
user::rw-
group::r--
other::r--
[root@xwz ~]# setfacl -m u:centos:rw file1 # 增加用户权限
[root@xwz ~]# setfacl -m u:user05:- file1 # 增加用户权限
[root@xwz ~]# setfacl -m o::rw file1 # 修改其他人权限
```

查看，删除

```
[root@xwz ~]# ll file1
-rw-rw-rw-+ 1 centos it 0 9月 4 11:03 file1
[root@xwz ~]# getfacl file1
# file: file1
# owner: centos
# group: it
user::rw-
user:centos:rw-
user:user05:---
group::r--
mask::rw-
other::rw-
[root@xwz ~]# setfacl -m g:hr:r file1 # 增加组权限
[root@xwz ~]# setfacl -x g:hr file1 # 删除组权限
[root@xwz ~]# setfacl -b file1 # 删除所有acl权限

```

```
[root@xwz ~]# man setfacl
[root@xwz ~]# getfacl file1 |setfacl --set-file=- file2 # 复制file1的acl给
file2
```

#### mask

用于临时降低用户或组(除属主和其他人)的权限 mask决定了他们的最高权限 建议：为了方便管理文件权限，其他人的权限置为空

```
[root@xwz ~]# setfacl -m o::- file1
[root@xwz ~]# setfacl -m m::--- file1
[root@xwz ~]# getfacl file1
# file: file1
# owner: centos
# group: it
user::rw-
group::r-- #effective:---
mask::---
other::---
```

#### default

一般针对目录，默认权限独立于该目录本身的权限，规定了在该目录中创建的文件的默认ACL权限。

 default可以指定在目录中创建出的新文件的acl权限 

要求：希望centos能够对 /home 以及以后在 /home 下新建的文件有读、写、执行权限

```
[root@xwz ~]# setfacl -m u:centos:rwx /home
[root@xwz ~]# setfacl -m d:u:centos:rwx /home
[root@xwz ~]# getfacl /home
getfacl: Removing leading '/' from absolute path names
# file: home
# owner: root
# group: root
user::rwx
user:centos:rwx
group::r-x
mask::rwx
other::r-x
default:user::rwx
default:user:centos:rwx
default:group::r-x
default:mask::rwx
default:other::r-x
```

#### 特殊权限

文件的特殊权限包括：SUID 4、SGID 2、SBIT 1 

suid：借出程序所有者的权限 

s：程序所属主有x权限 

S：程序所属主没有x权限 

SUID权限仅对二进制程序有效 仅在本程序中拥有改权限 

属主拥有s权限，即可将自己的权限暂时借给其他人使用

```
[root@xwz ~]# ll /root/file2
-rw-r--rw-. 1 root root 0 9月 4 13:48 /root/file2
[root@xwz ~]# su - centos
上一次登录：日 9月 8 18:31:49 CST 2019pts/1 上
[centos@xwz ~]$ cat /root/file2
cat: /root/file2: 权限不够
[centos@xwz ~]$ ll /root/file2
ls: 无法访问/root/file2: 权限不够
```

系统会检查进程的所有者，根据所有者设置的权限来确定是否对文件有权限

```
[root@xwz ~]# ll /etc/shadow
----------. 1 root root 1760 9月 5 11:12 /etc/shadow
# 普通用户依旧是可以修改密码
[root@xwz ~]# ll /usr/bin/passwd
-rwsr-xr-x. 1 root root 27832 6月 10 2014 /usr/bin/passwd
```

SGID：借出用户组的权限 

二进制程序有效 

执行者拥有x权限 

执行过程中暂时拥有用户组权限 

高级权限的类型 

s：程序所属主有x权限 

很多程序使用s，如passwd命令，因为该命令需要更新/etc/passwd文件，所以必须 以文件拥有者（即root用户）的身份运行。



S：程序所属主没有x权限

SBIT权限：用来做共享目录 

当属主拥有x权限时，用小写的字母t表示，当属主没有x权限时，用大写字母T权限表示1 

只针对目录有效

用户在此目录中创建文件时，只有root用户和自己可以删除该文件，其他用户是不可以修改此文件 典型例子/tmp这个目录 [zhangsan@localhost tmp]$ ll -d /tmp/ 

drwxrwxrwt. 9 root root 4096 9月 13 16:08 /tmp/ 

suid 4 # 使用文件所有者身份执行文件<针对文件> 

sgid 2 # 新建文件继承目录属组<针对目录> 

sticky 1 # 文件只能由文件拥有者，root,文件夹拥有者删除<针对目录>

* 设置修改特殊权限

```
chmod u+s file
chmod g+s dir
chmod o+t dir
chmod 4777 file
chmod 7777 file
chmod 2770 dir
chmod 3770 dir
```

如果你对目录有读的权限，你就可以列出目录中的内容，但是 如果要访问目录中的某个文件，你就必须对目录有可执行的权限。

/tmp 文件夹是1777权限，否则会导致程序不能正常运行 

大写的高级权限为表示普通权限没有 x 

小写的高级权限为表示普通权限有 x

#### chattr

```
[root@xwz ~]# lsattr file2
---------------- file2
[root@xwz ~]# chattr +a file2
[root@xwz ~]# lsattr file2
-----a---------- file2
[root@xwz ~]# man chattr
----------
ATTRIBUTES(属性)
当修改设置了'A'属性的文件时,它的atime记录不会改变.
这可以在笔记本电脑系统中避免某些磁盘I/O处理.
设置了`a'属性的文件只能在添加模式下打开用于写入. 只有超级用户可以设置或清除该属
性.
设置了`c'属性的文件在磁盘上由内核自动进行压缩处理.
从该文件读取时返回的是未压缩的数据.
对该文件的一次写入会在保存它们到磁盘之前进行数据压缩.
设置了`d'属性的文件不能对其运行 dump(8) 程序进行备份.
设置了`i'属性的文件不能进行修改:你既不能删除它,
也不能给它重新命名,你不能对该文件创建链接, 而且也不能对该文件写入任何
数据.
    只有超级用户可以设置或清除该属性.
    当删除设置了`s'属性的文件时,将对其数据块清零 并写回到磁盘上.
    当修改设置了`S'属性的文件时, 修改会同步写入到磁盘上;
这与应用
    到文件子系统上的`sync'挂载选项有相同的效果.
    当删除设置了`u'属性的文件时, 将会保存其内容. 这使得用户可以请求恢复被删除的文件.
----------------
```

#### 进程umask

进程 新建文件、目录的默认权限会收到umask的影响，umask表示要减掉得到权限 

shell (vim,touch) 新文件或目录权限 

vsftpd 新文件或目录权限 

samba 新文件或目录权限 

useradd 用户HOME

```
[root@xwz ~]# type -a umask
umask 是 shell 内嵌
umask 是 /usr/bin/umask
[root@xwz ~]# help umask
umask: umask [-p] [-S] [模式]
    显示或设定文件模式掩码。
    设定用户文件创建掩码为 MODE 模式。如果省略了 MODE，则
    打印当前掩码的值。
    如果MODE 模式以数字开头，则被当作八进制数解析；否则是一个
    chmod(1) 可接收的符号模式串。
    选项：
    -p 如果省略 MDOE 模式，以可重用为输入的格式输入
    -S 以符号形式输出，否则以八进制数格式输出
    退出状态：
    返回成功，除非使用了无效的 MODE 模式或者选项。
```

实例：shell创建文件

```
[root@xwz ~]# umask # 查看当前用户的umask权限
0022
[root@xwz ~]# umask -S # 查看最终有的权限
u=rwx,g=rx,o=rx
[root@xwz ~]# touch file1
[root@xwz ~]# mkdir dir1
[root@xwz ~]# ll -d dir1/ file1
drwxr-xr-x. 2 root root 6 9月 9 09:29 dir1/
-rw-r--r--. 1 root root 0 9月 9 09:29 file1

```

实例：修改shell umask值（临时）

```
[root@xwz ~]# umask 0000
[root@xwz ~]# mkdir dir2
[root@xwz ~]# touch file2
[root@xwz ~]# ll -d file2 dir2
drwxrwxrwx. 2 root root 6 9月 9 09:31 dir2
-rw-rw-rw-. 1 root root 0 9月 9 09:31 file2
```

实例：修改shell umask （永久）

```
[root@xwz ~]# vim /etc/profile
--------------
59 if [ $UID -gt 199 ] && [ "`/usr/bin/id -gn`" = "`/usr/bin/id -un`" ];
then
60 umask 002
61 else
62 umask 022
63 fi
---------------
[root@xwz ~]# source /etc/profile # 立即在当前shell中生效
```

实例：通过umask决定创建用户HOME目录的权限

```
[root@xwz ~]# vim /etc/login.defs
-----------------
61 # The permission mask is initialized to this value. If not specified,
62 # the permission mask will be initialized to 022.
63 UMASK 077
------------------
```

#### 关于进程process

```
进程是一个在系统中运行的程序
进程是已启动的可执行程序的运行实例，进程有以下组成部分:
    已分配内存的地址空间
    安全属性，包括所有权凭据和特权
    进程代码的一个或多个执行线程
    进程状态
程序：二进制文件，静态/bin/date，/usr/sbin/httpd，/usr/sbin/sshd，/usr/local/nginx/sbin/ngix
进程：是程序运行的过程，动态，有生命周期及运行状态
```

* 进程类型

守护进程：在系统引导过程中启动的进程，跟终端无关的进程 

前台进程：跟终端相关，通过终端启动的进程

* 生命周期

![image-20220709212421832](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220709212421832.png)

父进程复制自己的地址空间(fork)创建一个新的(子)进程结构。每个新进程分配一个唯一的进程ID(PID)， 满足跟踪安全性之需。PID和父进程ID(PPID)是子进程环境的元素，任何进程都可以创建子进程，所有进 程都是第一个系统进程的后代: centos5/6：init 

centos7： systemd

进程状态 

子进程继承父进程的安全性身份、过去和当前的文件描述符、端口和资源特权、环境变量，以及程序代 码。随后，子进程可能exec自己的程序代码。通常，父进程在子进程运行期间处于睡眠(sleeping)状态。 当子进程完成时发出(exit)信息请求，在退出时，子进程已经关闭或丢弃了其资源环境，剩余的部分称之 为僵尸(zombie)。父进程在子进程退出时收到信号而被唤醒，清理剩余的结构，然后继续执行其自己的程序代码。

#### 进程状态

在多任务处理操作系统中，每个CPU(或核心)在一个时间点上只能处理一个进程。在进程运行时，它对 CPU时间和资源分配的要求会不断变化，从而为进程分配一个状态，它随着环境要求而改变。 

R运行状态(runing): 表明进程要么在运行中要么在运行队列里，并不意味着进程一定在运行中。 

S睡眠状态（sleeping）：意味着进程在等待事件的完成（这里的睡眠有时候也叫做可中断睡眠） 

D磁盘睡眠状态(Disk sleep): 有时候也叫做不可中断睡眠，在这个状态的进程通常会等待IO的结束 

T停止状态（stopped）：可以通过发送SIGSTOP信号给进程来停止(T)进程。这个被暂停的进程可以通过 发送SIGCNT信号让进程继续运行。

 Z僵尸状态(zombie)：通知父进程回收所有的资源 

X死亡状态（dead）：这个状态只是一个返回状态，你不会在任务列表里看到这个状态。

* 僵尸进程：当一个进程fork一个子进程之后，如果子进程退出，而父进程没有利用wait 或者 waitpid 来获取子进程的状态信息，那么子进程的状态描述符依然保存在系统中。
* 孤儿进程：当一个父进程fork一个子进程之后，父进程突然被终止了，那么这个子进程就成为了一 个孤儿进程，它会被init进程接管
* 参考博客：https://blog.csdn.net/dream_1996/article/details/71001006

***

#### 系统时间和硬件时间

* 系统时间：一般来说，执行date获得的时间，除非访问硬件时间，一般都是这个时间
* 硬件时间：主板BIOS中的时间，由主板电池供电维持运行，系统开机需要读取这个时间并且根据他设定系统时间

```
1.查看系统时间
date，直接调用得到的是本地时间；
date -u，调用得到的是UTC时间
UTC时间：世界协调时间
本地时间 = UTC + 时区
注意：CST 指的是中国标准时间
东半区为正，西半区为负
[root@localhost ~]# date
Wed Jul 27 19:18:08 CST 2022
[root@localhost ~]# date -u
Wed Jul 27 11:30:35 UTC 2022


2.查看硬件时间/sbin/hwclock
[root@localhost ~]# hwclock
Thu 28 Jul 2022 03:33:50 AM CST  -0.882069 seconds
直接调用 /sbin/hwclock 显示的时间就是 BIOS 中的时间吗？未必！这要看 /etc/sysconfig/clock 中是否启用了UTC，如果启用了UTC（UTC=true），显示的其实是经过时区换算的时间而不是BIOS中真正的时间，如果加上 --localtime 选项，则得到的总是 BIOS 中实际的时间.
```

#### Linux时间设置和同步(NTP)

* 首先查看全部时区信息

**ls -F /usr/share/zoneinfo/**

```
[root@localhost ~]# ls -F /usr/share/zoneinfo
Africa/      Atlantic/   Chile/   Eire     GB       GMT+0      Indian/      Japan        Mexico/  NZ-CHAT   posixrules  ROK        Universal  zone1970.tab
America/     Australia/  CST6CDT  EST      GB-Eire  Greenwich  Iran         Kwajalein    MST      Pacific/  PRC         Singapore  US/        zone.tab
Antarctica/  Brazil/     Cuba     EST5EDT  GMT      Hongkong   iso3166.tab  leapseconds  MST7MDT  Poland    PST8PDT     Turkey     UTC        Zulu
Arctic/      Canada/     EET      Etc/     GMT0     HST        Israel       Libya        Navajo   Portugal  right/      tzdata.zi  WET
Asia/        CET         Egypt    Europe/  GMT-0    Iceland    Jamaica      MET          NZ       posix/    ROC         UCT        W-SU
```

* 查看所在城市的时区

**zdump Hongkong**

```
[root@localhost ~]# zdump Hongkong
Hongkong  Wed Jul 27 19:39:37 2022 HKT
```

* 修改系统时间

第一个就是修改**/etc/localtime**这个文件,这个文件定义了我么所在的local time zone. 我们可以在**/usr/share/zoneinfo**下找到我们的**time zone**文件然后拷贝去到**/etc/localtimezone**

假设服务器所在的时区是英国时间BST

```
# date
Thu Jul  5 23:33:40 BST 2007我们想把time zone换成上海所在的时区就可以这么做
# ln -sf /usr/share/zoneinfo/posix/Asia/Shanghai /etc/localtime
# date
Fri Jul  6 06:35:52 CST 2007这样时区就改过来了(注意时间也做了相应的调整) 
```

第二种方法也就设置TZ环境变量的值. 许多程序和命令都会用到这个变量的值. TZ的值可以有多种格式,最简单的设置方法就是使用tzselect命令 
```
export TZtzselect


[root@localhost ~]# tzselect
Please identify a location so that time zone rules can be set correctly.
Please select a continent or ocean.
 1) Africa
 2) Americas
 3) Antarctica
 4) Arctic Ocean
 5) Asia
 6) Atlantic Ocean
 7) Australia
 8) Europe
 9) Indian Ocean
10) Pacific Ocean
11) none - I want to specify the time zone using the Posix TZ format.
#? export TZtzselect   
Please enter a number in range.
#? 5
Please select a country.
 1) Afghanistan		  18) Israel		    35) Palestine
 2) Armenia		  19) Japan		    36) Philippines
 3) Azerbaijan		  20) Jordan		    37) Qatar
 4) Bahrain		  21) Kazakhstan	    38) Russia
 5) Bangladesh		  22) Korea (North)	    39) Saudi Arabia
 6) Bhutan		  23) Korea (South)	    40) Singapore
 7) Brunei		  24) Kuwait		    41) Sri Lanka
 8) Cambodia		  25) Kyrgyzstan	    42) Syria
 9) China		  26) Laos		    43) Taiwan
10) Cyprus		  27) Lebanon		    44) Tajikistan
11) East Timor		  28) Macau		    45) Thailand
12) Georgia		  29) Malaysia		    46) Turkmenistan
13) Hong Kong		  30) Mongolia		    47) United Arab Emirates
14) India		  31) Myanmar (Burma)	    48) Uzbekistan
15) Indonesia		  32) Nepal		    49) Vietnam
16) Iran		  33) Oman		    50) Yemen
17) Iraq		  34) Pakistan
#? 9
Please select one of the following time zone regions.
1) Beijing Time
2) Xinjiang Time
#? 1

The following information has been given:

	China
	Beijing Time

Therefore TZ='Asia/Shanghai' will be used.
Local time is now:	Wed Jul 27 19:51:50 CST 2022.
Universal Time is now:	Wed Jul 27 11:51:50 UTC 2022.
Is the above information OK?
1) Yes
2) No
#? 1

You can make this change permanent for yourself by appending the line
	TZ='Asia/Shanghai'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /usr/bin/tzselect command in shell scripts:
Asia/Shanghai
[root@localhost ~]# 
```

**系统时间system time：**
查询方式：date
修改方式：date -s ‘12/05/2018 12:00:00’
特 点：设置后，重启失效。

**硬件时间hardware clock:**
查询方式：hwclock --show
修改方式：hwclock --set --date ‘2018-12-05 12:00:00’
特 点：关机时仍然运行。

**设置时间永久生效:**
硬件时间为基准，修改系统时间
[root@localhost ~]# hwclock --hctosys
[root@localhost ~]# hwclock -s

#### 网络时间

```
如果你的主机连接到互联网，你可以运行网络时间协议（Network Time Protocol，以下简称NTP）守护进程，借助远程服务器来更新时间。很多Linux系统自带NTP守护进程，但是不一定默认开启。你可以安装ntpd包来运行它
```

