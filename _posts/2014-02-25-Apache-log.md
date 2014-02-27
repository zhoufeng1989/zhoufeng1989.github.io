---
layout: post
title: Apache 日志过滤(filter)及转储(rotate)
tags: [Apache]
---

今天，线上的apache出现了问题，查看日志，出现大片错误
{% highlight bash %}
child pid 10727 exit signal File size limit exceeded (25)
piped log program '/usr/local/sbin/cronolog /path/to/access.log.%Y%m%d' failed unexpectedly
{% endhighlight %}

去网上一查，是因为日志文件超过了2G，导致cronolog程序继续写入日志失败，有人建议用logrotate代替cronolog
切分日志，但是logrotate只支持cron模式运行，不能像cronolog一样通过管道来切分日志。    
为什么日志会这么多呢？看了一下大量请求返回的都是404，这部分请求前些时间做了防盗链处理，更改了资源请求的url，但是盗链应用仍从旧链接访问，cdn请求不到资源便访问我们的apache，所以才有了这些请求。。好吧，那就不记录这些请求的日志，具体做法:

{% highlight bash %}
#设置一个条件
SetEnvIf Request_URI "pattern" dontlog
#当请求不满足dontlog条件时，才记录日志
CustomLog "|/usr/local/sbin/cronolog /path/to/access.log.%Y%m%d" log_fmt_name env=!dontlog
{% endhighlight %}

最后让apache重新加载配置文件
{% highlight bash %}
apachectl  graceful
{% endhighlight %}

**graceful and restart**

> apachectl graceful: Gracefully restarts the Apache daemon by sending it a **SIGUSR1**. If the daemon is not running, it is started.    
This differs from a normal restart in that **currently open connections are not aborted**.    
A side effect is that old log files will not be closed immediately. This means that if used in a
log rotation script, a substantial delay may be necessary to ensure that the old log files are closed before processing them.    
This command automatically checks the configuration files via configtest before initiating the restart to make sure Apache doesn't die.

> apachectl restart: Restarts the Apache daemon by sending it a **SIGHUP**.If the
daemon is not running, it is started.     
This command automatically checks the configuration files via configtest before initiating the restart to make sure Apache doesn't die.

再看下日志，清净了很多。

**总结一下**：

apache log rotate 主要有三种工具: rotatelogs/cronolog/logrotate，前两者可以用管道的方式转储，最后一种只支持外部cron方式定时运行。     
当日志过大，超过2G时，如果用cronolog转储日志，要么缩小切分日志的时间间隔，要么改用logrotate。还有一种方法，直接 hack cronolog 的代码：

{% highlight python %}
src/cronolog.c
#将open改为open64，返回的文件标志符的文件大小用off64_t类型，从而支持大于2G的位移, 相当于open加上O_LARGEFILE 的 flag
log_fd = open(pfilename, O_WRONLY|O_CREAT|O_APPEND, FILE_MODE);
{% endhighlight %}
