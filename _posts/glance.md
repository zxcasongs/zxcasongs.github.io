# glance


glance-api
接收镜像API的调用，诸如镜像发现、恢复、存储。

glance-registry
存储、处理和恢复镜像的元数据，元数据包括项诸如大小和类型。

 警告

glance-registry是私有内部服务，用于服务OpenStack Image服务。不要向用户暴露该服务


数据库
存放镜像元数据，用户是可以依据个人喜好选择数据库的，多数的部署使用MySQL或SQLite。



