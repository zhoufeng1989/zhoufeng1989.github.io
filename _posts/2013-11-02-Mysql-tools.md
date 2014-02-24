---
layout: post
title: Mysql tools
tags: [MySQL]
---
总结了常用的MySQL工具:

**mysqldump**:备份数据库表

mysqldump常用的一些选项:
--databases
--tables
--where #dump满足条件的数据
-d --no-data #只dump表结构，不包括数据

**mysql**:与MySQL交互的命令行工具

mysql常用的一些选项
-e 执行sql
--skip-column-names 去掉列名，只有数据，用于在管道中直接处理数据。


**mysqlbinlog**: 解析MySQL二进制log

mysqlbinlog --database=db_name \
--start-date="yyyy-mm-dd HH:MM:SS" \ 
--stop-date="yyyy-mm-dd HH:MM:SS" \
/path/to/mysql-bin.log 

**mysqldumpslow**:分析慢日志 

首先得配置my.cnf：
log_slow_queries = /path/slow.log # 定义log位置，注意要有写入的权限

具体的使法如下：

mysqldumpslow -s c -t 40 /path/slow.log
出来的结果是访问次数最多的40个sql，几个参数大概意思如下：
-t 显示多少条
-s 排序，默认是at。c是次数，t是时间，l是lock时间，r是返回结果。如果是ac，at，al，ar则是升序
-g 可以用正则匹配部分语句

**mysqlsla**: 分析日志
-lt 日志类型，如slow,binary
--sort META，按照META来排序,如c_sum(sql次数),t_sum(总时间，只有slow log支持),t_avg(平均每条sql的执行时间,只有slow log支持)
--top n,只打印排名前n的数据
具体见[这里](http://hackmysql.com/mysqlsla)

**切分慢日志**
/bin/mv mysql_slow_log dest_log 
mysqladmin -uusername -ppass flush-logs
flush-logs会自动切分binary log ，reopen error log and slow log
