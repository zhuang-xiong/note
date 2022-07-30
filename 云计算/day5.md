# day5

### 防火墙分类：

* 逻辑分类：主机防火墙（个人） 、网络防火墙（集体）

* 物理分类：硬件防火墙、软件防火墙

## IPTABLES

```
客户端，将用户安全设定执行到“安全框架 --netfilter"
内核空间 netfilter
用户空间 iptables
```

* 原理：IPtables根据rule匹配处理数据包（放行（accept）、拒绝（reject）、丢弃（drop）等）

* 网卡驱动工作在内核空间，可以通过Iptables和netfilter在内核空间设置关卡

  ```
  其实Iptables服务不是真正的防火墙，只是用来定义防火墙规则功能的"防火墙管理工具"，将定义好的规
  则交由内核中的netfilter即网络过滤器来读取，从而真正实现防火墙功能。
  ```

  ## \* 五链四表

![image-20220702185941000](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702185941000.png)

### 五链

FORWARD：处理转发的数据包

INPUT:入站的数据包

OUTPUT：出站的数据包

PREROUTING：判断转发之前进行的规则   (DNAT/REDIRECT)

POSTROUTING：判断转发之后进行的规则  (SNAT/MASQUEADE)

### 报文流向

* 到本地的某个进程：PREROUTING  -->INPUT -->OUTPUT -->POSTROUTING
* 由本地转发的进程：PREROUTING -->FORWORD --> POSTROUTING
* 本地进程发出：OUTPUT --->POSTROUTING

### 四表

* PREROUTING：raw/mangle/nat
* INPUT：mangle/filter/nat
* OUTPUT：raw/mangle/nat/filter
* POSTROUTING：mangle/nat
* FORWARD：mangle/filter

### 忽略点

```
默认关闭路由转发功能：
echo 1 > /proc/sys/net/ipv4/ip_forward
```

![image-20220702191621445](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702191621445.png)

## iptables 用法

```
1.查看链的规则
IPtables -t 表名 -xvL 链名
2.清除链中所有规则
iptables -t 表名 -F 链名
3.增加规则
iptables -A 链名 -s 源ip地址 -j 匹配命令
4.删除规则
①iptables -t 表名 -D 链名 5
②iptables -t 表名 -D 链名 匹配条件 执行动作
5.--line-numbers
显示序号
6.修改单个规则
iptables -t filter -R INPUT  数字  -s ip地址 -j 执行命令
7.修改默认执行动作
iptables -t 表名 -P 链名 执行动作
8保存与恢复
iptables-save /etc/sysconfig/iptables
iptables-restore < /etc/sysconfig/iptables
9.转发本地端口（不理解）
 iptables -t nat -A PREROUTING -p tcp --dport 6666 -j REDIRECT --to-port 22
10.自定义链
iptables -t filter -N http_chain(链名)
11.应用自定义链
iptables -I INPUT -p tcp --dport 80 -j http_chain
12.创建规则（和五链使用方法一样）
iptables -t filter -A http_chain -s 192.168.80.1 -j REJECT
13.删除引用
iptables -t filter -D INPUT -p tcp --dport 80 -j
http_chain
14.删除链上所有规则
iptables -F http_chain
15.删除链
iptables -X http_chain
```

![image-20220702192040421](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702192040421.png)

![image-20220702192429603](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702192429603.png)

![image-20220702193703644](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702193703644.png)

![image-20220702194209525](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220702194209525.png)

## firewalld

```
1.查看当前所在的区域
firewall-cmd --get-default-zone
2.查看网卡所在firewall中的区域
firewall-cmd --get-zone-of-interface=ens33(网卡接口)
3.设置当前服务的区域
firewall-cmd --set-default-zone=public
4.查询当前区域（公共区）是否允许服务
firewall-cmd --zone=public --query-service=ssh
5.添加https协议
firewall-cmd --add-service=https
firewall-cmd --reload（重载）
6.富规则
firewall-cmd --permanent --zone=public --add-rich-
rule="rule \
family="ipv4" \
source address="192.168.91.0/24" \
service name="ssh" \
reject"
7.防火墙关闭
systemctl stop firewalld
```

### selinux

```
setenforce 0
关闭selinux
vi /etc/selinux/config
SELINUX=disabled

查看selinux
getenforce
```





