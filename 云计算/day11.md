# day11

* 功能

```
1.索引的功能就是快速查找
2.mysql中primary key，unique，联合唯一也都是索引，可以加速查找，；也可以约束功能。
```

* 常用索引

```bash
普通索引INDEX：加速查找
唯一索引：
	-主键索引primary key：加速查找+约束（不为空，不能重复）
	-唯一索引unique：加速查找+约束（不能重复）
联合索引：
	-primary key（id,name):联合主键索引
	-unique：联合唯一索引
	-index：联合普通索引
```

* 索引类型

```
索引类型：
hash类型：查询单条快，范围查询慢
btree类型：b+树，层数越多，数据量指数级增长（innodb默认支持）
#不同存储引擎支持的索引类型不同
InnoDB支持事务，支持行级别锁定，支持B-tree，Full-text等索引，不支持
#CREATE TABLE 表名 (
字段名1 数据类型 [完整性约束条件…],
字段名2 数据类型 [完整性约束条件…],
[UNIQUE | FULLTEXT | SPATIAL ] INDEX | KEY
[索引名] (字段名[(长度)] [ASC |DESC])
);
#方法二：CREATE在已存在的表上创建索引
CREATE [UNIQUE | FULLTEXT | SPATIAL ] INDEX 索引名
ON 表名 (字段名[(长度)] [ASC |DESC]) ;
#方法三：ALTER TABLE在已存在的表上创建索引
ALTER TABLE 表名 ADD [UNIQUE | FULLTEXT | SPATIAL ] INDEX
索引名 (字段名[(长度)] [ASC |DESC]) ;
#删除索引：DROP INDEX 索引名 ON 表名字; 索引；
MyISAM 不支持事务，支持表级别锁定，支持 B-tree、Full-text 等索引，不支持 Hash 索引；
Memory 不支持事务，支持表级别锁定，支持 B-tree、Hash 等索引，不支持 Full-text 索引；
NDB 支持事务，支持行级别锁定，支持 Hash 索引，不支持 B-tree、Full-text 等索引；
Archive 不支持事务，支持表级别锁定，不支持 B-tree、Hash、Full-text 等索引；
```

### 主从复制原理

#### 复制过程

* 主服务器上任何的更新操作会被写入到二进制日志文件中
* 从服务器上的IO线程
* 检测主服务器的二进制文件变化
* 同步主服务器二进制日志到本地中继日志中
* 从服务器上的sql线程负责读取和执行中继日志中的sql

#### 应用场景

* 一主多从对应读写分离
  * 写操作由主服务器
  * 读操作由从服务器

- 主服务器：drop
  * 要去备份，在服务器上通过LVM快照进行备份
- 主服务器挂了
  * 高可用模型：多主架构
  * 任何一台服务器即是主服务器也是从服务器
- 高可用架构：MHA架构



### 实验

#### 主数据库

```
修改配置文件
[root@server2 ~]# cat /etc/my.cnf
[mysqld]
......
server_id =1
log_bin=mysql-bin
......
[root@server2 ~]# systemctl restart mariadb.service
创建主从复制用户
[root@server2 ~]# mysql -uroot -p1
MariaDB [(none)]> grant replication slave on *.* to rep@'192.168.80.%'
identified by '123456'
记录主库位置点
MariaDB [(none)]> show master status;
+------------------+----------+--------------+------------------+
| File | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000001 | 395 | | |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
```

#### 从库操作

``` 
修改配置文件
[root@server3 ~]# cat /etc/my.cnf
......
server_id =5
......
[root@server3 ~]# systemctl restart mariadb.service
从库开始连接主库
MariaDB [(none)]> change master to
master_host='192.168.80.129',master_port=3306,master_user='rep',master_passw
ord='123456',master_log_file='mysql-bin.000001',master_log_pos=395;
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> start slave;
Query OK, 0 rows affected (0.01 sec)

```

#### 验证结果

``` 
# 在主库上建库
MariaDB [(none)]> create database db1;
Query OK, 1 row affected (0.00 sec)
# 在从库上查库
MariaDB [(none)]> show databases;
+--------------------+
| Database |
+--------------------+
| information_schema |
| db1 |
| mysql |
| performance_schema |
+--------------------+
```

### 补充

# 简介

业务的发展，一台数据服务器无法满足需求，负担过重。 此时，需要减压，实现负载平衡的读写分离，设为一主一从或一主多从。

由于主服务器只负责写入，只负责从服务器读取，因此提高了效率并减轻了压力。

主从副本分为以下几部分

主从同步：用户写数据的主服务器必须与从属服务器同步才能告诉用户写成功了，等待时间很长。 主从异步：用户访问写入数据主体后，将立即返回给用户。 主从半同步：用户访问写入数据的主服务器进行写入，同步任一从属服务器后，将成功返回给用户。

#### 一主一从

![img](https://p6.toutiaoimg.com/origin/pgc-image/2b45b84427f142ffa399e4c99a3ba6c0?from=pc)

#### 一主多从

![img](https://p6.toutiaoimg.com/origin/pgc-image/7aba1588fbba4424ad6b3c6bf7bcf8aa?from=pc)

一主从和一主从是我们现在最常见的主从结构，使用方便有效，不仅可以实现HA，还可以读写分离，可以提高集群的同时性。

#### 多主一从

![img](https://p6.toutiaoimg.com/origin/pgc-image/3706a4d5ec164ed8b443c05f53bf93b6?from=pc)

多主机可以将多个MySQL数据库备份到存储性能高的服务器上。

#### 双主复制

![img](https://p6.toutiaoimg.com/origin/pgc-image/2a7bb87caeed4780b24c322393ed6640?from=pc)

可以进行双重主复制，即相互进行主从复制，每个主服务器既是主服务器，也是另一个服务器的salve。 其中一个所做的更改将通过复制应用到另一个数据库。

#### 级联复制

![img](https://p6.toutiaoimg.com/origin/pgc-image/91d5a608a6dc4ecaaebde43ea3c6924a?from=pc)

在级联复制模式下，一些从属数据同步连接到从属节点，而不是主节点。

如果主节点有太多的从节点，复制的一部分性能将受损，因此将主节点连接到3~5个从节点，其他从节点作为2次或3次连接到从节点，从而实现了质量

#### 原理

MySQL主从复制基于主服务器，通过二进制日志跟踪对数据库的所有更改。 因此，要进行复制，必须在主服务器上启用二进制记录。

各从站服务器从主服务器接收日志中记录的数据。 从服务器连接到主服务器时，通知主服务器从服务器日志读取上次更新成功的位置。

服务器将接收此后发生的所有更新，并在主机上执行相同的更新。 然后，阻止等待主服务器通知的更新。

来自服务器的备份不会干扰主服务器，并且主服务器可以在备份期间继续更新。

* 博客：https://www.cnblogs.com/qianyueric/p/14822012.html

### MHA架构

MHA能够在较短的时间内实现自动故障检测和故障转移，通常在10-30秒以内;在复制框架中，MHA能够很好地解决复制过程中的数据一致性问题，由于不需要在现有的replication中添加额外的服务器，仅需 要一个manager节点，而一个Manager能管理多套复制，所以能大大地节约服务器的数量;另外，安装简单，无性能损耗，以及不需要修改现有的复制部署也是它的优势之处。 MHA还提供在线主库切换的功能，能够安全地切换当前运行的主库到一个新的主库中(通过将从库提升为 主库),大概0.5-2秒内即可完成。

#### 工作流程

* 宕机的master二进制日志保存下来
* 找到binlog位置点新的slave
* 找到binlog位置用relay log修复其他slave
* 将宕机的master保存的二进制恢复到新的位置点slave
* 将含有新位置点的slave提升为master
* 将其他slave重新指向master，开始主从复制

![img](https://img-blog.csdn.net/20170510121455979?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGlsaW5nemo=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 部署：https://www.cnblogs.com/fawaikuangtu123/p/10927888.html
