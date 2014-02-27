---
layout: post
title: Python 的内置函数 max, min, sorted 中的参数 key
tags: [Python]
---

Python 的内置函数max, min, sorted
都支持参数key，要求参数key是一个函数，该只接收一个参数（也就是该内置函数要处理的元素），key函数返回的结果作为比较大小或排序的依据。

注意，一定不要忘记了key参数是keyword argument，这三个函数都接收多个position arguments。  
{% highlight python %}
x = [-1, -2, -3]
max(x, key=abs) # 绝对值最大的元素，-3
min(x, key=abs) # 绝对值最小的元素，-1

# 对于接收多个参数的函数，可以用lambda表达式封装
import math
max(x, key=lambda item: math.pow(2, item)) 

# 也可以用functools.partial 封装
import functools
f = functools.partial(math.pow, 2)
max(x, key=f)

sorted(x, abs) # 按照元素的绝对值排序，[-1, -2, -3]
{% endhighlight %}

通过对key的设定，可以灵活的进行找最大/小或排序。



