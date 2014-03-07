---
layout: post
title: iterator 与 generator
tags: [Python]
---
**容器和iterator**

在Python中，可以对容器类(container,如list,tuple,set,dict等)进行遍历(iterate)，这是因为这类容器实现了\_\_iter\_\_方法，该方法返回了一个iterator类型(iterator type)的对象。以字典类型为例:

{% highlight python %}
x = {}
iter_over_x = x.__iter__() # 或者是调用iter(x)
type(iter_over_x)  # dictionary-keyiterator,in python2.7
{% endhighlight %}

生成iterator type对象的方法有三种: 

1.  构造实现iterator protocol的类并实例化
2.  调用generator expression
3.  调用generator函数


**iterator protocol**

看一个对象是不是iterator type,要看该对象是否实现了以下两个方法，也就是通常所说的是否实现了 iterator protocol

+   \_\_iter\_\_   
该方法通常返回自身，容器类也实现了这个方法，所以无论是容器类还是iterator类，都可以出现在 for item in object 语句中。

+   next   
在python3中，被改名为\_\_next\_\_方法，调用该方法返回遍历的下一个元素，如果当前元素已经是最后一个元素，则抛出 StopIteration 异常。

一个iterator只能被遍历一次，而且遍历过程中不能回退到已经遍历的节点，而一个容器每次调用\_\_iter\_\_都会生成一个全新的iterator。

一个容器类也可以是一个iterator类型，这就要求其\_\_iter\_\_方法返回自身，而且实现next方法。如文件对象就是如此：
{% highlight python %}
f = open('somefile')
iter_over_f = f.__iter__()
print f is iter_over_f  # True
{% endhighlight %}

**for循环** 
for 循环对用户封装了iterator 对象的生成和遍历，如
{% highlight python %}
for item in container:
    # process item
{% endhighlight %}
相比
{% highlight python %}
_iter = iter(container)
while True:
    try:
        item = _iter.next()
    except StopIteration:
        break
    # process item
{% endhighlight %}
明显简洁了很多。

**iter的一个技巧**

除了 iter(collection)这种方式外，iter还可以 iter(callable, sentinel)这么调用，直到callable返回sentinel时，停止对callable的调用
    
{% highlight python %}
# iter(callable, sentinel),
# the callable is called until it returns the sentinel
CHUNKSIZE = 8192


def reader(s):
    while True:
        data = s.recv(CHUNKSIZE)
        if data == b'':
            break
        #process_data(data)


def reader2(s):
    for chunk in iter(lambda: s.recv(CHUNKSIZE), b''):
        pass
        #process_data(chunk)
{% endhighlight %}

**generator expression**  

generator expression 返回的结果是generator对象，该对象实现了iterator protocol。

**generator**

generator
function是指有yield语句的函数，调用该函数，不会执行该函数，返回的结果也不是某一个单一的值，而是一个generator对象，该对象实现了iterator protocol，当遍历该generator对象时，函数体内的逻辑才开始真正执行。
{% highlight python %}
def x():
    yield 1
_gen = x()
type(_gen) # generator
{% endhighlight %}
