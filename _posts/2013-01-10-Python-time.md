---
layout: post
title: Python 时间相关
tags: [Python]
---

+   **aware 与 naive**

time 和 datetime 对象分两种：aware 和 naive。   
**aware**
是指有明确的时区信息或夏令时信息，而**naive**没有这些信息，时间究竟属于哪个时区，是本地时还是标准时，是否是夏令时等，都由应用程序来决定。对象是哪种，由其属性tzinfo来确定，如果tzinfo属性不为空且tzinfo.utcoffset不返回None，则该对象是aware的，否则就是naive。

+   **互转问题**

{% highlight python %}

import time
import datetime

date_str = '2013-01-10 11:13:25'

# 从日期字符串到datetime
date_obj = datetime.strptime(date_str, '%Y-%m-%d %H:%M:%S')

# 从datetime到字符串
date_str = date_obj.strftime('%Y-%m-%d %H:%M:%S')

# 从datetime到时间戳,time.mktime将timetuple看作 in localtime
timestamp = time.mktime(date_obj.timetuple())

# 从datetime到时间戳，calendar.timegm将timetuple看作 in utctime
import calendar
timestamp = calendar.timegm(date_obj.timetuple())

# 从时间戳到datetime, 返回的是localtime，但是是naive的。
date_obj = datetime.fromtimestamp(timestamp)

# 从时间戳到datetime，返回的是utctime，也是naive的。
date_obj = datetime.utcfromtimestamp(timestamp)

{% endhighlight %}

datetime中的today()返回naive的localtime，now()没有tz参数时等同于today，utcnow（）返回naive的utctime。

+   **时区问题**

处理日期问题，就用naive就好了，如果需要处理时区的问题，建议用pytz。

{% highlight python %}
import pytz
eastern = pytz.timezone('US/Eastern')
amsterdam = timezone('Europe/Amsterdam')
date_obj = datetime(2013, 01, 10, 11, 13, 25)
# 将naive datetime转化为具体时区的日期
# datetime.datetime(2013, 1, 10, 11, 13, 25, tzinfo=<DstTzInfo 'US/Eastern'  EST-1 day, 19:00:00 STD>)
loc_obj = eastern.localize(date_obj)
# 改变datetime时区
# datetime.datetime(2013, 1, 10, 17, 13, 25, tzinfo=<DstTzInfo  'Europe/Amsterdam' CET+1:00:00 STD>)
mas_obj = loc_obj.astimezone(amsterdam)
{% endhighlight %}


+   **时间间隔**

一般用datetime.timedelta就可以了，但是不支持以月为单位的时间间隔，要用dateutil.relativedelta
{% highlight python %}
from dateutil.relativedelta import relativedelta
from datetime import timedelta
date_obj = datetime(2013, 01, 10, 11, 13, 25)
# 昨天
date_obj + timedelta(days=-1)
# 下个月的当天
date_obj + relativedelta(months=1)
{% endhighlight %}


+   **其他**

得到某个月的天数
{% highlight python %}
from calendar import monthrange
# 返回2013年1月的天数
monthrange(2013, 1)[1]
{% endhighlight %}
