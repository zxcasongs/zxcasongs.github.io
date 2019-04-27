---
layout:     post                    # 使用的布局（不需要改）
title:    pt-query-digest查询日志分析            # 标题 
subtitle:   Hello World, Hello Blog  #副标题
date:       2019-04-17              # 时间
author:    chen                     # 作者
header-img: img/post-bg-hacker.jpg   #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - mysql
---

> pt-query-digest是用于分析mysql慢查询的一个工具，它可以分析binlog、General log、slowlog，也可以通过SHOWPROCESSLIST或者通过tcpdump抓取的MySQL协议数据来进行分析。可以把分析结果输出到文件中，分析过程是先对查询语句的条件进行参数化，然后对参数化以后的查询进行分组统计，统计出各查询的执行时间、次数、占比等，可以借助分析结果找出问题进行优化。

##### 安装

```
wget percona.com/get/pt-query-digest 
chmod u+x pt-query-digest
```

##### 语法
> pt-query-digest [OPTIONS] [FILES] [DSN]


```
--create-review-table  当使用--review参数把分析结果输出到表中时，如果没有表就自动创建。
--create-history-table  当使用--history参数把分析结果输出到表中时，如果没有表就自动创建。
--filter  对输入的慢查询按指定的字符串进行匹配过滤后再进行分析
--limit限制输出结果百分比或数量，默认值是20,即将最慢的20条语句输出，如果是50%则按总响应时间占比从大到小排序，输出到总和达到50%位置截止。
--host  mysql服务器地址
--user  mysql用户名
--password  mysql用户密码
--history 将分析结果保存到表中，分析结果比较详细，下次再使用--history时，如果存在相同的语句，且查询所在的时间区间和历史表中的不同，则会记录到数据表中，可以通过查询同一CHECKSUM来比较某类型查询的历史变化。
--review 将分析结果保存到表中，这个分析只是对查询条件进行参数化，一个类型的查询一条记录，比较简单。当下次使用--review时，如果存在相同的语句分析，就不会记录到数据表中。
--output 分析结果输出类型，值可以是report(标准分析报告)、slowlog(Mysql slow log)、json、json-anon，一般使用report，以便于阅读。
--since 从什么时间开始分析，值为字符串，可以是指定的某个”yyyy-mm-dd [hh:mm:ss]”格式的时间点，也可以是简单的一个时间值：s(秒)、h(小时)、m(分钟)、d(天)，如12h就表示从12小时前开始统计。
--until 截止时间，配合—since可以分析一段时间内的慢查询。
```

##### 用法示例
###### 直接分析慢查询文件:

```
pt-query-digest  slow.log > slow_report.log
```

![image](/img/slow1.png)


第一部分：总体统计结果
```
Overall: 总共有多少条查询，上例为总共266个查询。
Time range: 查询执行的时间范围。
unique: 唯一查询数量，即对查询条件进行参数化以后，总共有多少个不同的查询，该例为55。
total: 总计   min:最小   max: 最大  avg:平均
95%: 把所有值从小到大排列，位置位于95%的那个数，这个数一般最具有参考价值。
median: 中位数，把所有值从小到大排列，位置位于中间那个数。
```
第二部分：查询分组统计结果

```
由上图可见，这部分对查询进行参数化并分组，然后对各类查询的执行情况进行分析，结果按总执行时长，从大到小排序。
Response: 总的响应时间。
time: 该查询在本次分析中总的时间占比。
calls: 执行次数，即本次分析总共有多少条这种类型的查询语句。
R/Call: 平均每次执行的响应时间。
Item : 查询对象
```
第三部分：每一种查询的详细统计结果

```
由上图可见，12号查询的详细统计结果，最上面的表格列出了执行次数、最大、最小、平均、95%等各项目的统计。
Databases: 库名
Users: 各个用户执行的次数（占比）
Query_time distribution : 查询时间分布, 长短体现区间占比，本例中1s-10s之间查询数量是10s以上的两倍。
Tables: 查询中涉及到的表
Explain: 示例
```






###### 分析最近12小时内的查询：

```
pt-query-digest  --since=12h  slow.log > slow_report2.log
```
###### 分析指定时间范围内的查询：

```
pt-query-digest slow.log --since '2014-04-17 09:30:00' --until '2014-04-17 10:00:00'> > slow_report3.log
```

###### 分析指含有select语句的慢查询


```
pt-query-digest--filter '$event->{fingerprint} =~ m/^select/i' slow.log> slow_report4.log
```

###### 针对某个用户的慢查询


```
pt-query-digest--filter '($event->{user} || "") =~ m/^root/i' slow.log> slow_report5.log
```

###### 查询所有所有的全表扫描或full join的慢查询


```
pt-query-digest--filter '(($event->{Full_scan} || "") eq "yes") ||(($event->{Full_join} || "") eq "yes")' slow.log> slow_report6.log
```

###### 把查询保存到query_review表


```
pt-query-digest  --user=root –password=abc123 --review  h=localhost,D=test,t=query_review--create-review-table  slow.log
```

###### 查询保存到query_history表

```
pt-query-digest  --user=root –password=abc123 --review  h=localhost,D=test,t=query_ history--create-review-table  slow.log_20140401
pt-query-digest  --user=root –password=abc123--review  h=localhost,D=test,t=query_history--create-review-table  slow.log_20140402
```


###### 通过tcpdump抓取mysql的tcp协议数据，然后再分析

```
tcpdump -s 65535 -x -nn -q -tttt -i any -c 1000 port 3306 > mysql.tcp.txt
pt-query-digest --type tcpdump mysql.tcp.txt> slow_report9.log
```


###### 分析binlog

```
mysqlbinlog mysql-bin.000093 > mysql-bin000093.sql
pt-query-digest  --type=binlog  mysql-bin000093.sql > slow_report10.log
```


###### 分析general log

```
pt-query-digest  --type=genlog  localhost.log > slow_report11.log
```


转载自: https://blog.csdn.net/seteor/article/details/24017913