docker 命令

查看docker registry镜像
curl 10.0.0.221:5000/v2/_catalog

启动docker
dcoekr run
参数
-d  后台运行 
-i 分配一个终端
-t 

查看容器 
docker ps 
查看正运行的容器

参数
-a  查看所有容器


进入容器
docker attach 和 docker exec

dcoker attach 容器id 
可通过 Ctrl+p 然后 Ctrl+q 组合键退出 attach 终端。


docker  exec  容器id sh
-it 以交互模式打开 pseudo-TTY，执行 bash，其结果就是打开了一个 bash 终端

exit 退出容器

attach 与 exec 主要区别如下:

1. attach 直接进入容器 启动命令 的终端，不会启动新的进程。

2. exec 则是在容器中打开新的终端，并且可以启动新的进程。

3. 如果想直接在终端中查看启动命令的输出，用 attach；其他情况使用 exec。

容器改名
docker rename 

停止/开启/重启容器
docker stop/start/restart

容器进程发送 SIGKILL 信号(容器快速关闭)
docker kill


容器可能会因某种错误而停止运行。对于服务类容器，我们通常希望在这种情况下容器能够自动重启。启动容器时设置 --restart 就可以达到这个效果。
--restart=always


pause/unpause 容器
有时我们只是希望暂时让容器暂停工作一段时间，比如要对容器的文件系统打个快照，或者 dcoker host 需要使用 CPU，这时可以执行 docker pause。

处于暂停状态的容器不会占用 CPU 资源，直到通过 docker unpause 恢复运行。


删除容器

使用 docker 一段时间后，host 上可能会有大量已经退出了的容器。
这些容器依然会占用 host 的文件系统资源，如果确认不会再重启此类容器，可以通过 docker rm 删除

docker rm 一次可以指定多个容器，如果希望批量删除所有已经退出的容器，可以执行如下命令

docker rm -v $(docker ps -aq -f status=exited)
顺便说一句：docker rm 是删除容器，而 docker rmi 是删除镜像


create      创建容器  

run         运行容器  

pause       暂停容器  

unpause     取消暂停继续运行容器  

stop        发送 SIGTERM 停止容器  

kill        发送 SIGKILL 快速停止容器  

start       启动容器  

restart     重启容器  

attach      attach 到容器启动进程的终端  

exec        在容器中启动新进程，通常使用 "-it" 参数  

logs        显示容器启动进程的控制台输出，用 "-f" 持续打印  

rm          从磁盘中删除容器



docker run -itd --network=host -h busy1 busybox 指定网络
brctl show 查看网络

docker network create -d bridge --subnet 172.22.0.0/24 --getway 172.22.0.1 my_net  创建bridge类型网络

docker inspect ccfc808b46b5 查看新创建的网络

docker run -itd --network=my_net --ip 172.22.0.8 -h busy1 busybox
