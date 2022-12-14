# day20

#### 通配符

| 符号 | 作用         |
| ---- | ------------ |
| *    | 所有         |
| ?    | 任意一个字符 |
| {}   | 生成序列     |
| []   | [a-z]        |
| [^]  | 排除         |

* 通配符*

```
*.txt  *.log
```

* 通配符{}

```
file{01..100}
备份一个文件
cp abc{,.bak}
```

#### 特殊符号

| 引号     |                                                    |
| -------- | -------------------------------------------------- |
| 单引号   | 所见即所得，引号内的内容原封不动的输出（多数命令） |
| 双引号   | 与单引号类似，但是特殊符号会被解析运行             |
| 不加引号 | 与双引号类似，支持通配符                           |
| 反引号   | 优先执行反引号里面的命令                           |

#### 正则表达式（regular expression   RE)

* 匹配字符：手机号，身份证号，邮箱
* 主要匹配字符（三剑客）
* 与通配符不同

* 基础正则   BRE
* 扩展正则 ERE

| 正则分类 |                               |
| -------- | ----------------------------- |
| 基础正则 | ^  $  ^$  .  *  .*   []   [^] |
| 扩展正则 | \|   +   {}   （）  ？        |

#### 通配符和正则表达式区别

| 区别       | 处理目标                                 | 支持命令                    |
| ---------- | ---------------------------------------- | --------------------------- |
| 通配符     | 文件，目录  处理的是参数                 | linux大部分支持             |
| 正则表达式 | 进行过滤，在文件中查找内容，处理的是字符 | linux三剑客，python，Golang |

#### 基础正则BRE

```
以my开头的
grep '^my' a.txt
以448结尾的内容
grep '448$' a.txt
cat -A 查看文件结尾的空格
匹配空行(没有，空格也没有)
grep '^$'
grep -n 增加行号
grep -v 排除

\ 转义字符：去掉特殊含义
以.结尾
grep '\.$' 
```

| 转义字符系列 |               |
| ------------ | ------------- |
| \            | 去掉特殊含义  |
| \n           | 换行符        |
| \t           | 制表符，tab键 |

```
echo -e 'html \n saf'
不加-e不支持
```

* 正则表达式*
  * 前一个字符出现0次或者0次以上

```
8
555 出现三次
6666 出现四次
贪婪性：出现尽可能多的匹配
```

* 正则表达式 .*所有字符，不限长度，字符

```
以任意开头，有o
grep '^.*o'
以n开头，.结尾
grep '^n.*\.$'
```

* 正则表达式[]

```bash
[abc]匹配a或者b或者c中任意一个，一次只找到一个
grep '[a-z]'
不区分大小写
grep -i '[a-z]'或者grep '[a-zA-Z]'
```

* 正则表达式[^] \[^abc]  匹配不是b不是a不是c的内容(排除a，b，c)

#### 扩展正则ERE

* 正则 + 前一个字符出现1次或者1次以上
* \将只支持基础正则的支持扩展正则

```
过滤连续出现的数字
grep '[0-9]+'
连续出现的字母
[a-zA-Z]+
```

* 扩展正则| 或者

```
|不同于管道
与[]区别
```

| \|    | 或者 | 单词，字符，长内容 |
| ----- | ---- | ------------------ |
| [a-z] | 或者 | 某个字符           |

* 扩展正则{}  a{n，m} 前一个字符a，出现的次数最少n，最多m

```
匹配出现3次的数字
[0-9]{3}
匹配身份证号
前17为是数字
最后一位是数字或者X
[0-9]{17}[0-9X]$
{,m}
{n,}
```

* 扩展正则（），括起来的相当于一个整体；sed命令中的后向引用（反向引用）

* 扩展正则？ 前一个字符出现一次或者0次

#### 扩展正则（ERE）总结

| ERE  |                            |
| ---- | -------------------------- |
| +    | 前一个字符出现一次或者多次 |
| \|   | 或者会                     |
| {}   | 出现次数                   |
| ()   | 1.整体；2.反向引用         |
| ?    | 前一个字符出现一次或者0次  |

#### 正则总结

出现次数

| +    | 1次或者1次以上           |
| ---- | ------------------------ |
| {}   | 任意次数                 |
| *    | 任意次数                 |
| ？   | 前一个字符出现1次或者0次 |

整体（或者）

| []   | [abc]出现单次 |
| ---- | ------------- |
| ()   |               |
| [^]  |               |
| \|   |               |

```python
常用正则表达式
邮箱的正则表达式：[a-zA-Z0-9._]+@[a-zA-Z0-9._]+\.[a-zA-Z0-9]{1,3}
手机号的正则表达式：^(13[0-9]|14[5-9]|15[0-35-9]|16[25-7]|17[0-8]|18[0-9]|19[0135689])[0-9]{8}$
IP地址的正则表达式：^((\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])\.){3}(\d|[1-9]\d|1\d\d|2[0-4]\d|25[0-5])$
```



***

#### grep 命令选项

| -n               | 显示行号       |
| ---------------- | -------------- |
| -v               | 排除匹配的选项 |
| -i               | 不区分大小写   |
| -o               | 显示执行过程   |
| grep -E == egrep | 扩展正则       |
| -w               | 精确过滤       |

```
精确过滤，只显示过滤的
grep -w 'xz'
查找文件值含有字符的文件
grep 'xz' `find /etc/`:反引号有限执行
简单过滤
grep -R 'xz' /etc
只显示文件名
grep -Rl 'xz' /etc/
```

#### sed命令选项

* 增删改查
* 取行，替换，查找

`sed + 条件 + 指令`

#### 执行流程

* 读取一行，存放至内存
* sed判断是否满足条件
  * 满足，执行指令
  * 不满足，读取下一行
* 默认读取完最后一行结束

#### sed查询

| sed命令格式 | 选项 | ‘找谁干啥’                    | 文件 |
| ----------- | ---- | ----------------------------- | ---- |
|             |      | 3p                            |      |
| -n          |      | 取消默认输出，一般和p一起使用 |      |

```
sed命令默认显示文件所有内容
sed -n '3P' s.txt
显示行数
sed -n '2,4p' xz.txt

模糊查询
sed -n '/xz/p' xz.txt(支持正则，不支持扩展正则 -r)

显示行数 范围
sed -n '/100/,/105/p'  xz.txt

案例
查看11：03:00到11:30:00的日志内容
1.过滤是否存在
grep '11:03:00'
2.sed查找范围
sed -n '/11:03:00/,/11:30:00/p'

wc -l
显示行数


```

#### sed删除

* p  print 显示
* d delete 删除 按行删除

```
扩展正则
删除含有xz和we的
sed -r ‘/xz|we/d’ 

删除多行，范围
sed '2,5d' 
```

#### sed增加

* 类似 >>
* 增加 cia 

```
1. i 在第1行前面插入
sed '2i hello world ' xz.txt

2. a 在第1行后面添加
sed '2a hello world ' xz.txt

3. c 替代，将第2行内容替换，整行
sed '2c hello world' zx


sed -i 修改文件内容，不显示出来
sed -i.bak 修改文件，会提前备份
```

#### sed替换

```
sed 's/目标/替换成what/g'
斜线不限，可以用其他字符替换

删除数字，只删除数字，不删除字母之类的
sed 's/[0-9]//g'


将文件第2行的大写字母替换
sed '2s/[A-Z]//g'
g :满足条件，全部删除；否则只删除第一个

```

#### sed后向引用

```
后面使用前面分组的东西被称为反向引用
sed 命令中使用（）分组，然后在后面使用\n  n为数字，引用分组

使用案例
 123456   变为 <123456>
 echo 123456  | sed -r 's/(.*)/<\1>/g' xz.txt
 
 123456   变成  <1><2><3><4><5><6>
 echo 123456 | sed -r 's/(.)/<\1>/g'  xz.txt
 
 案例
 面试题
 将第一列和最后一列换位置
 sed  's/^[a-z-]:/:(.*):/.*$/\3/\2/\1'
```

![image-20220723190132091](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220723190132091.png)

#### sed总结

```
查询
删除
反向引用
```

#### awk

```
1.执行流程
2.awk取行和取列
3.awk精确过滤
4.awk统计方法
```

* 取行与取列

```
行    记录			    NR
列    字段（域）		 

取行
取第2行
awk 'NR==2' xz.txt

取第2到第5行
awk 'NR<=5 && NR>=2' xz.txt

模糊查询
awk '/dawd/' xz

从包含xz 到we的行
awk '/xz/,/we/'

显示最后一行

```

* 内置规则

| NR   | 记录号（Record Number） |
| ---- | ----------------------- |
| NF   | 列数（number of field)  |
| $0   | 表示整行                |

```
取列 /etc/hosts文件中的信息
默认使用空格分隔
awk '{print $i}'
$NF:最后一列

指定分隔符：
awk -F：文件| column -t对齐

指定多个分隔符
awk -F'[ /]+' '{print $3}'
```

* 精确过滤

```
特殊BEGIN()和END()
```

```
1.过滤包含dd的内容
awk '/dd/'

2.去除文件中第三列以数字1开头的行
awk '{$3} ~/^1/{print $1}'  xz
~：表示包含，支持正则表达式


3.不包含内容
awk '!/dd/'

4.删除文件中的空行(tab键和空格开头)
cat -A，可以在行结尾添加$
awk '!/[	 ]+/'	xz

5./从哪里来/,/到哪里去/
awk '$4~/11:25/,$4~/11:55/'
```

* BEGIN()和END()

| BEGIN() | BEGIN的内容会在awk读取文件前执行           | 使用频率低，测试awk功能 |
| ------- | ------------------------------------------ | ----------------------- |
| END()   | **END**里面的内容会在awk读取完文件之后执行 | 输出结果                |

```
1.显示passwd第1列和第3列减伤name uid
awk -F: 'BEGIN{"name","uid"} {print $1 $3}' passwd

2.统计 /etc/service空行
awk '/^$/' /etc/service |wc -l

awk '/^$/{i++ ;print i}' 
显示多个
awk '/^$/{i++}END{print i}'  

3.求和案例
seq 100 |awk '{sum=sum+$0}{print sum}'

4.统计log中使用的流量
awk '{sum+=$10}END{print sum/1024/1024}'
```

* 博客：[常用正则表达式最强整理（速查手册，记得收藏） (qq.com)](https://mp.weixin.qq.com/s/_kPJeEqXOXnBHzyUVFJ2Gw)
