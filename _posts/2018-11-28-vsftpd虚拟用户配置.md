# vsftpd虚拟用户配置

安装虚拟用户用到的软件和认证

    yum install pam* libdb-utils libdb* --skip-broken -y

    vi /opt/ftpuser.txt
        chenvip1
        000000
        chenvip2
        000000



    db_load -T -t hash -f /opt/ftpuser.txt /etc/vsftpd/vsftpd_login.db

选项-T允许应用程序能够将文本文件转译载入进数据库。
-t hash使用hash码加密
-f 指定包含用户名和密码文本文件。此文件格式要示：奇数行用户名、偶数行密码

    chmod 700 /etc/vsftpd/vsftpd_login.db 
    
配置pam认证文件，在/etc/pam.d/vsftpd行首加入两行代码:

    auth required pam_userdb.so db=/etc/vsftpd/vsftpd_login.db
    account required pam_userdb.so db=/etc/vsftpd/vsftpd_login.db

vsftpd虚拟用户需要映射到一个系统用户，该用户不用登陆，不需要密码。

    useradd -s /sbin/nologin chenvip
    
在vsftpd.conf文件下配置内容:
pam_service_name=vsftpd:虚拟用户启动pam认证
userlist_enable=YES:启用虚拟用户
tcp_wrappers=YES
guest_enable=YES
guest_username=chenvip:映射虚拟用户为chenvip
user_config_dir=/etc/vsftpd/vsftpd/vsftpd_user_conf:虚拟用户配置文件目录
virtual_use_local_privs=YES:虚拟用户与本地用户相同权限

修改虚拟用户配置文件

    vi /etc/vsftpd/vsftpd_user_conf/chenvip1


local_root=/home/chenvip/chenvip1:虚拟用户文件路径
write_enable=YES：允许用户写权限
anon_world_readable_only=YES：允许匿名用户下载
anon_upload_enable=YES：允许匿名用户上传文件
anon_mkdir_write_enable=YES：允许匿名用户创建目录
anon_other_write_enable=YES：允许匿名用户其他权限







