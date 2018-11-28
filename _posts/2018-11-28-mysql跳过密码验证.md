# mysql 跳过密码验证
首先停止数据库，再以跳过权限验证方式进入。

    systemctl stop mariadb
    
    /usr/bin/mysqld_safe --user=mysql --skip-grant-tables &

重新设置密码
use mysql;
update user set password=password('000000') where user ='root';




