---
layout:     post                    # 使用的布局（不需要改）
title:      查看linux分区block大小           # 标题 
subtitle:   Hello World, Hello Blog  #副标题
date:       2018-03-12              # 时间
author:    chen                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - linux命令
---
```
dumpe2fs /dev/sda |grep "Block size"

```


