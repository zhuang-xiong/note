# day8

## Apache服务

### 介绍

* Apache HTTP Server简称为Apache，是Apache软件基金会的一个高性能、功能强大、见状
  可靠、又灵活的开放源代码的web服务软件，它可以运行在广泛的计算机平台上如Linux、
  Windows。因其平台型和很好的安全性而被广泛使用，是互联网最流行的web服务软件之一。

### MPM工作模式

* prefork：多进程I/O模型，一个主进程，管理多个子进程，一个子进程处理一个请求。
* worker：复用的多进程I/O模型，多进程多线程，一个主进程，管理多个子进程，一个子进程
  管理多个线程，每个 线程处理一个请求。

* event：事件驱动模型，一个主进程，管理多个子进程，一个进程处理多个请求。

### http命令

```
-t 检查配置文件语法错误
-v 显示版本信息
-V 显示版本信息和建立环境

-c：在读取配置⽂件前，先执⾏选项中的指令。
-C：在读取配置⽂件后，再执⾏选项中的指令。
-d<服务器根⽬录>：指定服务器的根⽬录。
-D<设定⽂件参数>：指定要传⼊配置⽂件的参数。
-f<设定⽂件>：指定配置⽂件。
-h：显示帮助。
-l：显示服务器编译时所包含的模块。
-L：显示httpd指令的说明。
-S：显示配置⽂件中的设定
-X：以单⼀程序的⽅式来启动服务器。
```

### 安装第一个站点

```
[root@localhost ~]#yum install -y httpd
[root@localhost ~]#echo '<h1>It works!</h1>' > /var/www/html/index.html
[root@localhost ~]#systemctl start httpd
```

检查防火墙和selinux

```
[root@localhost ~]#systemctl stop firewalld
[root@localhost ~]#systemctl status firewalld
[root@localhost ~]#setenforce 0
[root@localhost ~]#getenforce
```

检查端口80

```
[root@localhost ~]#ss -tanl | grep 80
```

查看进程

```
[root@localhost ~]#ps -ef | grep http
```

本地机器测试

```
[root@localhost ~]#curl <IP地址>
```

#### 配置文件说明

```
/etc/httpd/：主配置文件目录
/etc/httpd/conf/httpd.conf：服务配置文件
/etc/httpd/conf.d/：服务配置目录（模块化）
/etc/httpd/conf.modules.d/：模块配置目录
/etc/sysconfig/httpd：守护进程配置文件
/usr/lib64/httpd/modules/：可用模块
/usr/sbin/：相关命令目录
/var/log/httpd/：日志目录
/var/www/：站点目录
```

#### 主配置文件

```
##主配置说明##
[root@node3 ~]# grep "^[^ #]" /etc/httpd/conf/httpd.conf
ServerRoot "/etc/httpd" # 服务器的根
Listen 80 # 监听的端口
Include conf.modules.d/*.conf # 包含模块
User apache # 用户
Group apache # 属组
ServerAdmin root@localhost # 服务器管理员
<Directory />
AllowOverride none
Require all denied
</Directory> # <Directory>和</Directory>用于封装一组指令，使之仅对某个
目录及其子目录生效。
DocumentRoot "/var/www/html"
ErrorLog "logs/error_log" # 错误日志
LogLevel warn # 日志等级
EnableSendfile on # 开启
IncludeOptional conf.d/*.conf # 虚拟服务器配置文件
说明：<></>此类称之为容器，针对某个容器做配置
```

### #### perfork模式

* 优点：适合于没有线程安全库，需要避免线程兼容性问题的系统。它是要求将每个请求相互独立的情况 下最好的mpm，这样若一个请求出现问题就不会影响到其他请求。
* 缺点：一个进程相对占用更多的系统资源，消耗更多的内存。而且，它并不擅长处理高并发请求，在这 种场景下，它会将请求放进队列中，一直等到有可用进程，请求才会被处理。

* 查看默认选择处理模块为perfork

  ```\
  [root@server ~]# httpd -V
  AH00558: httpd: Could not reliably determine the server's fully qualified
  domain name, using fe80::eaf3:dc40:2bf:6da2. Set the 'ServerName' directive
  globally to suppress this message
  Server version: Apache/2.4.6 (CentOS)
  Server built: Nov 16 2020 16:18:20
  Server's Module Magic Number: 20120211:24
  Server loaded: APR 1.4.8, APR-UTIL 1.5.2
  Compiled using: APR 1.4.8, APR-UTIL 1.5.2
  Architecture: 64-bit
  Server MPM: prefork
  threaded: no
  forked: yes (variable process count)
  ```

* 切换apache的mpm工作模式

  ```
  [root@server ~]# cat /etc/httpd/conf.modules.d/00-mpm.conf | grep -Ev
  "^#|^$"
  LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
  [root@server ~]# ps aux | grep httpd
  root 12886 0.4 0.2 221928 4956 ? Ss 11:03 0:00 /usr/sbin/httpd -
  DFOREGROUND
  apache 12887 0.0 0.1 221928 2992 ? S 11:03 0:00 /usr/sbin/httpd -
  DFOREGROUND
  apache 12888 0.0 0.1 221928 2992 ? S 11:03 0:00 /usr/sbin/httpd -
  DFOREGROUND
  apache 12889 0.0 0.1 221928 2992 ? S 11:03 0:00 /usr/sbin/httpd -
  DFOREGROUND
  apache 12890 0.0 0.1 221928 2992 ? S 11:03 0:00 /usr/sbin/httpd -
  DFOREGROUND
  apache 12891 0.0 0.1 221928 2992 ? S 11:03 0:00 /usr/sbin/httpd -
  DFOREGROUND
  ```

* 修改prefork参数

```
默认参数：
StartServers 5 # 服务启动时的进程数
MaxSpareServers 10 # 最大空闲服务进程数
MinSpareServers 5 # 最小空闲进程数
MaxRequestWorkers 256 # 单个进程最多接受的进程数
[root@server ~]# vim /etc/httpd/conf.d/mpm.conf
StartServers 10
MaxSpareServers 15
MinSpareServers 10
MaxRequestWorkers 256
MaxRequestsPerChild 4000
[root@localhost ~]# systemctl restart httpd
[root@server ~]# ps -ef | grep httpd
```

* 测压工具

``` 
[root@localhost ~]# ab -n 1000000 -c 1000 http://127.0.0.1/
# -n 即requests，用于指定压力测试总共的执行次数
# -c 即concurrency，用于指定的并发数
```

### worker模式

work使用了多进程和多线程的混合模式，worker模式也同样会先派生一下子进程，然后每个子进程创建 一些线程，同时包括一个监听线程，每个请求过来会被分配到一个线程来服务。

* 优点：线程比进程会更轻量，因为线程是通过共享父进程的内存空间，因此，内存的占用会减少一些， 在高并发高流量的场景下会比prefork有更多可用的线程，表现会更优秀一些。
* 缺点：如果一个线程出现了问题也会导致同一进程下的线程出现问题，如果是多个线程出现问题，也只 是影响apache的一部分，而不是全部。由于用到多进程多线程，需要考虑到线程的安全。

* 参数

```
StartServers
#服务器启动时建立的子进程数量,在workers模式下默认是3.
ServerLimit
#系统配置的最大进程数量
MinSpareThreads
#空闲子进程的最小数量，默认75
MaxSpareThreads
#空闲子进程的最大数量，默认250
ThreadsPerChild
#每个子进程产生的线程数量，默认是64
MaxRequestWorkers /MaxClients
#限定服务器同一时间内客户端最大接入的请求数量.
MaxConnectionsPerChild
#每个子进程在其生命周期内允许最大的请求数量，如果请求总数已经达到这个数值，子进程将会结束，
如果设置为0，子进程将永远不会结束。在Apache2.3.9之前称之为MaxRequestsPerChild。
```

### event模式

```
这个是apache中最新的模式，在现在的版本里已经是稳定可用的模式，它和worker模式很像，最大的
区别在于，它解决了keep-alive场景下，长期被占用的线程的资源浪费问题（某些线程因为被keepalive，挂载哪里等待，中间基于没有请求过来，一直等到超时）
event中会有一个专门的线程来管理这些keep-alive类型的线程，当有真实请求u过来的时候，将请求传
递给服务线程，执行完毕后，又允许它释放。这样，一个线程就能处理几个请求了，实现了异步非阻
塞。
```

## 访问控制机制

### 更改站点根目录案例

* 从新定义根目录

```
# 定义服务器的文档的页面路径：
[root@server1 conf]# vim httpd.conf
......
DocumentRoot "/data/www/html"
......
# 准备页面
[root@server1 ~]# echo "this path /data/wwww/html" >
/data/www/html/index.html
# 重启服务
[root@server1 ~]# systemctl restart httpd

```



* 测试，403无权限

```
[root@server1 conf]# curl 192.168.80.151 -I
HTTP/1.1 403 Forbidden
Date: Mon, 22 Feb 2021 06:19:54 GMT
Server: Apache/2.4.6 (CentOS)
Last-Modified: Thu, 16 Oct 2014 13:20:58 GMT
ETag: "1321-5058a1e728280"
Accept-Ranges: bytes
Content-Length: 4897
Content-Type: text/html; charset=UTF-8
```



* 放开权限

``` 
[root@server1 conf]# vim httpd.conf
<Directory "/data/www/html">
Require all granted
</Directory>
[root@server1 ~]# systemctl restart httpd
```



* 测试，通过

```
[root@server1 conf]# curl 192.168.80.151 -I
HTTP/1.1 200 OK
Date: Mon, 22 Feb 2021 06:21:15 GMT
Server: Apache/2.4.6 (CentOS)
Last-Modified: Mon, 22 Feb 2021 06:18:57 GMT
ETag: "1a-5bbe6c6eb43fa"
Accept-Ranges: bytes
Content-Length: 26
Content-Type: text/html; charset=UTF-8
[root@server1 conf]# curl 192.168.80.151
this path /data/wwww/html
```





* 详细参数

``` 
Require常见配置参数：
Require all granted # 全部放行
Require all denied # 全部拒绝
Require ip IPAd # 放行某ip地址
Require not ip IP # 拒绝某ip地址
Require user user1 # 放行某用户
Require group group1 # 放行某组
PS：34参数需要在…中才可以。
<RequireAll>
Require all granted
Require not ip 10.252.46.165
</RequireAll>
```

* 白名单

``` 
<RequireAny>
Require all denied
require ip 172.16.1.1 #允许特定IP
</RequireAny>
```

* 黑名单

```
<RequireAll>
Require all granted
Require not ip 172.16.1.1 #拒绝特定IP
</RequireAll>
```

### 用户访问控制

创建用户认证文件

```
[root@server1 ~]# htpasswd -c -m /etc/httpd/conf.d/.htpassword lisi
New password:
Re-type new password:
Adding password for user lisi
[root@server1 ~]# htpasswd -b -m /etc/httpd/conf.d/.htpassword zhangsan zhangsan
Adding password for user zhangsan
```

修改配置文件，启用用户认证

```
[root@server1 ~]# vim /etc/httpd/conf/httpd.conf
<Directory "/data/www/html">
AuthType Basic
AuthName "Restricted Resource"
AuthBasicProvider file
AuthUserFile /etc/httpd/conf.d/.htpassword
Require user lisi
</Directory>
[root@server1 ~]# systemctl restart httpd.service
```

lisi可以访问，zhangsan不行

### Options命令

后跟1个或多个以空白字符分隔的选项列表， 在选项前的+，- 表示增加或删除指定选项

```
[root@localhost ~]# vim /etc/httpd/conf/httpd.conf
<Directory "/data/html">
Options Indexes
[root@localhost html]# systemctl restart httpd
```

![image-20220706201148941](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220706201148941.png)

```
[root@localhost ~]# cd /data/html/dir
[root@localhost dir]# touch f1 f2
```

## 虚拟主机

### 基于IP地址虚拟主机

```
[root@node3 data]# cat /etc/httpd/conf.d/site.conf
<Directory "/data/">
Require all granted
</Directory>
<VirtualHost 192.168.0.140:80>
Servername www.site1.com
DocumentRoot "/data/site1/"
</VirtualHost>
<VirtualHost 192.168.0.145:80>
Servername www.site2.com
DocumentRoot "/data/site2/"
</VirtualHost>
[root@node1 ~]# curl 192.168.0.142
<h1>This is site1</h1>
[root@node1 ~]# curl 192.168.0.145
<h1>This is site2</h1>
```

### 基于端口虚拟主机

```
[root@server1 ~]# cat /etc/httpd/conf.d/site.conf
Listen 8080
Listen 9090
<Directory "/data/">
Require all granted
</Directory>
<VirtualHost *:8080>
DocumentRoot "/data/site3/"
</VirtualHost>
<VirtualHost *:9090>
DocumentRoot "/data/site4/"
</VirtualHost>
[root@server1 ~]# curl 192.168.80.100:8080
<h1>This is site3</h1>
[root@server1 ~]# curl 192.168.80.100:9090
<h1>This is site4</h1>
```

### 基于域名FQDN虚拟主机

```
[root@server1 ~]# cat /etc/httpd/conf.d/site.conf
Listen 10101
<Directory "/data/">
Require all granted
</Directory>
<VirtualHost 192.168.80.100:10101>
Servername www.site5.com
DocumentRoot "/data/site5/"
</VirtualHost>
<VirtualHost 192.168.80.100:10101>
Servername www.site6.com
DocumentRoot "/data/site6/"
</VirtualHost>
~
[root@server1 ~]# cat /etc/hosts
192.168.0.142 www.site5.com
192.168.0.142 www.site6.com
[root@server1 ~]# curl www.site5.com:10101
<h1>This is site5</h1>
[root@server1 ~]# curl www.site6.com:10101
<h1>This is site6</h1>
```

三种结果

![image-20220706201407732](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220706201407732.png)

## SSL配置文件

```
[root@node1 ~]# yum install mod_ssl openssl -y
安装软件
[root@node1 ~]# openssl genrsa -out server.key 2048
生成2048位的加密私钥server.key
[root@node1 ~]# openssl req -new -key server.key -out server.csr
生成证书签名请求server.csr
[root@node1 ~]# openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt
生成类型为X509的自签名证书。有效期设置3650天，即有效期为10年server.crt
[root@node1 ~]# cp server.crt /etc/pki/tls/certs/
[root@node1 ~]# cp server.key /etc/pki/tls/private/
[root@node1 ~]# cp server.csr /etc/pki/tls/private/
复制文件到相应位置
Servername 192.168.0.140:443
SSLCertificateFile /etc/pki/tls/certs/server.crt
SSLCertificateKeyFile /etc/pki/tls/private/server.key
修改配置文件 vim /etc/httpd/conf/httpd.conf
[root@node1 ~]# firewall-cmd --add-port=443/tcp --per
防火墙放行
```

## LAMP

