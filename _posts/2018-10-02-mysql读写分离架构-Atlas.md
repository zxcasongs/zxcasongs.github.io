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
admin-password = pwd
proxy-backend-addresses = 10.0.0.55:3306
proxy-read-only-backend-addresses = 10.0.0.51:3306,10.0.0.53:3306
pwds = repl:3yb5jEku5h4=,mha:O2jBXONX098=
daemon = true
keepalive = true
event-threads = 8
log-level = message
log-path = /usr/local/mysql-proxy/log
sql-log=ON
proxy-address = 0.0.0.0:33060
admin-address = 0.0.0.0:2345
charset=utf8
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
