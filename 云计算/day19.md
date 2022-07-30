# day19

#### MAC地址

* MAC地址48位，用点分16进制
* 全球唯一，IEEE管理
* 分为两个部分，厂商号和序列号，前24为供应商，后24为序列号

#### 交换机工作机制

1. 读取数据帧头部的源MAC地址，并且将接口地址和MAC地址记录到MAC 地址
2. 读取数据帧头部的目的MAC地址，并且与自己的MAC地址表对照
   1. 匹配成功，从对应接口发出
   2. 匹配失败，除了收到的接口全部发出
3. 广播或者组播地址，则从收到的接口之外所有接口发出

#### 路由器工作机制

1. 

#### 单臂路由

```
1.R1和R2配置不同的网段的ip地址
Router(config-line)#ho R1
R1(config)#inte e0/0
R1(config-if)#ip add 192.168.1.1 255.255.255.0
R1(config-if)#no sh

R1(config)#inte e0/0
R1(config-if)#ip route 0.0.0.0 0.0.0.0 e0/0

2.R1和R2配置静态路由，端口指向R1和R2的端口
3.将交换机上的e0/0，e0/1，划分给不同的vlan，接口三配置成trunk
Switch(config)#vlan 10
Switch(config-vlan)#vlan 20
Switch(config)#inte e0/0
Switch(config-if)#sw mode acc
Switch(config-if)#sw acc vl 10
Switch(config-if)#ex
Switch(config)#inte e0/1
Switch(config-if)#sw mode acc
Switch(config-if)#sw ac vl 20
Switch(config-if)#inte e0/2 
Switch(config-if)#sw trunk encapsulation dot1q 
Switch(config-if)#sw mo trunk

4.配置单臂路由
Switch#sh vl b

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/3
10   VLAN0010                         active    Et0/0
20   VLAN0020                         active    Et0/1
1002 fddi-default                     act/unsup 
1003 token-ring-default               act/unsup 
1004 fddinet-default                  act/unsup 
1005 trnet-default                    act/unsup 

R1#ping 192.168.2.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.2.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 1/1/1 ms

```

