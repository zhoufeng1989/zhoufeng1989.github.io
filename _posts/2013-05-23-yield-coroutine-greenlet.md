---
layout: post
title: Python 中 yield generator coroutine greenlet等梳理
tags: [Python]
---
yield表达式只能出现在函数中，当函数中含有yield表达式时，该函数被称作generator function，调用该函数并不执行该函数内的逻辑，而是返回generator对象。

generator对象支持的操作有:

+   next

开始一个未执行的generator函数，或恢复执行一个在yield处挂起的generator函数

+   send(value)

类似与next，但是可以传值value给generator函数，作为当前挂起的yield表达式的返回值，next等同于send(None)

+   throw(type[, value[, traceback]])

在generator的挂起处抛出type类型的异常

+   close()

在generator挂起处抛出GeneratorExit异常

generator只有在yield处才能挂起，将程序控制转给调用者；而调用者通过next/send也只能和在yield处挂起的generator进行交互。这就意味着控制权只能在generator和调用者之间轮换，而且是在yield处进行轮换，如果多个generator之间交换数据，那么只能通过主调者进行中转。

可以跟据yield是否返回值来讲generator分为两种:

+   不考虑yield返回值的generator，这种generator主要用来生成数据，供主调者使用;
+   考虑yield返回值的generator，可以看作是协程，可以接收主调者的数据进行处理，但这种协程如上所说，有很大的局限性。

一个协程的例子

{% highlight python %}
def grep(pattern):
    print "Looking for %s" % pattern
    while True:
        line = (yield)
        if pattern in line:
            print line,

# Example use
if __name__ == '__main__':
    g = grep("python")
    #must use next() or send(None) to prime it
    g.next() 
    #after prime,it's ready to receive a value
    g.send("Yeah, but no, but yeah, but no")
    g.send("A series of tubes")
    g.send("python generators rock!")

{% endhighlight %}

generator协程和greenlet的比较:

greenlet可以通过swith方法在同一线程的协程间自由切换，而generator只能在yield处进行切换，这就意味着如果有多个generator时，需要有主调者充当中转的角色。

**参考**  
[greenlet vs generator](http://stackoverflow.com/questions/11830540/is-yield-based-coroutine-is-real-coroutine/11830678#11830678)   
[coroutines](http://www.dabeaz.com/coroutines/Coroutines.pdf)   
[PEP 342 -- Coroutines via Enhanced Generators](http://legacy.python.org/dev/peps/pep-0342/)   
[generator document](http://docs.python.org/2.7/reference/expressions.html#generator-iterator-methods)     
[Generators Are Not Coroutines](http://c2.com/cgi/wiki?GeneratorsAreNotCoroutines)   
[the difference between generators, coroutines and continuations.](https://mail.python.org/pipermail/python-dev/1999-July/000467.html)   


