# day22

### Zabbix企业级软件监控方案

***

#### Cacti

Cacti 是一套基于 PHP、MySQL、SNMP 及 RRD Tool 开发的监测图形分析工具，Cacti 是使用轮 询的方式由主服务器向设备发送数据请求来获取设备上状态数据信息的,如果设备不断增多,这个轮 询的过程就非常的耗时，轮询的结果就不能即时的反应设备的状态了。Cacti 监控关注的是对数据 的展示，却不关注数据异常后的反馈。如果凌晨 3 点的时候设备的某个数据出现异常，除非监控人 员在屏幕前发现这个异常变化，否则是没有任何报警机制能够让我们道出现了异常。

#### Nagios

* Nagios 是一款开源的免费网络监控报警服务,能有效监控 Windows、Linux 和 Unix 的主机状态， 交换机、路由器和防火墙等网络设置，打印机、网络投影、网络摄像等设备。在系统或服务状态异 常时发出邮件或短信报警第一时间通知运维人员，在状态恢复后发出正常的邮件或短信通知。 Nagios 有完善的插件功能,可以方便的根据应用服务扩展功能。
* Nagios 已经可以支持由数万台服务器或上千台网络设备组成的云技术平台的监控,它可以充分发挥 自动化运维技术特点在设备和人力资源减少成本。只是 Nagios 无法将多个相同应用集群的数据集 合起来,也不能监控到集群中特殊节点的迁移和恢复。

#### Ganlia

* Ganglia 是 UC Berkeley 发起的一个开源集群监视项目,设计用于测量数以千计的节点。Ganglia 的 核心包含 gmond、gmetad 以及一个 Web 前端。
* 主要是用来监控系统性能,如:CPU 、内存、硬盘利用率, I/O 负载、网络流量情况等,通过曲线很容易 见到每个节点的工作状态,对合理调整、分配系统资源,提高系统整体 性能起到重要作用,目前是监控 HADOOP 的官方推荐服务。

#### Zabbix

* Zabbix 是一个基于 WEB 界面的提供分布式系统监视以及网络监视功能的企业级的开源解决方案。 zabbix 能监视各种网络参数,保证服务器系统的安全运营;并提供灵活的通知机制以让系统管理员快 速定位/解决存在的各种问题。 
* Zabbix 是由 Alexei Vladishev 创建，目前由 Zabbix SIA 在持续开发和支持。 
* Zabbix 是一个企业级的分布式开源监控方案。 
* Zabbix 是一款能够监控各种网络参数以及服务器健康性和完整性的软件。 
* Zabbix 使用灵活的通知机制，允许用户为几乎任何事件配置基于邮件的告警。这样可以快速反馈 服务器的问题。基于已存储的数据，Zabbix提供了出色的报告和数据可视化功能。这些功能使得 Zabbix成为容量规划的理想方案。 
* Zabbix 支持主动轮询和被动捕获。 
* Zabbix所有的报告、统计信息和配置参数都可以通过基于Web的前端页面进行访问。基于Web的 前端页面可以确保您从任何方面评估您的网络状态和服务器的健康性。 
* Zabbix是免费的。Zabbix是根据GPL通用公共许可证第2版编写和发行的。这意味着它的源代码都 是免费发行的，可供公众任意使用, 商业支持 由Zabbix公司提供。

***

### Zabbix监控介绍

#### 优点

* 开源,无软件成本投入 
* Server 对设备性能要求低 
* 支持设备多,自带多种监控模板 
* 支持分布式集中管理,有自动发现功能,可以实现自动化监控 
* 开放式接口,扩展性强,插件编写容易 
* 当监控的 item 比较多服务器队列比较大时可以采用主动状态,被监控客户端主动 从server 端去下载 需要监控的 item 然后取数据上传到 server 端。 这种方式对服务器的负载比较小。
*  Api 的支持,方便与其他系统结合

***

#### Zabbix部署

* 官网：[Zabbix：企业级开源监控解决方案](https://www.zabbix.com/cn/)
* 安装Zabbix存储仓库

```
# rpm -Uvh https://repo.zabbix.com/zabbix/4.0/rhel/7/x86_64/zabbix-release-4.0-2.el7.noarch.rpm
# yum clean all
```

* 安装zabbix server，web前端，agent

```
# yum install zabbix-server-mysql zabbix-web-mysql zabbix-agent
```

* 安装数据库

```
yum install -y epel-release-noarch
yum install -y mariadb mariadb-server
```

* 创建数据库

```
# mysql -uroot -p
密码
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> create user zabbix@localhost identified by '密码';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> quit;
```

* 导入初始架构和数据，系统将提示您输入新创建的密码。

```
# zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

* 为Zabbix server配置数据库

```
编辑配置文件 /etc/zabbix/zabbix_server.conf
DBPassword=密码
```

* 为Zabbix前端配置PHP

```
编辑配置文件 /etc/httpd/conf.d/zabbix.conf， 取消注释并为您设置正确的时区。

# php_value date.timezone Asia/Shanghai
```

* 启动Zabbix server和agent进程,启动Zabbix server和agent进程，并为它们设置开机自启：

```
# systemctl restart zabbix-server zabbix-agent httpd
# systemctl enable zabbix-server zabbix-agent httpd
```

* 访问网站http://ip/zabbix

* 初始化之后，账号为Admin，密码为zabbix

***

#### 客户端配置

* 安装软件包

```
yum install zabbix-agent -y
客户端安装软件zabbix-agent
```

* 修改配置文件

```
 vim /etc/zabbix/zabbix_agentd.conf

Server=192.168.241.10(服务端ip地址)
ServerActive=192.168.241.10(服务端ip地址)
Hostname=server2（客户端主机名）
```

***

#### Zabbix实现邮件告警

* 博客：[zabbix邮箱告警_不可言、的博客-CSDN博客_zabbix邮件告警](https://blog.csdn.net/weixin_68330286/article/details/125260647)

* 管理-->报警媒介类型-->创建报警媒介类型：

https://service.mail.qq.com/cgi-bin/help?subtype=1&&id=28&&no=371 #设置参考、

![img](https://img-blog.csdnimg.cn/e47c0c167f3d48978bbca0fb4011b35e.png)

* 给用户添加褒奖媒介

管理-->用户-->选择用户-->选择报警媒介

![img](https://img-blog.csdnimg.cn/d6c6cd0579a14981bf8fac825371c9f3.png)

* 更新报警媒介

![img](https://img-blog.csdnimg.cn/21a2566172304eb5adcad5f08d366278.png)

* 创建动作

动作是对zabbix 触发器触发后生成的事件的具体处理操作，可以是远程执行命令，也可以是发送通知给指定的管 理员进行故障处理，发送命令是调用的上一步骤创建好的报警媒介类型。

配置-->动作-->创建动作：

![img](https://img-blog.csdnimg.cn/d9c0d4f51b0e44dc8444e395e0bdb296.png)

![img](https://img-blog.csdnimg.cn/9581de9a5d5b42e3a4dcd689d787a5d0.png)

* 在建立恢复操作

![img](https://img-blog.csdnimg.cn/f563d9bd404140e3aa1c42891d83c10d.png)

* 验证动作当前状态：

 将某个被监控的服务手动停止，验证能否收到zabbix 发送的报警通知。

* 验证事件状态：

在zabbix web界面，验证当事件触发后邮件通知也没有 发送成功。

![img](https://img-blog.csdnimg.cn/65f84d56b318490aae05170c28f10127.png)

***

#### 配置钉钉告警

* 博客：参考博客：https://blog.csdn.net/rightlzc/article/details/100702672

* 第一步，是钉钉群聊（这个操作直接在手机上就可以拉群） 
* 第二步，是添加群机器人（这个操作是需要在电脑端完成，手机没有权限创建机器人） 
* 第三步，去对应的目录下准备python脚本

**脚本代码**

```
[root@server ~]# yum install -y python-requests #脚本中会用到的一个
模块
[root@server alertscripts]# pwd
/usr/lib/zabbix/alertscripts
[root@server alertscripts]# cat zabbix_send_ding.py
#!/usr/bin/python
# -*- coding: utf-8 -*-
# Author: xxxxxxxx
import requests
import json
import sys
import os
headers = {'Content-Type': 'application/json;charset=utf-8'}
api_url = "https://oapi.dingtalk.com/robot/send?
access_token=1a047d0cdc5d0be0a438c73ad0b5e73e25b600173696fd49a2e4c1f352f4bca
4"
def msg(text):
	json_text= {
    "msgtype": "text",
    "at": {
        "atMobiles": [
        "13333333333"
        ],
        "isAtAll": True
    },
    "text": {
   		 "content": text
    }
    }
    print
requests.post(api_url,json.dumps(json_text),headers=headers).content
if __name__ == '__main__':
#text = "linux + zabbix 天下第一!"
text = sys.argv[1]
msg(text)
[root@server alertscripts]# systemctl restart zabbix-server.service
```

* 第四步，测试脚本

```
[root@server alertscripts]# chmod a+x zabbix_send_ding.py
[root@server alertscripts]# ./zabbix_send_ding.py "linux+zabbix天下第一！"
{"errcode":0,"errmsg":"ok"}
```

* 第五步，添加告警媒介 因为我们使用的python脚本只接收一个参数（内容），所以只需要添加一个参数{ALLERT.MESSAGE}即 可

* 第六步，添加动作 
* 第七步，绑定用户，用户收件人写电话号码即可
