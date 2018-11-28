# 搭建本地yum源

mount /dev/cdrom /mnt
mkdir -p /var/www/html/centos/
cp -R /mnt/* /var/www/html/centos/

使用createrepo创建本地源
yum install createrepo* -y
cd /var/www/html/

createrepo centos/

yum源 软件包扩展
----------
cd /var/www/html/centos
wget https://archive.fedoraproject.org/pub/epel/7/x86_64/Packages/n/ntfs-3g-devel-2017.3.23-6.el7.x86_64.rpm 
createrepo --update centos/


同步外网yum源
--------
通过reposync命令获取外网yum源所有软件包 -r 指定repolist id，默认不加r 获取所有yum包， -p指定下载软件的路径。
reposync -r base -p /var/www/html/centos/
reposync -r updates -p /var/www/html/centos/
下载完成后:
createrepo /var/www/html/centos/
更新源。## 标题 ##



