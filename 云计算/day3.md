# day3

#### 作业控制jobs

* 将任务进行前后台切换
* 控制任务开始或者结束

如果没有作业控制jobs，父进程将fork（）一个子进程，进入sleeping，直到子进程退出。

使用作业控制，可以选择控制暂停，恢复或者异步执行，让shell在子进程期间返回接受其他命令。

foreground:

前台进程，在终端运行，允许从终端读取数据，进行交互。

background:

后台进程，没有与终端的交互。

```
sleep 1000 & 让程序在后台运行
sleep 40000 在前台执行 ^z，将程序暂停 stop
ps aux|grep sleep
只显示未结束的进程
bg %1  让进城1在后台运行
fg %1  让进程1在前台运行
kill %1 让进程1终止 terminal
```

```
[root@xwz ~]# (while :; do date; sleep 2;done) & # 输出在前台
[root@xwz ~]# (while :; do date; sleep 2;done) &>/dev/null & # 无输出内容
```

##### 管理远程主机

```
yum install -y screen
screen 开启一个新终端
screen -S 'test' 创建一个test终端
screen -list 列出所有终端
screen -r 切换终端
ctrl +a+d 离开终端
screen -X -S name quit 删除终端（包括attached）
```

#### 存储结构和磁盘划分

| 目录名称    | 应放置文件的内容                                          |
| ----------- | :-------------------------------------------------------- |
| /boot       | 开机所需文件——内核、开机菜单以及所需配置文件等等          |
| /dev        | 以文件形式存放任何设备与接口                              |
| /etc        | 配置文件                                                  |
| /home       | 用户家目录                                                |
| /bin        | 存放单用户模式下还可以操作的命令                          |
| /lib        | 开机时用到的函数库，以及/bin与/sbin下面的命令要调用的函数 |
| /sbin       | 开机过程中需要的命令                                      |
| /media      | 用于挂载设备文件的目录，可移除设备                        |
| /opt        | 放置第三方的软件                                          |
| /root       | 系统管理员的家目录                                        |
| /srv        | 一些网络服务的数据文件目录                                |
| /tmp        | 任何人均可使用的“共享”临时目录，定期清除                  |
| /proc       | 虚拟文件系统，例如系统内核、进程、外部设备及网络状态等    |
| /usr/local  | 用户自行安装的软件                                        |
| /usr/sbin   | Linux系统开机时不会使用到的软件/命令/脚本                 |
| /usr/share  | 帮助与说明文件，也可放置共享文件                          |
| /var        | 主要存放经常变化的文件，如日志，但是不会定期清除          |
| /lost+found | 当文件系统发生错误时，将一些丢失的文件片段存放在这里      |

一般硬盘的命名规则是以“/dev/sd"开始，后面接数字

* 主分区或者扩展分区从1开始，4结束
* 逻辑分区从5开始

#### 文件系统和数据资料

文件系统是合理规划硬盘，保证用户正常的使用需求。

| 文件系统 | 描 述                                                        |
| -------- | :----------------------------------------------------------- |
| Ext      | Linux 中最早的文件系统，由于在性能和兼容性上具有很多缺陷，现在已经很少使用 |
| Ext2     | 是 Ext 文件系统的升级版本，Red Hat Linux 7.2 版本以前的系统默认都是 Ext2 文件系统。于 1993 年发布，支持最大 16TB 的分区和最大 2TB 的文件（1TB=1024GB=1024x1024KB) |
| Ext3     | 是 Ext2 文件系统的升级版本，最大的区别就是带日志功能，以便在系统突然停止时提高文件系统的可靠性。支持最大 16TB 的分区和最大 2TB 的文件。是一款日志文件系统，能够在系统异常宕机时避免文件系统资料丢失，并能自动修复数据 的不一致与错误。然而，当硬盘容量较大时，所需的修复时间也会很长，而且也不能百分 之百地保证资料不会丢失。它会把整个磁盘的每个写入动作的细节都预先记录下来，以便 在发生异常宕机后能回溯追踪到被中断的部分，然后尝试进行修复。 |
| Ext4     | 是 Ext3 文件系统的升级版。Ext4 在性能、伸缩性和可靠性方面进行了大量改进。Ext4 的变化可以说是翻天覆地的，比如向下兼容 Ext3、最大 1EB 文件系统和 16TB 文件、无限数量子目录、Extents 连续数据块 概念、多块分配、延迟分配、持久预分配、快速 FSCK、日志校验、无日志模式、在线碎片整理、inode 增强、默认启用 barrier 等。它是 CentOS 6.3 的默认文件系统 |
| xfs      | 被业界称为最先进、最具有可升级性的文件系统技术，由 SGI 公司设计，目前最新的 CentOS 7 版本默认使用的就是此文件系统。 |
| swap     | swap 是 Linux 中用于交换分区的文件系统（类似于 Windows 中的虚拟内存)，当内存不够用时，使用交换分区暂时替代内存。一般大小为内存的 2 倍，但是不要超过 2GB。它是 Linux 的必需分区 |
| NFS      | NFS 是网络文件系统（Network File System）的缩写，是用来实现不同主机之间文件共享的一种网络服务，本地主机可以通过挂载的方式使用远程共享的资源 |
| iso9660  | 光盘的标准文件系统。Linux 要想使用光盘，必须支持 iso9660 文件系统 |
| fat      | 就是 Windows 下的 fatl6 文件系统，在 Linux 中识别为 fat      |
| vfat     | 就是 Windows 下的 fat32 文件系统，在 Linux 中识别为 vfat。支持最大 32GB 的分区和最大 4GB 的文件 |
| NTFS     | 就是 Windows 下的 NTFS 文件系统，不过 Linux 默认是不能识别 NTFS 文件系统的，如果需要识别，则需要重新编译内核才能支持。它比 fat32 文件系统更加安全，速度更快，支持最大 2TB 的分区和最大 64GB 的文件 |
| ufs      | Sun 公司的操作系统 Solaris 和 SunOS 所采用的文件系统         |
| proc     | Linux 中基于内存的虚拟文件系统，用来管理内存存储目录 /proc   |
| sysfs    | 和 proc —样，也是基于内存的虚拟文件系统，用来管理内存存储目录 /sysfs |
| tmpfs    | 也是一种基于内存的虚拟文件系统，不过也可以使用 swap 交换分区 |

##### 格式化之后发生的事情





#### 挂在硬件设备

#### mount（临时挂载）

挂载是在使用硬件设备前所执行的最后一步操作。只需使用 mount 命令把硬盘设备或分区与一个目录文件进行关联，然后就能在这个目录中看到硬件设备中的数据了。

对于比较新的 Linux 系统来讲，一般不需要使用 -t 参数来指定文件系统的类型， Linux 系统会自动进行判断。

而 mount 中的 -a 参数则厉害了，它会在执行后自动检查 /etc/fstab 文件中有无疏漏被挂载的设备文件，如果有，则进行自动挂载操作。

| 参数 | 作用             |
| ---- | ---------------- |
| -a   | 自动挂载         |
| -t   | 指定文件管理类型 |

```
lbslk
查看所有挂载的设备
[root@localhost ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part 
  ├─centos-root 253:0    0   17G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0    6G  0 disk 
└─sdb1            8:17   0    2G  0 part /mnt/mou
sr0              11:0    1 1024M  0 rom 

mkdir xz 创建挂载目录
mount /dev/sda（硬件设备） /root/xz（挂载位置）
```

#### 永久挂载

```
[root@localhost ~]# cat /etc/fstab #将信息写入/etc/fstab文件

# /etc/fstab
# Created by anaconda on Sat Jul  2 18:47:41 2022
#
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
/dev/mapper/centos-root /                       xfs     defaults        0 0
UUID=fb828aca-6f65-41ea-a81f-3a21ed7af212 /boot                   xfs     defaults        0 0
/dev/mapper/centos-swap swap                    swap    defaults        0 0
/dev/sdb1 /mnt/mou  ext4 rw,seclabel,relatime,quota,usrquota,grpquota,data=ordered 0 0
设备路径（或者UUID)    挂载目录    文件系统格式	权限选项	是否备份	是否自检
```

#### umount

```
umount /dev/sda(硬件设备位置)
```

#### 添加硬盘设备

| 参数 | 作用               |
| ---- | ------------------ |
| m    | 查看所有参数       |
| n    | 添加新的分区       |
| d    | 删除某个分区信息   |
| l    | 列出所有可用的分区 |
| t    | 更改分区类型       |
| p    | 查看分区情况       |
| w    | 退出并保存         |
| q    | 不保存直接退       |

```
fdisk /dev/sdb
[root@server ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。
更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。
Device does not contain a recognized partition table
使用磁盘标识符 0x5d679c57 创建新的 DOS 磁盘标签。
命令(输入 m 获取帮助)：n
Partition type:
p primary (0 primary, 0 extended, 4 free)
e extended
Select (default p):
Using default response p
分区号 (1-4，默认 1)：
起始 扇区 (2048-10485759，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-10485759，默认为 10485759)：+2G
分区 1 已设置为 Linux 类型，大小设为 2 GiB
命令(输入 m 获取帮助)：w
The partition table has been altered!
Calling ioctl() to re-read partition table.
正在同步磁盘。
[root@server ~]# partprobe
Warning: 无法以读写方式打开 /dev/sr0 (只读文件系统)。/dev/sr0 已按照只读方式打开。
[root@server ~]# lsblk
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda 8:0 0 20G 0 disk
├─sda1 8:1 0 1G 0 part /boot
└─sda2 8:2 0 19G 0 part
├─centos-root 253:0 0 17G 0 lvm /
└─centos-swap 253:1 0 2G 0 lvm [SWAP]
sdb 8:16 0 5G 0 disk
└─sdb1 8:17 0 2G 0 part
sr0 11:0 1 792M 0 rom
```

#### 创建磁盘分区步骤

* fdisk磁盘分区
* mdfs，设置文件系统
* mount挂载

#### du

查看文件数据占用量

`du [命令] [文件]`

```
du -sh /*
查看/目录下所有文件占用率
```

#### 添加交换分区

SWAP（交换）分区是一种通过在硬盘中预先划分一定的空间，然后将把内存中暂时不常用的数据临时 存放到硬盘中，以便腾出物理内存空间让更活跃的程序服务来使用的技术

```
umount /dev/sdb
mkswap /dev/sdb	#创建交换分区
swapon /dev/sdb #打开交换分区
free -h #查看交换分区的信息
```

### 磁盘容量配额

* 创建分区

```
[root@server ~]# fdisk /dev/sdb
欢迎使用 fdisk (util-linux 2.23.2)。
更改将停留在内存中，直到您决定将更改写入磁盘。
使用写入命令前请三思。
Device does not contain a recognized partition table
使用磁盘标识符 0x5d679c57 创建新的 DOS 磁盘标签。
命令(输入 m 获取帮助)：n
Partition type:
p primary (0 primary, 0 extended, 4 free)
e extended
Select (default p):
Using default response p
分区号 (1-4，默认 1)：
起始 扇区 (2048-10485759，默认为 2048)：
将使用默认值 2048
Last 扇区, +扇区 or +size{K,M,G} (2048-10485759，默认为 10485759)：+2G
分区 1 已设置为 Linux 类型，大小设为 2 GiB
命令(输入 m 获取帮助)：w
The partition table has been altered!
Calling ioctl() to re-read partition table.
正在同步磁盘。
[root@server ~]# partprobe
Warning: 无法以读写方式打开 /dev/sr0 (只读文件系统)。/dev/sr0 已按照只读方式打开。
[root@server ~]# lsblk
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda 8:0 0 20G 0 disk
├─sda1 8:1 0 1G 0 part /boot
└─sda2 8:2 0 19G 0 part
├─centos-root 253:0 0 17G 0 lvm /
└─centos-swap 253:1 0 2G 0 lvm [SWAP]
sdb 8:16 0 5G 0 disk
└─sdb1 8:17 0 2G 0 part
sr0 11:0 1 792M 0 rom
```

* 格式化文件系统

```
[root@server ~]# mkfs.ext4 /dev/sdb1
mke2fs 1.42.9 (28-Dec-2013)
文件系统标签=
OS type: Linux
块大小=4096 (log=2)
分块大小=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
131072 inodes, 524288 blocks
26214 blocks (5.00%) reserved for the super user
第一个数据块=0
Maximum filesystem blocks=536870912
16 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912
Allocating group tables: 完成
正在写入inode表: 完成
Creating journal (16384 blocks): 完成
Writing superblocks and filesystem accounting information: 完成
```

* 创建目录并且挂载

```
[root@server ~]# mkdir /mnt/mountpoint1
[root@server ~]# mount /dev/sdb1 /mnt/mountpoint1/
[root@server ~]# df -TH
文件系统 类型 容量 已用 可用 已用% 挂载点
/dev/mapper/centos-root xfs 19G 1.2G 18G 7% /
devtmpfs devtmpfs 945M 0 945M 0% /dev
tmpfs tmpfs 956M 0 956M 0% /dev/shm
tmpfs tmpfs 956M 9.1M 947M 1% /run
tmpfs tmpfs 956M 0 956M 0% /sys/fs/cgroup
/dev/sda1 xfs 1.1G 150M 915M 15% /boot
tmpfs tmpfs 192M 0 192M 0% /run/user/0
/dev/sdb1 ext4 2.1G 6.3M 2.0G 1% /mnt/mountpoint1
```

* 准备用户

```
[root@server ~]# groupadd usergrp
[root@server ~]# useradd -g usergrp -b /mnt/mountpoint1/ user1
[root@server ~]# useradd -g usergrp -b /mnt/mountpoint1/ user2
[root@server ~]# useradd -g usergrp -b /mnt/mountpoint1/ user3
[root@server ~]# useradd -g usergrp -b /mnt/mountpoint1/ user4
[root@server ~]# useradd -g usergrp -b /mnt/mountpoint1/ user5
```

* 确保文件系统支持

```
检查挂载点并且是否支持quota
[root@server ~]# mount | grep mountpoint1
/dev/sdb1 on /mnt/mountpoint1 type ext4 (rw,relatime,data=ordered)
重新挂载，让文件系统支持quota配置
[root@server ~]# mount -o remount,usrquota,grpquota /mnt/mountpoint1/
[root@server ~]# mount | grep mountpoint1
/dev/sdb1 on /mnt/mountpoint1 type ext4
(rw,relatime,quota,usrquota,grpquota,data=ordered)
确保开机自动支持quota配置
 [root@server ~]# tail -n 1 /etc/mtab >> /etc/fstab

```

* 安装quota

```
-a：扫描所有在/etc/mtab内含有quota参数的文件系统
-u：针对用户扫描文件与目录的使用情况，会新建一个aquota.user文件
-g：针对用户组扫描文件与目录的使用情况，会新增一个aquota.group文件
-v：显示扫描过程的信息
[root@server ~]# yum install quota -y
```

* 开启quota

```
[root@server mountpoint1]# quotacheck -avug
[root@server mountpoint1]# quotaon -avug
/dev/sdb1 [/mnt/mountpoint1]: group quotas turned on
/dev/sdb1 [/mnt/mountpoint1]: user quotas turned on
```

* 设置配额

````
[root@server mountpoint1]# edquota -u user1
[root@server mountpoint1]# edquota -p user1 -u user2
[root@server mountpoint1]# edquota -t
设置宽限时间
[root@server mountpoint1]# repquota -as
*** Report for user quotas on device /dev/sdb1
Block grace time: 7days; Inode grace time: 7days
Space limits File limits
User used soft hard grace used soft hard grace
----------------------------------------------------------------------
root -- 20K 0K 0K 2 0 0
user1 -- 16K 245M 293M 4 0 0
user2 -- 16K 245M 293M 4 0 0
user3 -- 16K 0K 0K 4 0 0
user4 -- 16K 0K 0K 4 0 0
user5 -- 16K 0K 0K 4 0 0
````

* 测试

```
[user1@server ~]$ dd if=/dev/zero of=bigfile bs=10M count=50
sdb1: warning, user block quota exceeded.
sdb1: write failed, user block limit reached.
dd: 写入"bigfile" 出错: 超出磁盘限额
记录了30+0 的读入
记录了29+0 的写出
307183616字节(307 MB)已复制，1.96853 秒，156 MB/秒
[user1@server ~]$ du -sh *
293M bigfile
[user1@server ~]$
```

博客地址：`https://www.cnblogs.com/sddai/p/11111895.html`

```
quota命令还有软限制和硬限制的功能
    软限制：当达到软限制时会提示用户，但仍允许用户在限定的额度内继续使用。
    硬限制：当达到硬限制时会提示用户，且强制终止用户的操作。	
```

### 软硬方式连接

*****

补充概念等

****

#### 压缩和归档

`tar`

gzip命令只压缩单个文件，要压缩和归档多个文件和目录，可以使用tar命令

```
压缩文件
tar cvf <name>.tar fiel fiel1
解压文件
tar xvf <name>.tar
 tar命令解压后并不会删除归档文件。
 
```



#### ln

`ln [选项] 目标`

| 参数 | 作用                                   |
| ---- | -------------------------------------- |
| -s   | 创建软连接（没有参数，默认创建硬链接） |
| -f   | 强制创建文件或者目录的链接             |
| -i   | 覆盖前询问                             |
| -v   | 显示创建过程                           |

```
[root@localhost ~]# ln -s file file156845
[root@localhost ~]# ll | grep file156
lrwxrwxrwx. 1 root root    4 Jul 17 04:23 file156845 -> file
```

### RAID(独立冗余磁盘阵列)

#### RAID 0

![img](http://img.blog.itpub.net/blog/2020/05/13/00d98d9893a83196.png?x-oss-process=style/bb)

```
如果说需要存储的数据是12345678，那么可能是1357存储在disk1上面，2468存储在disk2上面
读取速率得到一定提高
没有数据冗余性
最少需要两块硬盘组合
有效存储空间为N块盘
```



##### RAID1

![img](http://img.blog.itpub.net/blog/2020/05/13/5180d6346797a697.png?x-oss-process=style/bb)

```
如果需要存储1357四份数据，那么会将同样的数据备份写入不同的盘中
读写速率没有得到提升
具有数据冗余性
最少需要两块磁盘
有效存储空间为N/2块磁盘
```

#### RAID5

![img](http://img.blog.itpub.net/blog/2020/05/13/34920815c39492e7.png?x-oss-process=style/bb)

```
具有数据安全性
可以提高读写性能
最少需要三块盘
有效存储空间为N-1块磁盘
```



#### RAID10

![img](http://img.blog.itpub.net/blog/2020/05/13/04b6c719ec86f846.png?x-oss-process=style/bb)

```
具有数据冗余性
提高数据读写速率
有效存储空间为N/2块磁盘
```

#### 案例

##### raid10

| 参数 | 作用                  |
| ---- | --------------------- |
| -a   | 检测设备名称 添加磁盘 |
| -n   | 指定设备数量          |
| -l   | 指定RAID级别          |
| -C   | 创建                  |
| -v   | 显示过程              |
| -f   | 模拟设备损坏          |
| -r   | 移除设备              |
| -Q   | 查看摘要信息          |
| -D   | 查看详细信息          |
| -S   | 停止RAID磁盘阵列      |

```
yum install -y mdadm
[root@localhost ~]# mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sdb /dev/sdc
/dev/sdd /dev/sde
mdadm: layout defaults to n2
mdadm: layout defaults to n2
mdadm: chunk size defaults to 512K
mdadm: size set to 20954112K
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.

将RAID磁盘格式化为ext4
[root@localhost ~]# mkfs.ext4 /dev/md0
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
2621440 inodes, 10477056 blocks
523852 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2157969408
320 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
4096000, 7962624
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

创建挂载点然后把硬盘设备进行挂载操作
[root@localhost ~]# mkdir /RAID
[root@localhost ~]# mount /dev/md0 /RAID/
[root@localhost ~]# df -h
Filesystem Size Used Avail Use% Mounted on
/dev/mapper/centos-root 17G 1.1G 16G 7% /
devtmpfs 898M 0 898M 0% /dev
tmpfs 910M 0 910M 0% /dev/shm
tmpfs 910M 9.6M 901M 2% /run
tmpfs 910M 0 910M 0% /sys/fs/cgroup
/dev/sda1 1014M 146M 869M 15% /boot
tmpfs 182M 0 182M 0% /run/user/0
/dev/md0 40G 49M 38G 1% /RAID

查看/dev/md0磁盘阵列的详细信息，并把挂载信息写入到配置文件中，使其永久生效。
[root@localhost ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Mon Apr 15 17:43:04 2019
Raid Level : raid10
Array Size : 41908224 (39.97 GiB 42.91 GB)
Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
Raid Devices : 4
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Mon Apr 15 17:44:14 2019
State : active, resyncing
Active Devices : 4
Working Devices : 4
Failed Devices : 0
Spare Devices : 0
Layout : near=2
Chunk Size : 512K
Consistency Policy : resync
Resync Status : 66% complete
Name : localhost.localdomain:0 (local to host
localhost.localdomain)
UUID : 9b664b1c:ab2b2fb6:6a00adf6:6167ad51
Events : 15
Number Major Minor RaidDevice State
0 8 16 0 active sync set-A /dev/sdb
1 8 32 1 active sync set-B /dev/sdc
2 8 48 2 active sync set-A /dev/sdd
3 8 64 3 active sync set-B /dev/sde
[root@localhost ~]# echo "/dev/md0 /RAID ext4 defaults 0 0" >> /etc/fstab
```

#### 磁盘损坏与修复

在确认有一块物理硬盘设备出现损坏而不能继续正常使用后，应该使用mdadm命令将其移除，然后查 看RAID磁盘阵列的状态，可以发现状态已经改变。

```
[root@localhost ~]# mdadm /dev/md0 -f /dev/sdb
mdadm: set /dev/sdb faulty in /dev/md0
[root@localhost ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Thu Apr 18 09:25:38 2019
Raid Level : raid10
Array Size : 41908224 (39.97 GiB 42.91 GB)
Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
Raid Devices : 4
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Thu Apr 18 09:28:24 2019
State : clean, degraded
Active Devices : 3
Working Devices : 3
Failed Devices : 1
Spare Devices : 0
Layout : near=2
Chunk Size : 512K
Consistency Policy : resync
Name : localhost.localdomain:0 (local to host
localhost.localdomain)
UUID : 09c273ff:77fa1bc2:84a934f1:b924fa6e
Events : 27
Number Major Minor RaidDevice State
- 0 0 0 removed
1 8 32 1 active sync set-B /dev/sdc
2 8 48 2 active sync set-A /dev/sdd
3 8 64 3 active sync set-B /dev/sde
0 8 16 - faulty /dev/sdb
```

在RAID 10级别的磁盘阵列中，当RAID 1磁盘阵列中存在一个故障盘时并不影响RAID 10磁盘阵列的使 用。当购买了新的硬盘设备后再使用mdadm命令来予以替换即可，在此期间我们可以在/RAID目录中正 常地创建或删除文件。由于我们是在虚拟机中模拟硬盘，所以先重启系统，然后再把新的硬盘添加到 RAID磁盘阵列中。

```
[root@localhost ~]# umount /RAID/
[root@localhost ~]# mdadm /dev/md0 -a /dev/sdb
mdadm: added /dev/sdb
[root@localhost ~]# mdadm -D /dev/md0
    /dev/md0:
    Version : 1.2
    Creation Time : Thu Apr 18 09:25:38 2019
    Raid Level : raid10
    Array Size : 41908224 (39.97 GiB 42.91 GB)
    Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
    Raid Devices : 4
    Total Devices : 4
    Persistence : Superblock is persistent
    Update Time : Thu Apr 18 09:34:14 2019
    State : clean, degraded, recovering
    Active Devices : 3
    Working Devices : 4
    Failed Devices : 0
    Spare Devices : 1
    Layout : near=2
    Chunk Size : 512K
    Consistency Policy : resync
    Rebuild Status : 30% complete
    Name : localhost.localdomain:0 (local to host
    localhost.localdomain)
    UUID : 09c273ff:77fa1bc2:84a934f1:b924fa6e
    Events : 47
    Number Major Minor RaidDevice State
    4 8 16 0 spare rebuilding /dev/sdb
    1 8 32 1 active sync set-B /dev/sdc
    2 8 48 2 active sync set-A /dev/sdd
    3 8 64 3 active sync set-B /dev/sde
[root@localhost ~]# mount -a
```

#### 磁盘阵列+备份盘

```
部署RAID 5磁盘阵列时，至少需要用到3块硬盘，还需要再加一块备份硬盘，所以总计需要在虚拟机中
模拟4块硬盘设备
现在创建一个RAID 5磁盘阵列+备份盘。在下面的命令中，参数-n 3代表创建这个RAID 5磁盘阵列所需的
硬盘数，参数-l 5代表RAID的级别，而参数-x 1则代表有一块备份盘。当查看/dev/md0（即RAID 5磁盘
阵列的名称）磁盘阵列的时候就能看到有一块备份盘在等待中了。
```

```
[root@localhost ~]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdb /dev/sdc
/dev/sdd /dev/sde
mdadm: layout defaults to left-symmetric
mdadm: layout defaults to left-symmetric
mdadm: chunk size defaults to 512K
mdadm: /dev/sdb appears to be part of a raid array:
level=raid10 devices=4 ctime=Thu Apr 18 09:25:38 2019
mdadm: /dev/sdc appears to be part of a raid array:
level=raid10 devices=4 ctime=Thu Apr 18 09:25:38 2019
mdadm: /dev/sdd appears to be part of a raid array:
level=raid10 devices=4 ctime=Thu Apr 18 09:25:38 2019
mdadm: /dev/sde appears to be part of a raid array:
level=raid10 devices=4 ctime=Thu Apr 18 09:25:38 2019
mdadm: size set to 20954112K
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
[root@localhost ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Thu Apr 18 09:39:26 2019
Raid Level : raid5
Array Size : 41908224 (39.97 GiB 42.91 GB)
Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
Raid Devices : 3
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Thu Apr 18 09:40:28 2019
State : clean
Active Devices : 3
Working Devices : 4
Failed Devices : 0
Spare Devices : 1
Layout : left-symmetric
Chunk Size : 512K
Consistency Policy : resync
Name : localhost.localdomain:0 (local to host
localhost.localdomain)
UUID : f72d4ba2:1460fae8:9f00ce1f:1fa2df5e
Events : 18
Number Major Minor RaidDevice State
0 8 16 0 active sync /dev/sdb
1 8 32 1 active sync /dev/sdc
4 8 48 2 active sync /dev/sdd
3 8 64 - spare /dev/sde

将部署好的RAID 5磁盘阵列格式化为ext4文件格式，然后挂载到目录上，之后就可以使用了。
[root@localhost ~]# mkfs.ext4 /dev/md0
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=128 blocks, Stripe width=256 blocks
2621440 inodes, 10477056 blocks
523852 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2157969408
320 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
4096000, 7962624
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
[root@localhost ~]# echo "/dev/md0 /RAID ext4 defaults 0 0" >> /etc/fstab
[root@localhost ~]# mkdir /RAID
[root@localhost ~]# mount -a

把硬盘设备/dev/sdb移出磁盘阵列，然后迅速查看/dev/md0磁盘阵列的状态
[root@localhost ~]# mdadm /dev/md0 -f /dev/sdb
mdadm: set /dev/sdb faulty in /dev/md0
[root@localhost ~]# mdadm -D /dev/md0
/dev/md0:
Version : 1.2
Creation Time : Thu Apr 18 09:39:26 2019
Raid Level : raid5
Array Size : 41908224 (39.97 GiB 42.91 GB)
Used Dev Size : 20954112 (19.98 GiB 21.46 GB)
Raid Devices : 3
Total Devices : 4
Persistence : Superblock is persistent
Update Time : Thu Apr 18 09:51:48 2019
State : clean, degraded, recovering
Active Devices : 2
Working Devices : 3
Failed Devices : 1
Spare Devices : 1
Layout : left-symmetric
Chunk Size : 512K
Consistency Policy : resync
Rebuild Status : 40% complete
Name : localhost.localdomain:0 (local to host
localhost.localdomain)
UUID : f72d4ba2:1460fae8:9f00ce1f:1fa2df5e
Events : 26
Number Major Minor RaidDevice State
3 8 64 0 spare rebuilding /dev/sde
1 8 32 1 active sync /dev/sdc
4 8 48 2 active sync /dev/sdd
0 8 16 - faulty /dev/sdb
```

****

**实验没做**

***

### LVM（逻辑卷管理器）

