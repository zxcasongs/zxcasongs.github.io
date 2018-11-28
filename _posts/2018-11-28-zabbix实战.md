zabbix实战
========

1.1交换机上开启snmp
    config t
    snmp-server conmmunity public ro
    end

1.2在zabbix上添加监控
    设置SNMP interface

1.3关联监控模板


----------


**2.1 zabbix服务器**


----------


Zabbix服务器进程是以守护进程（Deamon）运行的。服务器的启动可以通过执行以下命令来完成：
shell> cd sbin
shell> ./zabbix_server
Zabbix server下的命令行参数：
-c --config <file>        配置文件的绝对路径 (默认路径： /etc/zabbix/zabbix_server.conf)
-R --runtime-control <选项>     执行管理能力
-h --help                       帮助提示
-V --version                    显示版本号

命令行参数示例:

shell> zabbix_server -c /usr/local/etc/zabbix_server.conf
shell> zabbix_server --help
shell> zabbix_server -V


----------


**2.2 zabbix agent**


----------


Zabbix agent部署在监控的目标上，主动监测本地的资源和应用(硬件驱动，内存，处理器统计等)。


*被动（passive）和主动（active）检查*
Zabbix agents可以执行被动和主动两种检查方式。

在passive check 模式中agent应答数据请求，Zabbix server（或者proxy）询问agent数据,如CPU 的负载情况，然后Zabbix agent回送结果。

Active checks 处理过程将相对复杂。 Agent必须首先从Zabbix sever索取监控项列表以进行独立处理，然后周期性地发送新的值给server。

执行被动或主动检查是通过选择相应的监测项目类型来配置的。item type. Zabbix agent处理监控项类型有’Zabbix agent’和’Zabbix agent (active)’。


**运行 agent进程**
Zabbix agent运行在被监控主机上。

Zabbix agent在UNIX上以守护进程运行。Agent（独立的守护进程）可以通过执行代码运行：

shell> cd sbin
shell> ./zabbix_agentd

 1. 显示所有内置监控项和它们的值
 2. 使用指定的配置文件中定义的“mysql.ping”键来测试用户参数

shell> zabbix_agentd –print
shell> zabbix_agentd -t "mysql.ping" -c /etc/zabbix/zabbix_agentd.conf


**进程用户**
Zabbix agent是unix平台上设计在非root账户下的。它会以其他任何非root用户启动的进程一样的方式运行。所以，您可以使用任意非root用户运行agent，且不会产生任何问题。

如果您在’root’账户下运行，它将切换到硬编码的“zabbix”用户，该用户必须存在于您的系统上。如果您只想以’root’方式运行proxy，您必须在proxy配额文件里修改‘AllowRoot‘参数。


----------
zabbix proxy


----------
Zabbix Proxy是一个可以从一个或多个受监控设备收集监控数据，并将信息发送到Zabbix sever的进程，基本上是代表sever工作的。 所有收集的数据都在本地进行缓存，然后传送到proxy所属的Zabbix sever。


Proxy 进程
Zabbix proxy以守护进程的方式运行。Proxy可以通过执行代码运行。

shell> cd sbin
shell> ./zabbix_proxy
您可以使用Zabbix agent下的命令行参数:

-c --config <file>              配置文件的绝对路径
-R --runtime-control <option>    执行管理功能
-h --help                      帮助
-V --version                   显示版本号


进程用户
Zabbix proxy是设计在非root账户下的。它会以其他任何非root用户启动的进程一样的方式运行。 所以，您可以使用任意非root用户运行agent，且不会产生任何问题。

如果您在’root’账户下运行，它将切换到硬编码的“zabbix”用户，该用户必须存在于您的系统上。如果您只想以’root’方式运行proxy，您必须在proxy配额文件里修改‘AllowRoot‘参数。


----------
java gateway


----------

从Zabbix 2.0版本开始，以Zabbix守护进程的形式原生支持监控JMX程序出现了，它被称为Zabbix Java gateway。Zabbix gateway 是用Java语言写成。要查得一台主机特定的JMX计数器值，Zabbix server向Zabbix Java gateway发送请求，后者使用JMX管理API 去请求远程的有关应用。应用不需要额外安装软件，只需要启动时在命令行指定-Dcom.sun.management.jmxremote 即可。

    

略
----------
安装




