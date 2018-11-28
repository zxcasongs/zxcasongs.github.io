# cinder

OpenStack块存储服务(cinder)为虚拟机添加持久的存储，块存储提供一个基础设施为了管理卷，以及和OpenStack计算服务交互，为实例提供卷。此服务也会激活管理卷的快照和卷类型的功能。


cinder-api
接受API请求，并将其路由到``cinder-volume``执行。

cinder-volume
与块存储服务和例如``cinder-scheduler``的进程进行直接交互。它也可以与这些进程通过一个消息队列进行交互。``cinder-volume``服务响应送到块存储服务的读写请求来维持状态。它也可以和多种存储提供者在驱动架构下进行交互。


cinder-scheduler守护进程
选择最优存储提供节点来创建卷。其与``nova-scheduler``组件类似。

cinder-backup守护进程
``cinder-backup``服务提供任何种类备份卷到一个备份存储提供者。就像``cinder-volume``服务，它与多种存储提供者在驱动架构下进行交互。



在 [DEFAULT 部分，配置``my_ip`` 来使用控制节点的管理接口的IP 地址。

    [DEFAULT]
    ...
    my_ip = 10.0.0.11
    
 在 [oslo_concurrency] 部分，配置锁路径：

     [oslo_concurrency]
    ...
    lock_path = /var/lib/cinder/tmp
初始化块设备服务的数据库：

     su -s /bin/sh -c "cinder-manage db sync" cinder

