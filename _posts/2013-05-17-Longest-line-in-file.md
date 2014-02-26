---
layout: post
title: 找出文件中最长的行
tags: [Linux Command]
---

如何找出文件中最长的行？我写了下面的awk脚本，打印出最长行的行号，内容以及行的长度:
{% highlight awk%}
#!/bin/awk -f
BEGIN {
    max_len = 0
    max_line = ""
    max_nu = 0
}
{
    cur_len = length
    if(cur_len>max_len) {
        max_len = cur_len
        max_line = $0
        max_nu = NR
    }
}
END{
    print max_nu, max_line, max_len
}
{% endhighlight %}

在网上看到，还有人这么写
{% highlight bash %}
awk '{print length,NR,$0}' a.txt | sort -nr | head -1  
{% endhighlight %}

第二种写法的好处是更简洁，而且可以根据head的参数来调整显示的行数，但是sort处多出排序的过程，处理起来更费时，应用时看情况而定。

另外,wc也可以显示最长行的长度，但是没法显示内容，可以结合其他工具来用
{% highlight bash %}
wc -L a.txt
{% endhighlight %}

如果在vim里面编辑文件，通常需要找出超过某长度的行，较好的方法是：在vim里面高亮长行。
{% highlight vim %}
# 高亮每行超过80个字符的部分, :help /\%
/\%>80v
{% endhighlight %}
