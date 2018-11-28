# NFS

   NFS，是Network File System的简写，即网络文件系统。网络文件系统是FreeBSD支持的文件系统中的一种，也被称为NFS.  NFS允许一个系统在网络上与他人共享目录和文件。通过使用NFS，用户和程序可以像访问本地文件一样访问远端系统上的文件 。
   
   
   要部署NFS服务，需要安装上面两个软件包
  1.nfs-utils：NSF主程序
    包括rpc.nfsd,rpc.mountd等等
  2.rpcbind:rpc主程序
     NFS可以被视为一个RPC程序，在启动任何一个RPC程序之前，需要做好端口映射工作，这个映射工作
     就是由rpcbind服务来完成的，因此必须先启动rpcbind服务，再启动nfs服务
     
     
    yum install nfs-utils rpcbind
    
    
    
    2、NFS文件配置：
[root@test /]# vi /etc/exports 
增加一行：
/tmp/test0920  11.11.165.0/24(rw,no_root_squash,no_all_squash,sync)
:x保存退出；

3、使配置生效：
[root@test /]# exportfs -r
注：配置文件说明：
/tmp/test0920  为共享的目录，使用绝对路径。
11.11.165.0/24


客户端的配置：
1、yum -y install nfs-utils
2、mount -t nfs  11.11.165.115:/tmp/test0920   /data 


RHEL7.3环境：
在/etc/sysconfig/nfs文件里
RPCNFSDARGS="-V 4.2"
编辑/etc/fstab
则可以
11.11.165.115:/tmp/test0920   /data        nfs4.2   defaults    0 0


[root@linuxprobe ~]# systemctl start rpcbind
[root@linuxprobe ~]# systemctl enable rpcbind
[root@linuxprobe ~]# systemctl start nfs-server
[root@linuxprobe ~]# systemctl enable nfs-server

