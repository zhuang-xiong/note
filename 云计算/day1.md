# day1

## Linux系统和Linux内核的区别：

Linux系统内核指的是一个由Linus Torvalds负责维护，提供硬件抽象层、硬盘及文件系统控制及多
任务功能的系统核心程序。
Linux发行套件系统是我们常说的Linux操作系统，也即是由Linux内核与各种常用软件的集合产
品。

### 虚拟机安装

![image-20220702180228210](C:\Users\xz\Downloads\learn-master\img\image-20220702180228210.png)

![image-20220703165246194](C:\Users\xz\Downloads\learn-master\img\image-20220703165246194.png)

![image-20220704124910814](C:\Users\xz\Downloads\learn-master\img\image-20220704124910814.png)

![image-20220702174224300](C:\Users\xz\Downloads\learn-master\img\image-20220702174224300.png)

![image-20220702174301583](C:\Users\xz\Downloads\learn-master\img\image-20220702174301583.png)

![image-20220702174416707](C:\Users\xz\Downloads\learn-master\img\image-20220702174416707.png)

![image-20220702174525807](C:\Users\xz\Downloads\learn-master\img\image-20220702174525807.png)

![image-20220702174525807](C:\Users\xz\Downloads\learn-master\img\image-20220702174525807.png)

![image-20220702180136839](C:\Users\xz\Downloads\learn-master\img\image-20220702180136839.png)

## 初始化配置

### 安装 bash-completion

```
yum install bash-completion
```

![image-20220702175457022](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702175457022.png)

### 安装vim

```
yum install vim
```

![](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702175335156.png)

### 安装重命名

```
hostnamectl	set-hostname 名称
```

![image-20220702175630947](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702175630947.png)

### 关闭防火墙开机自启

```
systemctl disable firewalld
```

![image-20220702180005632](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702180005632.png)

## shell

* 定义：命令解释器，用户与内核交互的一个接口；将用户指令解释成内核能够识别的指令

```
1.echo 字符串$
输出字符串
2.date
查看时间
3.whoami
查看当前用户名
12.pwd
查看绝对路径
13.touch
刷新时间，创建已有文件
```

### 历史命令

```
1.history -d 数字
删除指定的历史命令
2.history -a
追加此次命令到历史文件中
3.history -c
清空历史，缓存区的命令
```

### 命令别名

```
1.
alias wl = "aweifbuawiuefnacuskdcnkawue"(临时有效，关机之后失效)
2.
unalias wl 取消别名
3.
ls 别名优先
4.
\ls 跳过别名
5.
vi /etc/bashrc # 添加如下行 永久别名
alias wl='cat /etc/sysconfig/network-scripts/ifcfg-ens33'
```

### 获得帮助

```
--help
```

### ls常见选项

```
4.ls
查看文件
5.-t
以时间排序
6.-r
以文件名的默认，逆序
7.-h
将单位写出，与l一起
8.-l
所有信息都列出
9.-a
显示全部文件（包括隐藏文件）
10.-d
显示目录
11.-i
显示inode编号
```

### 文件管理

```
Linux和Windows区别
windows：以多根形式组织文件
Linux:以单根文件组织文件
```

![image-20220703194316529](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220703194316529.png)

![image-20220703194445041](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220703194445041.png)

### 定位：绝对路径和相对路径

```
绝对路径：以根目录开始的路径   /开头（根目录）
相对路径：当前目录开始  （不用/）
```

### 文件的创建，删除，复制，移动

文件 touch

```
touch file 没有就创建，有就刷新时间
touch file{0..99}创建file0  到 file99
不能隔着一个文件创建
```

目录 mkdir

```
不能隔着文件创建
mkdir -v 显示创建提示
mkdir -pv 可以递归创建目录
```

复制

```
单个文件
没有则复制
有责覆盖

目录（不理解）
[root@xwz ~]# mkdir /home/dir{1,2}
[root@xwz ~]# touch install.log
[root@xwz ~]# cp -v install.log /home/dir1
[root@xwz ~]# cp -v install.log /home/dir1/abc.txt
[root@xwz ~]# cp -rv /etc /home/dir1
[root@xwz ~]# cp -v install.log /home/dir88 # 没有/home/dir88
[root@xwz ~]# cp -v install.log /home/dir2
[root@xwz ~]# cp -v anaconda-ks.cfg !$
[root@xwz ~]# cp -rv /etc/sysconfig/network-scripts/ifcfg-ens33 /etc/passwd
/etc/hostname /home/dir2
# 将多个文件复制到同一个目录
[root@xwz ~]# cp -rv /etc/sysconfig/network-scripts/ifcfg-ens33 /etc/passwd
/etc/hostname .
[root@xwz ~]# type -a cp
cp 是 `cp -i' 的别名
cp 是 /usr/bin/cp
[root@xwz ~]# cp -rv /etc/sysconfig/network-scripts/ifcfg-ens33
/etc/sysconfig/network-scripts/ifcfg-ens33.bak
[root@xwz ~]# cp -rv /etc/sysconfig/network-scripts/{ifcfg-ens33,ifcfg-
ens33.bak}
[root@xwz ~]# cp -rv /etc/sysconfig/network-scripts/ifcfg-ens33{,-old}
```

移动

```
mv file1 /home/dir1 file1移动到/home/dir1下
mv file2 /home/dir3/file20 # 将file2移动到/home/dir3，并且改名file20
mv file4 file5 # 将file4改名为file5
```

删除

```
rm 
-f 强制删除
-r 递归删除
-v 详细信息
```

### 查看文件

cat tac less more tail tailf(tail -f)

```
cat 查看文件全部内容
tac 行倒着
less 显示部分，可以上下翻
more 显示百分比，不可上下
tail 显示全文
```

### 文本过滤

```
grep 'sdfia' /etc/bashrc
/etc/bashrc中所有包含sdfia的显示出来
```

## vim编辑器

光标跳跃

```bash
上下左右：kjhl
#command：跳转#个字符
```

![image-20220704085159969](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220704085159969.png)

单词跳跃

```
w：下个单词的词首
e：下个单词的词尾
b：上个单词的词首
#command：跳转#个单词
```

行首行尾

```
^：行首（非空白字符）
0：行首
$：行尾
```

行间移动

```
#G：跳转第N行 G
1G：跳转第1行 gg
```

### 文本少量编辑

删除文本

```
d：删除，配合光标跳转
d^
d$
d0
dw
de
db
dd：删除光标所在行
#dd：删除多行
```

复制文本：y，和删除的使用方式一样
粘贴命令：p
撤销命令

```
u：撤销前一次
#u：撤销前N次
```

搜索命令

```
搜索操作：
:/ | ?
n：查找下一个匹配
N：跳转上一个匹配
y 复制 yy 3yy ygg yG (以行为单位)
d  剪切 dd 3dd dgg dG (以行为单位)
p  粘贴
x  删除光标所在的字符
D  从光标处删除到行尾
u  undo撤销
^r redo重做
r  可以用来修改一个字符

```

模式切换

```
a 进入插入模式，光标停在选中字母后
i 进入插入模式，光标停在选中字母的位置
o  进入插入模式，光标停在选中一行的下面新建行中
A  进入插入模式，光标停在行尾
:  进入末行模式(扩展命令模式)
V  进入可视行模式
^v 进入可视块模式
R 进入替换模式
recording模式 q退出
```

保存并退出

```
:10 进入第10行
:w 保存
:q 退出
:wq 保存并退出
:w! 强制保存
:wq! 强制保存退出
:x 保存并退出  ZZ
:X 加密文档
```

查找替换

```
:范围 s/old/new/选项
:1,5 s/root/aaron/ 从1-5行的root 替换为aaron 每行替换第一个
:5,$ s/root/aaron/ $表示最后一行
:1,$ s/root/aaron/g = :% s/root/aaron/g  %表示全文 g表示全局
:% s#/dev/sda#/var/ccc#g
:,8 s/root/aaron/ 从当前行到第8行
:4,9 s/^#//       4-9行的开头#替换为空
:5,10 s/.*/#&/ 5-10前加入#字符(.*整行，&引用查找的内容)
```

读写另存为

```
:w 存储到当前文件
:w /tmp/aaa.txt 另存为/tmp/aaa.txt
:1,3 w /tmp/2.txt 将1-3行保存到文件
:r /etc/hosts 读入文件到当前行后
:5 r /etc/hosts 读入文件到第5行后
```

临时设置

```
行号
显示：set number，简写为set nu
取消显示：set nonumber，简写为set nonu
括号匹配
匹配：set showmatch，简写为set sm
取消：set nosm
自动缩进
启用：set ai
禁用：set noai
高亮搜索
启用：set hlsearch
禁用：set nohlsearch
语法高亮
启用：syntax on
禁用：syntax off
忽略字符的大小写
启用：set ic
不忽略：set noic
获取帮助
: help
```

永久环境编辑

```
/etc/vimrc 影响所有系统用户
~/.vimrc 影响某一个用
```

### 文件时间

```
[root@localhost ~]# stat tes
  File: ‘tes’
  Size: 0         	Blocks: 0          IO Block: 4096   regular empty file
Device: fd00h/64768d	Inode: 33628299    Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Context: unconfined_u:object_r:admin_home_t:s0
Access: 2022-07-02 20:02:26.498148847 +0800		atime 访问时间
Modify: 2022-07-02 20:02:26.498148847 +0800		mtime 修改时间,修改内容
Change: 2022-07-02 20:02:26.498148847 +0800		ctime 修改文件属性，权限等
 Birth: -
时间戳： 1970年1月1日0点0分到现在的秒数
```

## Linux文件类型

***通过颜色判断文件的类型是不一定正确的 !!!!***
***Linux系统中文件是没有扩展名的!!!!***

*** linux中一切皆文件 ***

### 方法一

```shell
ls -l 文件名 //看第一个字符
    - 普通文件(文本文档，二进制文件，压缩文件，电影，图片。。。)
    d 目录文件(蓝色)
    b 块设备文件(块设备)存储设备硬盘，U盘 /dev/sda,/dev/sda1
    c  字符设备文件(字符设备)打印机，终端 /dev/tty1,/dev/zero
    s 套接字文件
    p 管道文件 fifo
    l 链接文件(淡蓝色)
```

### 方法二

file

### 方法三

stat

## 查找文件

which会在环境变量中查找$PATH

locate在数据库中查找，将文件路径放进数据库

```
yum install -y mlocate
updatedb 
find 也可以
```

#### man

```
man -k keyword
[root@localhost ~]# man -k sort
comm (1)             - compare two sorted files line by line
grub2-rpm-sort (8)   - Sort input according to RPM version compare.
sort (1)             - sort lines of text files
sort (3pm)           - perl pragma to control sort() behaviour
tsort (1)            - perform topological sort

SORT(1)                                                          User Commands                                                         SORT(1)

NAME
       sort - sort lines of text files

SYNOPSIS
       sort [OPTION]... [FILE]...
       sort [OPTION]... --files0-from=F

DESCRIPTION
       Write sorted concatenation of all FILE(s) to standard output.

SORT(1)                                                          User Commands                                                         SORT(1)

NAME
       sort - sort lines of text files

SYNOPSIS
       sort [OPTION]... [FILE]...
       sort [OPTION]... --files0-from=F

DESCRIPTION
       Write sorted concatenation of all FILE(s) to standard output.

       Mandatory arguments to long options are mandatory for short options too.  Ordering options:

       -b, --ignore-leading-blanks
              ignore leading blanks

       -d, --dictionary-order
              consider only blanks and alphanumeric characters

       -f, --ignore-case
              fold lower case to upper case characters

```

#### info手册

`indo command`

