---
layout:     post                    # 使用的布局（不需要改）
title:      mysql读写分离架构-Atlas              # 标题 
subtitle:   Hello World, Hello Blog  #副标题
date:       2018-02-06              # 时间
author:    chen                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 数据库
---

###### 1.功能介绍
- 读写分离
- 从库负载均衡
- IP过滤
- SQL语句黑白名单
- DBA可平滑上下线DB
- 自动摘除宕机的DB
- 自动分表

###### 2.安装配置

```
下载地址
https://github.com/Qihoo360/Atlas/releases
注意：
1、Atlas只能安装运行在64位的系统上
2、Centos 5.X安装 Atlas-XX.el5.x86_64.rpm，Centos 6.X安装Atlas-XX.el6.x86_64.rpm。
3、后端mysql版本应大于5.1，建议使用Mysql 5.6以上
```


```
yum install -y Atlas*

cd /usr/local/mysql-proxy/conf
mv test.cnf test.cnf.bak
 
vi test.cnf （test为模块名）
[mysql-proxy]
admin-username = user
管理接口的用户名

admin-password = pwd
管理接口的密码

proxy-backend-addresses = 10.0.0.55:3306
Atlas后端连接的MySQL主库的IP和端口，可设置多项，用逗号分隔

proxy-read-only-backend-addresses = 10.0.0.51:3306,10.0.0.53:3306
从库

pwds = repl:3yb5jEku5h4=,mha:O2jBXONX098=
用户名和密码配置项，需要和主从复制配置的用户名和密码配置一样

daemon = true
keepalive = true
后台运行


event-threads = 8
工作线程数，对Atlas的性能有很大影响，可根据情况适当设置

log-level = message
日志级别，分为message、warning、critical、error、debug五个级别

log-path = /usr/local/mysql-proxy/log
日志存放的路径

sql-log=ON
SQL日志的开关，可设置为OFF、ON、REALTIME，OFF代表不记录SQL日志，ON代表记录SQL日志，REALTIME代表记录SQL日>志且实时写入磁盘，默认为OFF

sql-log-slow = 1000
慢日志输出设置。当设置了该参数时，则日志只输出执行时间超过sql-log-slow（单位：ms)的日志>记录。不设置该参数则输出全部日志。

instance = web
实例名称，用于同一台机器上多个Atlas实例间的区分

proxy-address = 0.0.0.0:33060
Atlas监听的工作接口IP和端口

admin-address = 0.0.0.0:2345
Atlas监听的管理接口IP和端口

tables = person.mt.id.3
分表设置，此例中person为库名，mt为表名，id为分表字段，3为子表数量，可设置多项，以逗号分>隔，若不分表则不需要设置该项

charset=utf8
默认字符集，设置该项后客户端不再需要执行SET NAMES语句

client-ips = 127.0.0.1, 192.168.1
允许连接Atlas的客户端的IP，可以是精确IP，也可以是IP段，以逗号分隔，若不设置该项则允许所>有IP连接，否则只允许列表中的IP连接
启动atlas

/usr/local/mysql-proxy/bin/mysql-proxyd test start
ps -ef |grep proxy
```


###### 3. Atlas功能测试


```
测试读操作：
mysql -umha -pmha  -h 10.0.0.53 -P 33060 
db03 [(none)]>select @@server_id;
测试写操作：
mysql> begin;select @@server_id;commit;
```


###### 4. 生产用户要求

++开发人员申请一个应用用户 app(  select  update  insert)  密码123456,要通过10网段登录++

```
1. 在主库中,创建用户
grant select ,update,insert on *.* to app@'10.0.0.%' identified by '123456';
2. 在atlas中添加生产用户
/usr/local/mysql-proxy/bin/encrypt  123456      ---->制作加密密码
vim test.cnf
pwds = repl:3yb5jEku5h4=,mha:O2jBXONX098=,app:/iZxz+0GRoA=
/usr/local/mysql-proxy/bin/mysql-proxyd test restart
[root@db03 conf]# mysql -uapp -p123456  -h 10.0.0.53 -P 33060
```



###### 5. Atlas基本管理

```
连接管理接口：

mysql -uuser -ppwd -h127.0.0.1 -P2345
打印帮助：

mysql> select * from help;

查询后端所有节点信息：
mysql>  SELECT * FROM backends    ;
+-------------+----------------+-------+------+
| backend_ndx | address        | state | type |
+-------------+----------------+-------+------+
|           1 | 10.0.0.55:3306 | up    | rw   |
|           2 | 10.0.0.51:3306 | up    | ro   |
|           3 | 10.0.0.53:3306 | up    | ro   |
+-------------+----------------+-------+------+
3 rows in set (0.00 sec)


动态添加删除节点：

REMOVE BACKEND 3;

动态添加节点：

ADD SLAVE 10.0.0.53:3306;

保存配置到配置文件

SAVE CONFIG;
```
