---
layout:     post                    # 使用的布局（不需要改）
title:      while              # 标题 
subtitle:   Hello World, Hello Blog  #副标题
date:       2018-01-06              # 时间
author:    chen                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - shell
---
    
  
```
#!/bin/bash
    while read line
    do
            echo $line
    done < www.txt
```


----------

    j=0
    i=1
    while ((i<=100))
    do
            j=`expr $i + $j`
            ((i++))
    done
    echo $j


----------

    FILE="/etc/passwd"
    while true
    do
            echo "the time is `date '+ %F-%T'`"
            OLD=`md5sum $FILE |cut -d " " -f 1`
            sleep 5
            NEW=`md5sum $FILE|cut -d " " -f 1`
            if [[ $OLD = $NEW  ]];then
                    echo 'the file has been modifield'
            fi
    done

