# day10

## DBA工作内容

* 数据管理
* 用户管理
* 集群管理
* 数据备份和恢复

### 数据库管理系统种类

|                | 关系型数据库 | 非关系型数据库 |
| :------------- | :----------: | :------------: |
| 强大的查询功能 |      √       |       ×        |
| 强一致性       |      √       |       ×        |
| 二级索引       |      √       |       ×        |
| 灵活模式       |      ×       |       √        |
| 扩展性         |      ×       |       √        |
| 性能           |      ×       |       √        |

## MySQL安装

#### yum 安装

* 添加源

```
rpm -ivh https://repo.mysql.com/mysql57-community-release-el7-9.noarch.rpm
```

* yum安装

```
yum install mysql-community-server -y --nogpgcheck
```

* 启动mysql

```
systemctl start mysqld
systemctl enable mysqld
```

* 查看默认密码

``` 
grep 'temporary password' /var/log/mysqld.log
```

* 登录

``` 
mysql -uroot -p
交互式输入密码
```

#### 源码安装

###### 介绍

版本选择

* 5.7：支持MGR（mysql自带的高可用）
* https://downloads.mysql.com/archives/community/

* 下载源码编译

```
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-5.6.40.tar.gz
tar xzvf mysql-5.6.40.tar.gz
cd mysql-5.6.40
yum install -y ncurses-devel libaio-devel cmake gcc gcc-c++ glibc
```

* 创建用户

```
useradd mysql -s /sbin/nologin -M
```

* 编译安装

``` 
mkdir /application
cmake . -DCMAKE_INSTALL_PREFIX=/application/mysql-5.6.38 \
-DMYSQL_DATADIR=/application/mysql-5.6.38/data \
-DMYSQL_UNIX_ADDR=/application/mysql-5.6.38/tmp/mysql.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci \
-DWITH_EXTRA_CHARSETS=all \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_FEDERATED_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITHOUT_EXAMPLE_STORAGE_ENGINE=1 \
-DWITH_ZLIB=bundled \
-DWITH_SSL=bundled \
-DENABLED_LOCAL_INFILE=1 \
-DWITH_EMBEDDED_SERVER=1 \
-DENABLE_DOWNLOADS=1 \
-DWITH_DEBUG=0

echo $?
在进行源代码编译，或者执行命令无法确认所执行的命令是否成功执行的情况下，我们都会使用 echo $? 来进行测试。
如果返回值是0，就是执行成功；如果是返回值是0以外的值，就是失败。
make
make install
```

* 创建配置用户

```
ln -s /application/mysql-5.6.38/ /application/mysql
cd /application/mysql/support-files/
cp my-default.cnf /etc/my.cnf
cp：是否覆盖"/etc/my.cnf"？ y
```

* 创建启动脚本

```
1 cp mysql.server /etc/init.d/mysqld
```

* 初始化数据库

```
cd /application/mysql/scripts/
yum -y install autoconf
./mysql_install_db --user=mysql --basedir=/application/mysql --
datadir=/application/mysql/data/
```

* 启动数据库

```
mkdir /application/mysql/tmp
chown -R mysql.mysql /application/mysql*
/etc/init.d/mysqld start
```

* 配置环境变量

```
vim /etc/profile.d/mysql.sh
export PATH="/application/mysql/bin:$PATH"
------------------立即生效------------------
source /etc/profile
```

* systemd管理mysql

```
vim /usr/lib/systemd/system/mysqld.service
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=https://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target
[Install]
WantedBy=multi-user.target
[Service]
User=mysql
Group=mysql
ExecStart=/application/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000
```

* 设置mysql开机启动，开始服务

```
systemctl start mysqld
systemctl enable mysqld
```

* 测试登录

```
mysqladmin -uroot password '123456'
mysql -uroot -p123456
mysql> show databases;
mysql> \q
Bye
```

#### 二进制安装

* 下载二进制包并且解压

```
tps://downloads.mysql.com/archives/get/p/23/file/mysql-5.6.40-linux-glibc2.12-x86_64.tar.gz
tar xzvf mysql-5.6.40-linux-glibc2.12-x86_64.tar.gz
```

* 与编译安装相同

```
mkdir /application
mv mysql-5.6.40-linux-glibc2.12-x86_64 /application/mysql-5.6.40
ln -s /application/mysql-5.6.40 /application/mysql
cd /application/mysql/support-files
cp my-default.cnf /etc/my.cnf
cp：是否覆盖"/etc/my.cnf"？ y
cp mysql.server /etc/init.d/mysqld
cd /application/mysql/scripts
useradd mysql -s /sbin/nologin -M
yum -y install autoconf
./mysql_install_db --user=mysql --basedir=/application/mysql --
data=/application/mysql/data
vim /etc/profile.d/mysql.sh
export PATH="/application/mysql/bin:$PATH"
source /etc/profile
```

* 官方默认二进制编译报在/usr/local目录下，我们修改配置

```
sed -i 's#/usr/local#/application#g' /etc/init.d/mysqld
/application/mysql/bin/mysqld_safe
```

* 创建systemd管理文件，测试是否可以正常使用

```
vim /usr/lib/systemd/system/mysqld.service
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=https://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target
[Install]
WantedBy=multi-user.target
[Service]
User=mysql
Group=mysql
ExecStart=/application/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000
systemctl start mysqld
systemctl enable mysqld
mysqladmin -uroot password '123456'
mysql -uroot -p123456
```

#### 部署mariadb

* 安装mariadb-server

```
 yum install mariadb mariadb-server -y
```

* 初始化

```
[root@node1 ~]# systemctl start mariadb.service
[root@node1 ~]# mysql_secure_installation
Enter current password for root (enter for none): 当前root用户密码为空，所以直接
敲回车
OK, successfully used password, moving on...
Set root password? [Y/n] y 设置root密码
New password:
Re-enter new password:
Password updated successfully!
Remove anonymous users? [Y/n] y 删除匿名用户
... Success!
Disallow root login remotely? [Y/n] y 禁止root远程登录
... Success!
Remove test database and access to it? [Y/n] y 删除test数据库
- Dropping test database...
... Success!
- Removing privileges on test database...
... Success!
Reload privilege tables now? [Y/n] y 刷新授权表，让初始化生效
... Success!
```

* 启动并查看数据库状态

```
[root@node1 ~]# systemctl start mariadb.service
[root@node1 ~]# systemctl status mariadb.service
● mariadb.service - MariaDB database server
Loaded: loaded (/usr/lib/systemd/system/mariadb.service; disabled; vendor
preset: disabled)
Active: active (running) since 一 2020-09-14 22:38:17 CST; 10s ago
Process: 1634 ExecStartPost=/usr/libexec/mariadb-wait-ready $MAINPID
(code=exited, status=0/SUCCESS)
Process: 1550 ExecStartPre=/usr/libexec/mariadb-prepare-db-dir %n
(code=exited, status=0/SUCCESS)
Main PID: 1633 (mysqld_safe)
```

* 登录数据库

```
[root@node1 ~]# mysql -uroot -p
Enter password:
Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MariaDB connection id is 2
MariaDB [(none)]>
```

* 主配置文件

```
[root@node1 ~]# vim /etc/my.cnf
[client] # 客户端基本配置
port = 3306 #客户端默认连接端口
socket = /tmp/mysql.sock #用于本地连接的socket套接字
[mysqld] # 服务端基本配置
port = 3306 # mysql监听端口
socket = /tmp/mysql.sock #为MySQL客户端程序和服务器之间的本地通讯指定一个套接
字文件
user = mariadb # mysql启动用户
basedir = /usr/local/mariadb # 安装目录
datadir = /data/mysql # 数据库数据文件存放目录
log_error = /data/mysql/mariadb.err #记录错误日志文件
pid-file = /data/mysql/mariadb.pid #pid所在的目录
skip-external-locking #不使用系统锁定，要使用myisamchk,必须关闭服务器
```





