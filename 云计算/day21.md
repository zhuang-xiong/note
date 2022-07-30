# day21

### shell编程

![img](https://img2.baidu.com/it/u=3604219579,1811084500&fm=253&fmt=auto&app=138&f=PNG?w=321&h=300)

* shell是命令行解释器，接受应用程序/用户命令，然后调用操作系统内核。

```
1.查看shell解释器
[root@localhost ~]# cat /etc/shells 
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
shell解释器
```

* shell脚本初识

```
1.#!/bin/bash
指定当前命令行默认的解析器

2.执行shell脚本
bash + 绝对路径或者相对路径

chmod +x script.sh
script.sh

3.环境变量
$HOME,$SHELL,$PWD
家目录

4.env/printenv
全局变量
查看USER
printenv USER

5.进去子bash，查看ps -f
[root@localhost ~]# ps -f
UID         PID   PPID  C STIME TTY          TIME CMD
root       7449   7447  0 08:35 pts/1    00:00:00 -bash
root       8108   7449  0 14:40 pts/1    00:00:00 bash
root       8135   8108  0 14:48 pts/1    00:00:00 ps -f
[root@localhost ~]# exit
exit
[root@localhost ~]# ps -f
UID         PID   PPID  C STIME TTY          TIME CMD
root       7449   7447  0 08:35 pts/1    00:00:00 -bash
root       8136   7449  0 14:48 pts/1    00:00:00 ps -f
```

* 自定义变量

```
1.定义变量：变量名=变量值，注意，=号前后不能有空格

2.定义全局变量
[root@localhost ~]# export zhangxiaowen
[root@localhost ~]# zhangxiaowen=999
[root@localhost ~]# echo $zhangxiaowen
999

3.设置只读变量
readonly b=5

4.撤销变量，但是不能撤销只读变量
unset

5.echo $PATH,sh文件添加进去可以直接执行

6.特殊变量：$n
$1,${12}
$0：整行


7.$#
获取所有输出参数的个数

8.
$*:这个变量代表命令行中所有的参数，$*把所有参数看做一个整体
$@：这个变量也代表所有参数，不过是把每个参数区分对待

9.$?
表示最后一次执行的命令的返回状态
返回0：正常
返回非0：不正常
```

* 基础运算

```
1.加法运算，空格必须有
expr 1 + 2

2.乘法运算
expr 2 \* 5

3.计算
[atguigu@hadoop101 shells]# S=$[(2+3)*4]
[atguigu@hadoop101 shells]# echo $S
$[5*2]

```

* 条件判断

```
1.test 
[ condition ]
[ 2 = 5 ]
等号前后需要添加空格


2.判断条件
-eq 等于	-ne不等于
-lt 小于	-le小于等于
-gt 大于	-ge大于等于

3.判断文件权限
[ -r text ]
-w
-x
判断文件是否有权限读写执行

-e：判断是否存在
-f:判断是一个文件
-d:判断是目录


4.多条件判断
&&:前一个命令执行成功，执行后面的命令
||:前一个命令执行失败，执行后面的命令

```

* 流程控制

```
if [条件判断];then
程序
fi
```

```
if [条件判断]
then
fi
```

```
if [条件判断]
then
	程序
elif [条件判断]
then
	程序
else
	程序
fi


if 【】
then
	程序
else 
	程序
```

```
-a
逻辑与的关系
-o
逻辑或的关系
```

* for循环

```
for (( 初始值;循环条件;变量变化))
do
	程序
done


双小括号，里面可以之间加数学计算式，不然需要使用-lt

for((i=1;i<=$i;i++))
do
	sum=$[$sum+$i]
done
echo $sum
```

* while循环

```
while [ 条件判断]
do
	程序
done

while[$i -lt $a]
do
	sum=$[$sum + $a]
	a=$[$a + 1]
	
#	sum+=a
#	a+=1
相同效果
done
```

* read 读取控制台输入

```
read (选项) (参数)
①选项：
    -p：指定读取值时的提示符；
    -t：指定读取值时等待的时间（秒）如果-t 不加表示一直等待
②参数
	变量：指定读取值的变量名
```

* 函数

```
时间戳
date + %s
```

* 自定义函数

```
[function] funname[()]
{
	action
	[return int;]
}
```

* 综合案例1：归档文件

```
# 首先判断参数唯一
if [ $# -ne 1]
then
	echo
	echo '参数个数不对'
if
# 判断是否是目录
if [-d $1]
then
	echo
else
	echo 
	echo '不是目录文件'
	exit
fi

# 获取目录和路径
DIR_NAME=$(basename $1)
DIR_PATH=$(cd (dirname $1);pwd)

# 获取当前日期
DATE=$(date + %y%m%d)

# 定义存档文件的名称
FILE=archive_$DIR_NAME_$DATE.tar.gz
DEST=/root/archive/$FILE

# 开始归档文件

echo '开始归档---'
tar -czf $DEST $DIR_PATH/$DIR_NAME

if [ $? -eq 0]
then 
	echo
	echo '归档成功！'
	echo '归档文件：$DEST'
else
	echo '归档错误！'
fi
exit
```

* 博客shell脚本练习题：[shell脚本使用案例_bbj1030的博客-CSDN博客](https://blog.csdn.net/bbj1030/article/details/118020067)
