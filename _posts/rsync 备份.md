# rsync 备份

sync(remote sync) 是用于同步某一位置文件和目录到另一位置的有效方法。

语法:

rsync options source destination



rsync – zvr在本地服务器上同步两个目录:

    $ rsync -zvr /var/opt/installation/inventory/ /root/temp
    building file list ... done
    sva.xml
    svB.xml
    .
    sent 26385 bytes  received 1098 bytes  54966.00 bytes/sec
    total size is 44867  speedup is 1.63
    
在上面rsync例子中：
• –z：允许压缩
• –v：冗长
• –r：递归

2. 用rsync –a同步时间戳

rsync的-a选项是archive的意思，加了之后有以下作用：
• 递归模式
• 同步软链接
• 同步权限
• 同步时间戳
• 同步属主和属组


现在，我们执行与例1一样的命令，不同的是使用-a选项。

    $ rsync -azv /var/opt/installation/inventory/ /root/temp/
    building file list ... done
    ./
    sva.xml
    svB.xml
    .
    sent 26499 bytes  received 1104 bytes  55206.00 bytes/sec
    total size is 44867  speedup is 1.63

我们可以看到，rsync将源目录下文件的时间戳也同步过来了。

$ ls -l /var/opt/installation/inventory/sva.xml /root/temp/sva.xml
-r--r--r-- 1 root  bin  949 Jun 18  2009 /var/opt/installation/inventory/sva.xml
-r--r--r-- 1 root  bin  949 Jun 18  2009 /root/temp/sva.xml


将远程文件同步到本地服务器：

    $ rsync -avz linuxprobe@192.168.200.10:/var/lib/rpm /root/temp
    Password:
    receiving file list ... done
    rpm/
    rpm/Basenames
    .
    sent 406 bytes  received 15810230 bytes  2432405.54 bytes/sec
    total size is 45305958  speedup is 2.87
    
远程同步时，Rsync可以指定使用某一shell进行安全传输。如：使用rsync –e ssh选择ssh进行安全连接。

    $ rsync -avz -e ssh linuxprobe@192.168.200.10:/var/lib/rpm /root/temp
    Password:
    receiving file list ... done
    rpm/
    rpm/Basenames
    sent 406 bytes  received 15810230 bytes  2432405.54 bytes/sec
        total size is 45305958  speedup is 2.87
        
        
https://www.linuxprobe.com/rsync-use-sample.html

## rsync备份服务器 ##
blog.51cto.com/oldboy/2307393

    
    
  

    

rsync+sersync
-------------

https://blog.csdn.net/orangleliu/article/details/46699147


