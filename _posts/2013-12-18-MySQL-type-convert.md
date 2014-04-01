---
layout: post
title: MySQL字符串与整数比较的隐式转换
tags: [MySQL]
---
以下在MySQL5.0版本适用，其他版本没有验证。

当在SQL中比较字符串和整数的时候，会将两方都转换为float进行比较，会产生意想不到的结果:

{% highlight sql %}
mysql> select 'CBA123' = 0;
+-----------------------+
| 'CBA123' = 0 |
+-----------------------+
| 1 | 
+-----------------------+
1 row in set, 1 warning (0.00 sec)
{% endhighlight %}
等号两边都被转换成0.0

{% highlight sql %}
mysql> select 'CBA123' = '0';
+-------------------------+
| 'CBA123' = '0' |
+-------------------------+
| 0 | 
+-------------------------+
1 row in set (0.00 sec)
{% endhighlight %}

字符串之间的比较不需要类型转换

{% highlight sql %}
mysql> select '123CBA' = 123;
+----------------+
| '123CBA' = 123 |
+----------------+
|              1 | 
+----------------+
1 row in set, 1 warning (0.00 sec)
{% endhighlight %}
字符串 '123CBA'被转换成123.0
看一下警告信息:
{% highlight sql %}
mysql> show warnings;
+---------+------+--------------------------------------------+
| Level   | Code | Message                                    |
+---------+------+--------------------------------------------+
| Warning | 1292 | Truncated incorrect DOUBLE value: '123CBA' | 
+---------+------+--------------------------------------------+
1 row in set (0.00 sec)
{% endhighlight %}

所以在以字符串类型列为筛选条件的时候，还是不要和整数进行比较了。
