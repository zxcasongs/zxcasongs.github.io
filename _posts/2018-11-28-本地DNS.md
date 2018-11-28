# 本地DNS

    yum install bind bind-utils bind-chroot -y

配置文件：

/etc/named.conf主配置文件

/etc/named/zone文件

如过你安装了bind-chroot，则bind-chroot会将bind服务放置于一个伪根目录下，这样如何黑客攻破了你的服务器，进到来的就是在伪根目录下，不会危害到你真正的根目录，是一个安全的措施。

    vim /var/named/chroot/etc/named.conf
    options {
        listen-on port 53 { any; };<span>	</span>## 修改成any
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        allow-query     { any; };<span>	</span>## 修改成any


----------


    zone "amos.com" IN { #amos为我设置的域名。可自定义修改为自己的域名
        type master;<span>		</span>## DNS服务类型，默认为master，这里我们是搭建的第一个DNS服务器，则就定为主服务器，即默认的master
        file "creatson.com.zone";<span>	</span>## 域名配置文件名  ，这个文件名会在 /var/named/chroot/var/named/ 这个目录下去寻找
        }；


----------

    cp /var/named/chroot/var/named/named.localhost /var/named/chroot/var/named/amos.com.zone<span>	</span>## 已named.localhost为基础模板，复制一份并改为上一步配置的名称
    vim /var/named/chroot/var/named/amos.com.zone<span>	</span>## 编辑
    -------------------------------------------以下为内容-------------------------------------------------
    $TTL 1D<span>		</span>## TTL 就是修改配置的生效时间，本质上就是这个域的DNS信息在别的DNS服务器的缓存中存在多久，默认为一天
    @       IN SOA  @ rname.invalid. (
                                            0       ; serial<span>	</span>## 这个是配置的编号，每次修改完配置后这个编号变化一下，通常是直接+1，这样从服务器就能知道有修改并更新配置
                                            1D      ; refresh<span>	</span>## 从服务器刷新时间，默认一天刷新一次<span>	</span>
                                            1H      ; retry<span>		</span>## 如果刷新失败，默认1小时重试一次
                                            1W      ; expire<span>	</span>## 缓存过期时间，一周<span>	</span>
                                            3H )    ; minimum<span>	</span>## 这个不知道干嘛的，默认就行
            NS      @
            A       127.0.0.1
            AAAA    ::1
    eip     IN A    192.168.9.89<span>	</span>## 配置子域名，也就是主机名，对应的IP地址，A记录就是对应 IPV4 地址。  IN 表示 internel 网络
    jenkins IN A    192.168.9.90<span>	</span>
    gitlab  IN A    192.168.9.55
    
    
systemctl statr named-chroot<span>		</span>## 启动服务，如何使用了bind-chroot 则启动 named-chroot  否则启动  named
systemctl enable named-chroot<span>		</span>## 设为开机启动



DNS从服务器
-------
vim /var/named/chroot/etc/named.rfc1912.zones
zone "amos.com" IN {
        type slave;<span>			</span>## 类型改为 salve 从
        masters { 192.168.9.57; };<span>	</span>## 配置 master服务器 地址
        file "salve/amos.com";<span>		</span>## 配置拉取后的存放路径  同样是在 /var/named/chroot/var/named/ 下的路径 ，这个slave文件夹是需要自己创建并赋予权限的
};


OK，就这样就可以了，将域名写上，设为从服务器，配置master地址就可以了，然后其他的与 主服务器相同，正常启动服务就可以了，然后可以去 salve/目录下查看是否正常拉取了主服务器的配置文件
