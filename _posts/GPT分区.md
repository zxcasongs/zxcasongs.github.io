# GPT分区
为了能支持使用大于2TB硬盘空间，需要使用GPT格式分区。
parted -s /dev/sdb mklabel gpt 设置分区格式为gpt
mkfs.xfs /dev/sdb 基于xfs文件类型格式化
mount /dev/sdb /data

https://my.oschina.net/guol/blog/61424




 

