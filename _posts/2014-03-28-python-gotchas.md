---
layout: post
title: Python中的一些“坑”
tags: [Python]
---

**函数的mutable类型默认参数**

{% highlight python %}
def append_list(element, l=[]):
    l.append(element)
    return l
# l1 == [2]
l1 = append_list(2)
# l2 == [2, 20]
l2 = append_list(20)
{% endhighlight %}

函数的默认参数在函数定义时求值，而不是在调用函数的时候求值

解决方法

{% highlight python %}
def append_list(element, l=None):
    if l is None:
        l = []
    l.append(element)
    return l
{% endhighlight %}

**赋值求值顺序**

    {% highlight python %}
    it = iter([1, 2, 3, 4])
    d = {}
    # d == {2: 1}
    d[it.next()] = it.next()
    # d == {2: 1, 3: 4}
    d.__setitem__(it.next(), it.next())
    {% endhighlight %}

赋值求值顺序是先右边的表达式(expression)，然后是赋值目标(target)；函数调用参数的求值顺序自左而右

**闭包执行时绑定外部变量**

{% highlight python %}
def adds():
    return [lambda x: x + i for i in xrange(5)]

for adder in adds():
    print adder(3)
# output:
# 7
# 7
# 7
# 7
# 7
{% endhighlight %}

上面的闭包相当于

{% highlight python %}
def adds():
    l = []
    for i in xrange(5)；
        def add(x):
            return x + i
        l.append(add)
    return l
{% endhighlight %}

闭包绑定外部变量是在真正执行闭包时，在上面的例子中，循环已经完成，i的值为4

解决方法

+   设置默认值

    {% highlight python %}
    def adds():
        return [lambda x, i=i: x + i for i in xrange(5)]
    {% endhighlight %}

+   partial function

    {% highlight python %}
    from functools import partial
    def adds():
        return [partial(lambda x,i :x + i,  i) for i in range(5)] 
    {% endhighlight %}

+   generator

    {% highlight python %}
    def adds():
        return (lambda x: x + i for i in xrange(5))
    {% endhighlight %}

**generator comprehension 求值时机**

{% highlight python %}
x = ['a', 'b']
y = ['c', 'd']
gen = (a + b for a in x for b in y)
x = ['e', 'f']
y = ['g', 'h']

# ['ag', 'ah', 'bg', 'bh']
print list(gen)
{% endhighlight %}

>Variables used in the generator expression are evaluated lazily when the __next__() method is called for generator object (in the same fashion as normal generators). However, the leftmost for clause is immediately evaluated, so that an error produced by it can be seen before any other possible error in the code that handles the generator expression. Subsequent for clauses cannot be evaluated immediately since they may depend on the previous for loop. For example: (x*y for x in range(10) for y in bar(x)).

**a+=b 和 a = a+b**

在Python中，赋值操作实质是名字和对象的重新绑定

{% highlight python %}
x = [1]
# 名字x绑定的列表对象没有改变, 实际调用了list的 __iadd__方法
x += [2]

# 名字x绑定的列表对象已经改变，实际调用了list的__add__方法
x = x + [2]

y = 1
# 由于y是不可变类型，赋值意味着名字和对象的重新绑定
# 名字y绑定的对象已经改变
y += 2
y = y + 2
{% endhighlight %}

**UnboundLocalError**

{% highlight python %}
var = 'global'
def my_func():
    print var
    var = 'local'
# throw UnboundLocalError!
my_func()

def my_func2():
    # var = var + ' local'
    var += ' local'
# throw UnboundLocalError !
my_func2()
{% endhighlight %}

可见，Python在确定变量引用的对象时，也不是逐行扫描的，而是整块考虑

**参考**

[Surprising Python](http://ballingt.com/2014/03/23/surprising-python.html)

[Python Quiz](http://web.archive.org/web/20101009122154/http://web.mit.edu/rwbarton/www/python.html)

[python docs](http://docs.python.org/2/reference/expressions.html#generator-expressions)

[Facts and myths about Python names and values](http://nedbatchelder.com/text/names.html)
