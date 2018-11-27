# mysql
修改密码

    mysql> set password for root@localhost=password('new')
    
    mysqladmin -uroot -p000000 password 123
    
    use mysql;
    update user set password=password('123') where user='root';


mysql密码丢失

    # pkill mysql               #先关闭mysql服务
     #使用--skip-grant-tables启动mysql，忽略授权登陆验证
    # mysqld_safe --defaults-file=/etc/my.cnf --skip-grant-tables &
    # mysql                   #此时再登陆，已经不需要密码了
    mysql> update mysql.user set password=password('abc123') where user='root' and host="localhost";            #设置新的密码
    mysql> flush privileges;
    # mysql -uroot -pabc123     #再次用新设置的密码登陆即可

