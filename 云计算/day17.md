# day17

### OSPF多区域

#### 单区域的问题

* 收到的LSA通告过多，OSPF路由器负担过大
* 内部动荡会引起全网路由器的完全SPF计算
* 资源内耗严重，LSDB庞大，设备性能降低，影响数据转发
* 每台路由器需要维护的路由表越来越大，单区域无法汇通

#### 多区域的特点

* 减少LSA洪泛的范围，有效的把拓扑控制在区域内，达到优化网络的目的
* 在区域边界可以做路由汇总，减少路由表
* 充分利用OSPF特殊区域的特性，进一步减少LSA洪泛，从而优化路由
* 多区域提高了网络的扩展性，有利于组建大规模的网络

#### 区域

* 骨干区域，编号为0
* 非骨干区域，编号非0
* 所有非骨干区域必须与骨干区域相连

https://pic4.zhimg.com/v2-f636cb28c5f04a12c49afdba307de823_r.jpg
![preview](https://pic4.zhimg.com/v2-f636cb28c5f04a12c49afdba307de823_r.jpg)

#### 骨干区域与非骨干区域

#### 路由角色

* ABR(如，C，D，E)
  * 区域边界路由，负责区域的路由条目传递
  * 必须有一个接口与骨干区域相连
  * 必须有一个与非骨干区域相连
* ASBR（F,G,H）
  * 负责将OSPF之外的路由引入

#### 多区域基本拓扑







***

#### LSA

* LSA（链路状态广播)是链路状态协议使用的一个分组，它包括有关邻居和通道成本信息，LSA被路由器接受用于维护他们的路由选择表。

#### LSA类型

| 类型 | 解释                   |
| ---- | ---------------------- |
| 1    | 路由器LSA              |
| 2    | 网络LSA                |
| 3    | 网络汇总LSA            |
| 4    | ASBR汇总LSA            |
| 5    | AS外部LSA              |
| 6    | 组成员LSA              |
| 7    | NSSA外部LSA            |
| 8    | 外部属性LSA            |
| 9    | Opaque（链路本地范围） |
| 10   | Opaque（本地区域范围） |
| 11   | Opaque（AS范围）       |

#### 1类LSA

***

* 每个路由器针对他所在的区域产生LSA1，描述区域内部和路由器直连的链路信息
* LSA1只在**本区域内洪泛**，不会跨越ABR
* LSA中会表示路由器是否是ABR，ASBR或者是virtual-link的端点的身份信息

```
```

* Link ID：可以理解为目的地
* ADV Router：链路来自哪个路由器
* Age：计时器
* Seq：序列号，初始值为 0x80000001，每收到一次数据包更新一次这个数值会+1， 加到0x8fffffff达到最大，下一次变为 0x80000000，ospf认为0x80000000是不可用的
* Checksum：校验和

#### 2类LSA

* 由DR生成，描述在网络是哪个连接的路由器以及网段掩码信息，以及这个MA所属的路由器
* LSA类型2只在**本区域内洪泛**，不允许跨越ABR
* Network LSA ID 是DR宣告的接口的IP地址
* Network LSA中没有COST字段

```
```

* 通过LSA1，LSA2在区域内洪泛，使每个路由器LSDB达到同步，计算生成表示为“O”的路由，解决区域内部的通信问题

#### 3类LSA

* 由ARB生成，实际上是将区域内部的Type1 Type2的信息以路由子网的形式扩散出去，是Summay LSA中Summay的含义
* Type3的链路状态ID是目的网络地址
* 如果一台ABR路由在与其本身相连的区域内有多条路由可以到达目的地，那么他只会发单一的一条网络汇总LSA到骨干区域，而且这条网络汇总LSA是上述多条路由中代价最低的
* ABR收到来自同区域的其他ABR传来的Type3 LSA会生成新的Type3 LSA,(（Advertising Router改 为自己),然后在整个OSPF中扩散

```
```

#### 4类LSA

* ASBR Summary LSA由ABR生成，用于描述ABR到达ASBR的链路状态，链路状态ID为目的ASBR地RID

```
```

#### 5类LSA

* AS External LSA是由ASBR生成用于描述OSPF自治系统外的目标网段信息，链路装填ID是目的地址的IP网络号
* 外部路由通过重发布，引入OSPF路由域，路由条目由ASBR以LSA5的形式生成然后进入OSPF路由域
* OE2 开销=外部开销
* OE1 开销=外部开销+内部开销

```
```

#### 4类和5类的区别

* 四类LSA告诉你怎么到达ASBR，由ABR产生
* 五类LSA告诉你具体外部有哪些路由可以走，由ASBR产生

#### 7类LSA

* NSSA中外部LSA NSSA External LSA
* 在NSSA(非完全存根区域)not-stubby area 中ASBR针对外部网络产生类似LSA5的LSA类型7
* 链路ID是外部网路地址
* LSA类型7只能在NSSA区域中洪泛，到达NSSA区域ABR后，NSSA ABR将其转换成LSA类型5外部路 由，传播到Area 0，从而传播到整个OSPF路由域
* 生成路由缺省用ON2表示，也可指定为ON1；





***

### 特殊区域

#### stub area

* 随着大量的5类LSA进入ospf区域，OSPF Database会变得庞大，同时路由表的外部地址也会增大。这会占用路由器大量的资源。
* 解决这个问题的办法是：让Area内部的路由器不记录任何AS外部的地址，而使用ABR作为默认网关，有一条指向ABR的默认路由。
* 这种方式我们把它叫做stub area。
* 配置 
* router ospf 2 
* area 1 stub
*  在区域二里涉及到的所有路由器使用上述命令 
* 没有配置末梢节点之前的路由表

#### totally stub area

* 有时仅仅不学习外部路由仍然不能够满足需求，路由表和ospf的数据库依然非常庞大，这个时候可 以连ospf的其他区域的路由也不学习 
* 只学习本区域的路由，以及有一条指向ABR的默认路由
*  配置与stub不同的是在ABR上配置area 2 stub no-summary 
* totally stub area的路由表

#### NSSA area

* 当我们把区域配置成末梢区域时，有时候有特例想要加入一些外部路由，这个时候nssa允许存在 ASBR通过7类LSA的方式将外部路由引入本区域，不会自动注入默认路由 
* 不会学习外部路由 
* 会学习其他区域路由 
* 可以学习ASBR注入的外部路由 
* 没有注入默认路由 
* 配置 
* 在区域内所有路由器配置以下命令 
* router ospf 1 
* area 1 nssa 
* 配置NSSA之前的路由表

#### totally NSSA area

* 不会学习外部路由 
* 不会学习其他区域路由 
* 可以学习ASBR注入的外部路由 
* 会自动注入默认路由 
* 配置：与nssa不同的是在ABR上配置以下命令 
* router ospf 1 
* area 1 nssa no-summary

#### Passive-interface（被动接口）

* 当有某台设备或者服务器与外网相连，那么当这台设备被黑客攻击成功后，黑客则可以利用ospf的 特性破坏内部网络，为了解决这种问题提出被动接口的概念 
* 配置 
* router ospf 1 
* passive-interface 接口



***

#### 默认路由

