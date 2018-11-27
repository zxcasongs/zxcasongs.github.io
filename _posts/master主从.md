# master主从

修改主配置文件:
vi /etc/my.cnf添加:

    server-id=1
    log-bin=mysql-bin
重启数据库
新建tongbu用户授权：

    grant replication slave on *.* to tongbu@'%' identified by '000000';
    show master status;
修改slave配置文件：
vi /etc/my.cnf
server-id=2
重启数据库


    change master to master_host='192.168.200.5',master_user='tongbu',master_password='000000',master_log_file='mysql-bin.000001',master_log_pos=313;
    start slave ;
    show slave status\G





