# linux进入救援模式

重启服务器并进入启动加载程序 （Grub） 屏幕并选择 “Ubuntu”。

按下 e，然后移动到以 linux 开头的行尾，并添加 systemd.unit=rescue.target。如果存在单词 $vt_handoff 就删除它。

现在按 Ctrl-x 或 F10 启动，
现在按回车键，然后你将得到所有文件系统都以读写模式挂载的 shell 并进行故障排除。完成故障排除后，可以使用 reboot 命令重新启动服务器。



