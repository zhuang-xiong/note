# day 9

## 		Ngnix

* Nginx是lgor Sysoev为俄罗斯访问量第二的rambler.ru站点设计开发的。从2004年发布至今，凭借 开源的力量， 已经接近成熟与完善。
* 类似于Apache Hpptd 
* 官方：http://www.nginx.org/

### 特点

* 高并发，消耗内存小
* 网站web服务
* 网站均衡代理
* 正向代理反向代理
* 网站缓存功能
* nginx实现网络通讯时使用的是异步网络IO模型：epoll模型，参考博客：https://segmentfault.co m/a/1190000003063859#item-3-13

### Nginx进程结构

#### web请求处理机制

* 多进程方式:服务器每接收到一个客户端请求就有服务器的主进程生成一个子进程响应客户 端，直到用户关闭连接，这样的优势是处理速度快。子进程之间相互独立，但是如果访问过大 会导致服务器资源耗尽而无法提供请求。
* 多线程方式:与多进程方式类似，但是每收到一个客户端请求会有服务进程派生出一个线程来 个客户方进行交互，一个线程的开销远远小于一个进程，因此多线程方式在很大程度减轻了 web服务器对系统资源的要求，但是多线程也有自己的缺点。即当多个线程位于同一个进程内 工作的时候，可以相互访问同样的内存地址空间，所以他们相互影响，一旦主进程挂掉则所有 子线程都不能工作了，IIS服务器使用了多线程的方式，需要间隔一段时间就重启一次才能稳 定。

#### 主进程的功能

* 对外接口:接收外部的操作(信号) 
* 对内转发:根据外部的操作的不同，通过信号管理worker 
* 监控:监控worker进程的运行状态，worker进程异常终止后，自动重启worker进程 
* 读取Nginx配置文件并验证其有效性和正确性 
* 建立、绑定和关闭socket连接 按照配置生成、管理和结束工作进程 
* 接受外界指令，比如重启、升级及退出服务器等指令 
* 不中断服务，实现平滑升级，重启服务并应用新的配置 
* 开启日志文件，获取文件描述符 不中断服务，实现平滑升级，升级失败进行回滚处理 
* 编译和处理perl脚本

#### 工作进程(worker process的功能

* 所有Worker进程都是平等的 
* 实际处理:网络请求，由Worker进程处理 Worker进程数量:在nginx.conf 中配置，一般设置为核心数，充分利用CPU资源，同时，避免 进程数量过多，避免进程竞争CPU资源，增加 上下文切换的损耗 
* 接受处理客户的请求 
* 将请求依次送入各个功能模块进行处理 I/O调用，获取响应数据 
* 与后端服务器通信，接收后端服务器的处理结果 
* 缓存数据，访问缓存索引，查询和调用缓存数据 
* 发送请求结果，响应客户的请求 
* 接收主程序指令，比如重启、升级和退出等

#### Nginx目录结构

* conf：主配置文件
* html：默认情况下，网页文件
* sbin：主进程文件
* logs：日志文件
  * access.log：访问日志
  * error.log：错误日志
  * nginx.pid

#### 进程

* 单进程

* 多进程

![img](https://img-blog.csdnimg.cn/20200208152453385.png?x-ss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L05vX0dhbWVfTm9fTGlmZV8=,size_16,color_FFFFFF,t_70)

#### 虚拟主机

```
原来一个IP地址对应一个域名，极大的造成了资源的浪费。为了节约资源，采用了虚拟主机。根据客户端的请求的网页的不同可以使用不同的端口号，访问同一台服务器。
```



### Ngnix部署安装

```
yum安装，安装前需要先安装扩展源
[root@localhost ~]# yum install epel-release.noarch -y
[root@localhost ~]# yum install nginx -y
启动nginx
[root@localhost ~]# systemctl start nginx.service
[root@localhost ~]# systemctl status nginx

关闭防火墙和selinux
[root@localhost ~]# systemctl stop firewalld.service
[root@localhost ~]# setenforce 0

创建nginx自启动文件
# 复制同一版本的nginx的yum安装生成的service文件
[root@localhost ~]# vim /usr/lib/systemd/system/nginx.service
[Unit]
Description=The nginx HTTP and reverse proxy server
Documentation=http://nginx.org/en/docs/
After=network.target remote-fs.target nss-lookup.target
Wants=network-online.target
[Service]
Type=forking
PIDFile=/apps/nginx/run/nginx.pid
ExecStart=/apps/nginx/sbin/nginx -c /apps/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
[Install]
WantedBy=multi-user.target
[root@localhost ~]# mkdir /apps/nginx/run/
[root@localhost ~]# vim /apps/nginx/conf/nginx.conf
pid /apps/nginx/run/nginx.pid;
验证nginx自启动文件
[root@localhost ~]# systemctl daemon-reload
[root@localhost ~]# systemctl enable --now nginx
[root@localhost ~]# ll /apps/nginx/run
总用量 4
-rw-r--r--. 1 root root 5 5月 24 10:07 nginx.pid

```

### Ngnix目录结构

![image-20220707203212110](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220707203212110.png)

#### 日志切割

1. /etc/logrotate.d/nginx：可以实现日志切割 

2. 日志切割方式一 
   1. mv /var/log/nginx/access.log /var/log/nginx/access_$(date +%F).log 
   2.  重启nginx systemctl restart nginx 
3.  日志切割方式二，使用专用的文件切割程序--logrotate

```
[root@localhost logrotate.d]# cat /etc/logrotate.conf
# see "man logrotate" for details
# rotate log files weekly
weekly # 定义默认日志切割周期
# keep 4 weeks worth of backlogs
rotate 4 # 定义只保留几个切割后的文件
# create new (empty) log files after rotating old ones
create # 切割后创建出一个相同的源文件，后面可以跟上文件权限、属主、属组
# use date as a suffix of the rotated file
dateext # 定义角标，扩展名称信息
# uncomment this if you want your log files compressed
#compress # 是否对切割后的文件进行压缩处理
# RPM packages drop log rotation information into this directory
include /etc/logrotate.d
# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp { # 当都对某个文件进行切割配置
monthly
create 0664 root utmp
minsize 1M
rotate 1
}
/var/log/btmp {
missingok
monthly
create 0600 root utmp
rotate 1
}
```

### 配置文件默认说明

```
[root@localhost ~]# cp /etc/nginx/nginx.conf{,.bak}
[root@localhost ~]# grep -Ev "#|^$" /etc/nginx/nginx.conf.bak
>/etc/nginx/nginx.conf
[root@localhost ~]# cat /etc/nginx/nginx.conf
=====================第一个部分，配置文件的主区域======================
user nginx; # 定义
worker进程的管理用户
worker_processes auto; # 定义worker进程数，
auto会自动调整为cpu核数
error_log /var/log/nginx/error.log; # 定义错误日志
pid /run/nginx.pid; # 定义pid文件
include /usr/share/nginx/modules/*.conf;
=====================第二个部分，配置文件的事件区域====================
events {
worker_connections 1024; # 定义一个worker进程可以同时接受1024个请求
}
=====================第三个部分，配置文件的http区域====================
http {
log_format main '$remote_addr - $remote_user [$time_local] "$request"
'
'$status $body_bytes_sent "$http_referer" '
'"$http_user_agent" "$http_x_forwarded_for"';
# 定义日志格式
access_log /var/log/nginx/access.log main;
# 指定日志文件路径
sendfile on; # 允许sendfile方式传输文件 ，sendfile系统调用在两个文
件描述符之间直接传递数据(完全在内核中操作)，从而避免了数据在内核缓冲区和用户缓冲区之间的拷
贝，操作效率很高，被称之为零拷贝。
tcp_nopush on; # 在sendfile启动下，使用TCP_CORK套接字，当有数据时，
先别着急发送, 确保数据包已经装满数据, 避免了网络拥塞
tcp_nodelay on; # 接连接保持活动状态
keepalive_timeout 65; # 超时时间
types_hash_max_size 2048;# 连接超时时间
include /etc/nginx/mime.types; # 文件扩展名与文件类型映射表
default_type application/octet-stream; # 默认文件类型，默认为
text/plain
include /etc/nginx/conf.d/*.conf;
server {
listen 80 default_server; # 指定监听的端口
listen [::]:80 default_server;
    server_name _; # 指定网站主
    机名
    root /usr/share/nginx/html; # 定义站点目录的位置
    include /etc/nginx/default.d/*.conf; # 定义首页文件
    location / {
    }
    error_page 404 /404.html; # 定义优雅显示页面信息
    location = /40x.html {
    }
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
    }
    }
    }

```

### location表达式

* ~ 表示执行一个正则匹配，区分大小写；
*  ~* 表示执行一个正则匹配，不区分大小写；
*  ^~ 表示普通字符匹配。使用前缀匹配。如果匹配成功，则不再匹配其他location；
*  = 进行普通字符精确匹配。也就是完全匹配；
*  @ 它定义一个命名的 location，使用在内部定向时，例如 error_page, try_files 
* 优先级：=/^/ ,~*/常规字符串

```
location = / {
[ configuration A ]
}
location / {
[ configuration B ]
}
location /documents/ {
[ configuration C ]
}
location ^~ /images/ {
[ configuration D ]
}
location ~* \.(gif|jpg|jpeg)$ {
[ configuration E ]
}
A：请求 /
B: 请求 index.html
C: 请求 /documents/document.html
D: 请求 /images/1.jpg
E: 请求 /documents/document.gif
```

### 客户端配置

```
keepalive_timeout # 保持连接的超时时长
keepalive_requests # 一次连接允许请求资源的最大数量
keepalive_disable # 对某种浏览器禁用长连接
send_timeout # 向客户端发送响应报文的超时时长
client_body_buffer_size # 接收客户端请求报文的body部分的缓冲区大小
client_body_temp_path # 设定用于存储客户端请求报文的body部分的临时存储路径及子目录结构
和数量
limit_rate rate # 限制响应给客户端的传输速率
limit_except # 限制对指定的请求方法之外的其它方法的使用客户端
```

### 搭建第一个网站

```
编写虚拟主机配置文件
[root@www conf.d]# vim /etc/nginx/conf.d/www.conf
server {
listen *:8080;
server_name www.eagle.com;
location / {
root /usr/share/nginx/html;
index ealgeslab.html;
}
}
编写网站代码
[root@www ~]# cat /usr/share/nginx/html/ealgeslab.html
赋予网站代码权限
[root@www ~]# chmod 777 /usr/share/nginx/html/ealgeslab.html
编写hosts文件，做好域名解析(如果windows需要访问也要在这个目录下的hosts文件做相同操作
C:\Windows\System32\drivers\etc)
[root@www ~]# cat /etc/hosts
...
192.168.33.133 www.eagle.com
...
[root@www ~]# ping www.eagle.com
重启nginx服务（使用reload平滑重启，以下两种方式不要混用）
[root@www html]# systemctl reload nginx.service
[root@www html]# nginx -s reload
访问测试
[root@www ~]# curl www.eagle.com:8080
```

### 搭建nginx服务多个网站

```
准备好配置文件
[root@www conf.d]# ll
总用量 12
-rw-r--r--. 1 root root 107 9月 25 16:45 bbs.conf
-rw-r--r--. 1 root root 109 9月 25 16:45 blog.conf
-rw-r--r--. 1 root root 107 9月 25 16:44 www.conf
[root@www conf.d]# cat *.conf
server {
listen *:8080;
server_name bbs.eagle.com;
location / {
root /html/bbs;
index index.html;
}
}
server {
listen *:8080;
server_name blog.eagle.com;
location / {
root /html/blog;
index index.html;
}
}
server {
listen *:8080;
server_name www.eagle.com;
location / {
root /html/www;
index index.html;
}
}
准备站点目录以及首页文件
[root@www bbs]# mkdir -p /html/{www,bbs,blog}
[root@www bbs]# for name in {www,bbs,blog};do echo "<h1> $name </h1>" >
/html/$name/index.html;done;
赋予网站代码权限
[root@www ~]# chmod -R 777 /html
[root@www ~]# setenforce 0
准备域名解析
[root@www bbs]# cat /etc/hosts
192.168.33.133 www.eagle.com bbs.eagle.com blog.eagle.com
重启nginx
[root@www html]# systemctl reload nginx.service
访问测试
[root@www ~]# curl www.eagle.com:8080
<h1> www </h1>
[root@www ~]# curl bbs.eagle.com:8080
<h1> bbs </h1>
[root@www ~]# curl blog.eagle.com:8080
<h1> blog </h1>

```

## nginx相关模块

#### nginx证书配置

```
server {
listen 80;
listen 443 ssl;
ssl_certificate /apps/nginx/certs/iproute.crt;
ssl_certificate_key /apps/nginx/certs/iproute.cn.key;
ssl_session_cache shared:sslcache:20m;
ssl_session_timeout 10m;
root /data/nginx/html;
}
```

### Nginx代理和缓存

* 反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内 部网络上的服务 器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器 对外就表现为一个反向代理服务 器，通常使用到的http/https协议和fastgci（将动态内容和http服务器 分离）
* 正向代理，意思是一个**位于客户端和原始服务器 (origin server)之间的服务器**，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标 (原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端
* 正向代理与反向代理最简单的区别：
  正向代理隐藏的是用户，反向代理隐藏的是服务器

#### 正向代理

1. resolver：指定dns服务器地址 

2. proxy_pass：代理到的地址 
3. 3. resolver_timeout：dns解析超时时长

``` 
添加配置文件
[root@nginx1 ~]# cat /etc/nginx/conf.d/www.conf
server{
listen *:8090;
resolver 114.114.114.114;
location / {
proxy_pass http://$http_host$request_uri;
}
}
重启nginx
test
```

* 方法一

```
[root@client ~]# curl -x 192.168.80.10:8090 "http://www.baidu.com" -I
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Wed, 21 Jul 2021 06:16:29 GMT
Content-Type: text/html
Content-Length: 277
Connection: keep-alive
Accept-Ranges: bytes
Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
Etag: "575e1f60-115"
Last-Modified: Mon, 13 Jun 2016 02:50:08 GMT
Pragma: no-cache
```

* 方法二

```
 [root@client ~]# export http_proxy=http://192.168.80.10:8090
```

### 基于Http协议代理实验

```
实验一、简单反向代理
[root@server conf.d]# cat proxy.conf
server {
listen 8080;
server_name www.server1.ccom;
location / {
proxy_pass http://192.168.80.128:8090;
}
}
实验二、proxy代理网站常用优化配置写入到新文件，调用时使用include即可
[root@server conf.d]# cat /etc/nginx/proxy_params
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_connect_timeout 30;
proxy_send_timeout 60;
proxy_read_timeout 60;
proxy_buffering on;
proxy_buffer_size 32k;
proxy_buffers 4 128k;
[root@server conf.d]# cat proxy.conf
server {
listen 8080;
server_name www.server1.com;
location / {
proxy_pass http://192.168.80.128:8090;
include proxy_params;
}
}
```

### proxy_cache缓存实验

```
准备配置文件
http {
# 其他配置-------------
# 指定了数据存放路径在/myweb/server/proxycache目录下，它包含两级hash目录，缓存数据的
总量不能超过20m，如果缓存在5分钟之内没有被访问则强制刷新，定义缓存空间mycache
proxy_cache_path /myweb/server/proxycache levels=1:2 max_size=20m
inactive=5m loader_sleep=1m keys_zone=mycache:10m;
# 配置响应数据的临时存放目录
proxy_temp_path /myweb/server/tmp;
# 其他配置--------------
server {
proxy_pass http://192.168.80.20;
proxy_cache mycache;
proxy_cache_valid 200 301 1h; # 状态码为200 301的响应缓存1h
proxy_cache_valid any 1m; # 配置其他状态的响应数据缓存1分钟
}
}
```

### 基于fastcgi协议代理和缓存实验 

```
# 官网示例
server {
location / {
fastcgi_pass localhost:9000;
fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
fastcgi_param QUERY_STRING $query_string;
}
location ~ \.(gif|jpg|png)$ {
root /data/images;
}
}
node1为代理服务器
node2为lnmp服务器
[root@node1 ~]# cat /etc/nginx/nginx.conf
http{
...
fastcgi_cache_path /var/cache/nginx/fastcgi_cache levels=1:2:1
keys_zone=fcgi:20m
inactive=120s;
}
[root@node1 conf.d]# cat proxy.conf
server{
server_name 172.16.0.10;
location / {
proxy_pass http://192.168.10.20;
}
# 实现动静分离
location ~ .*\.(html|txt)$ {
root /usr/share/nginx/html/;
}
# 缓存相关配置
location ~* \.php$ {
fastcgi_cache fcgi;
fastcgi_cache_key $request_uri;
fastcgi_cache_valid 200 302 10m;
fastcgi_cache_valid 301 1h;
fastcgi_cache_valid any 1m;
}
[root@node2 ~]# yum install php-fpm
[root@node2 ~]# systemctl start php-fpm.service
[root@node2 ~]# cat /etc/nginx/conf.d/php.conf
server{
listen 80;
server_name 192.168.10.20;
root /usr/share/nginx/html;
location ~* \.php$ {
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html$fastcgi_script_name;
include fastcgi_params;
}
}
测试：
1. 访问http://172.16.0.10/index.html 和 index.php
2. 查看node1上的cache目录下缓存内容

```

### LNMP架构

LNMP是一套技术的组合，L=Linux、N=Nginx、M~=MySQL、P=PHP

#### 工作模式

* 首先nginx服务是不能请求动态请求，那么当用户发起动态请求时，nginx无法处理 
* 当用户发起http请求，请求会被nginx处理，如果是静态资源请求nginx则直接返回，如果是动态请 求nginx则通过fastcgi协议转交给后端的PHP程序处理

![image-20220707205212993](C:\Users\xz\AppData\Roaming\Typora\typora-user-images\image-20220707205212993.png)

#### LNMP环境部署

```
安装nginx
# 安装扩展软件源
yum -y install epel-release
# 安装nginx
yum -y install nginx
# 启动nginx并且设置为开机自启动
systemctl start nginx
systemctl enable nginx
# 检查nginx是否成功安装
nginx -v
修改用户
方便后面的权限管理，将nginx改为www
# 创建www用户组
groupadd www -g 666
# 添加www用户
useradd www -u 666 -g 666 -s /sbin/nologin -M
# 修改配置切换nginx运行用户为www
sed -i '/^user/c user www;' /etc/nginx/nginx.conf
# 重启nginx服务
systemctl restart nginx
# 检查nginx运行的用户
ps aux |grep nginx
安装php
# 安装webtstic软件仓库
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
# 安装php环境，php所需的组件比较多，我们可以一次性安装全面了
yum -y install php71w php71w-cli php71w-common php71w-devel php71w-embedded
php71w-gd php71w-mcrypt php71w-mbstring php71w-pdo php71w-xml php71w-fpm
php71w-mysqlnd php71w-opcache php71w-pecl-memcached php71w-pecl-redis php71w-pecl-mongodb
切换用户,配置php-fpm用于与nginx的运行用户保持一致
sed -i '/^user/c user = www' /etc/php-fpm.d/www.conf
sed -i '/^group/c group = www' /etc/php-fpm.d/www.conf
启动php-fpm,启动并且加入开机自启动
systemctl start php-fpm
systemctl enable php-fpm

安装Mariadb数据库
# 安装mariadb数据库软件
yum install mariadb-server mariadb -y
# 启动数据库并且设置开机自启动
systemctl start mariadb
systemctl enable mariadb
# 设置mariadb的密码
mysqladmin password '123456'
# 验证数据库是否工作正常
mysql -uroot -p123456 -e "create database blog;charset='utf8';show databases;"
安装php探针
# 首先为php探针创建一个虚拟主机
vim /etc/nginx/conf.d/php.conf
server {
listen 80;
server_name php.iproute.cn;
root /code;
location / {
index index.php index.html;
}
location ~ \.php$ {
fastcgi_pass 127.0.0.1:9000;
fastcgi_param SCRIPT_FILENAME
$document_root$fastcgi_script_name;
include fastcgi_params;
}
}
# 测试nginx配置是否正确
nginx -t
# 重启nginx服务
systemctl restart nginx
编写php文件，在php文件中编写如下代码
vim /code/info.php
<?php
phpinfo();
?>
测试数据库
vim /code/mysqli.php
<?php
$servername = "localhost";
$username = "root";
$password = "123456";
// 创建连接
$conn = mysqli_connect($servername, $username, $password);
// 检测连接
if (!$conn) {
die("Connection failed: " . mysqli_connect_error());
}
echo "连接MySQL...成功！";
?>
安装phpmyadmin
# 为数据库管理工具创建虚拟主机
vim /etc/nginx/conf.d/mysql.conf
server {
listen 80;
server_name mysql.iproute.cn;
root /code/phpmyadmin;
location / {
index index.php index.html;
}
location ~ \.php$ {
fastcgi_pass 127.0.0.1:9000;
fastcgi_param SCRIPT_FILENAME
$document_root$fastcgi_script_name;
include fastcgi_params;
}
}
# 检查nginx配置文件，并且重启
nginx -t
systemctl restart nginx
# 下载phpmyadmin源码
wget https://files.phpmyadmin.net/phpMyAdmin/5.1.1/phpMyAdmin-5.1.1-alllanguages.zip
# 解压软件包，并且重命名
unzip phpMyAdmin-5.1.1-all-languages.zip
mv phpMyAdmin-5.1.1-all-languages phpmyadmin
# 添加session文件夹权限
chown www.www /var/lib/php/session
安装博客系统，部署虚拟主机
# 为博客创建虚拟主机
vim /etc/nginx/conf.d/typecho.conf

server {
listen 80;
server_name blog.iproute.cn;
root /code/typecho;
index index.php index.html;
location ~ .*\.php(\/.*)*$ {
root /code/typecho;
fastcgi_pass 127.0.0.1:9000;
fastcgi_index index.php;
fastcgi_param SCRIPT_FILENAME
$document_root$fastcgi_script_name;
include fastcgi_params;
}
}
# 检查nginx配置，并且重启nginx
nginx -t
systemctl restart nginx
# 下载源代码然后解压重命名
cd /code/
wget http://typecho.org/downloads/1.1-17.10.30-release.tar.gz
tar xzvf 1.1-17.10.30-release.tar.gz
mv build typecho
```

* http和TCP/IP:[计算机网络-HTTP和TCP/IP协议简介_非常之观常在险远的博客-CSDN博客_http tcp/ip](https://blog.csdn.net/m0_50275872/article/details/124138214)

