---
layout: post
title: Python扩展数据类型
tags: [Python]
---
+   collections.deque, used for store latest limited items

de means double ended   
https://wiki.python.org/moin/TimeComplexity
{% highlight python %}
from collections import deque
queue = deque(maxlen=3)
queue.append(1)
queue.append(2)
queue.append(3)  # queue is deque([1,2,3])
queue.append(4)  # queue is deque([2,3,4])
{% endhighlight %}

+   find largest or smallest N items

find the largest or smallest N (N<<n) items,use heap sort to get result

use max() or min() if only the largest or smallest

use sort and slice if N is almost n (n is total)

{% highlight python %}
import heapq
numbers = [i for i in xrange(10)]
heapq.nlargest(2, numbers)  # [8, 9]
heapq.nsmallest(2, numbers)  # [0, 1]
# key parameter for sort
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
cheap = heapq.nsmallest(3, portfolio, key=lambda s: s['price'])
expensive = heapq.nlargest(3, portfolio, key=lambda s: s['price'])
{% endhighlight %}

+   collections.defaultdict

call defaultdict(default_factory) to init a new defaultdict,the default factory is called without arguments to produce a new value when a key is not present, in __getitem__ only.

+   collections.OrderDict

preserves the original insertion order of data when iterating

+   eliminate dumplicates in list , use set ,but order is not guarntee


+   determing the most frequent items in sequence

{% highlight python %}
from collections import Counter
words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around', 'the',
    'eyes', "don't", 'look', 'around', 'the', 'eyes', 'look', 'into',
    'my', 'eyes', "you're", 'under'
]
# words_counter is a dict like object, words_counter['eyes']==8
words_counter = Counter(words)
# most_common return a list contain tuples of words and its apperance times
top_three = words_counter.most_common(3)
morewords = ['why', 'are', 'you', 'not', 'looking', 'in', 'my', 'eyes']
# update counter with new sequence
words_counter.update(morewords)
words_counter2 = Counter(morewords)
# substract
words_counter - words_counter2
# addtion
words_counter + words_counter2
{% endhighlight %}
