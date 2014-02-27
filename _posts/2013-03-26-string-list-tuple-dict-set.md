---
layout: post
title: Python 中 str, list, tuple, dict, set 的一些技巧
tags: [Python]
---
**int**

+   int padding 显示
{% highlight python %}
x = 4
print '%03d' % x # 004
print"{0:03d}".format(4) # 004
{% endhighlight %}


**str**  

+   str是iterable类型
{% highlight python %}
for i in 'abc':
    print i
{% endhighlight %}

+   支持切片
{% highlight python %}
x = 'abcdefg'
x[1::3] # 'be'
x[::-1] # 倒序字符串
{% endhighlight %}

+   join
连接list/tuple的元素时，用join，而不是循环：

{% highlight python %}
x = ['a', 'b', 'c']
result = ''
# 每次连接都有内存的重新分配，影响效率
for i in x:
    result += i
# join
result = ''.join(x)
{% endhighlight %}

+   string padding
{% highlight python %}
x = '4'
print x.zfill(3)  #'004'
{% endhighlight %}

**list**

+   和str一样，支持切片操作，第三个参数表示步长
{% highlight python %}
x = [1, 2, 3, 4]
x[1::2]  # 2, 4
x[::-1]  # 倒序元素 [4, 3, 2, 1]
x.reverse() # 倒序元素，直接原地改变x的值，而不产生拷贝
{% endhighlight %}

需要注意的是，str和list的切片都返回一个新的对象，而原有的对象不发生改变；同时，list还支持reverse等可以改变自身的方法，而str就没有，因为str是不可变的，而list是可变的。

+   enumerate
在遍历list时，用到index时用到。


**tuple**

+   tuple通过逗号构造，而不是括号
{% highlight python %}
In:  1,
Out: (1,)
In:  1, 2
Out: (1, 2)
{% endhighlight %}
    不过为了直观，还是加上括号较好。创建元素多于一个的元组，尾部的逗号可以省略，但是创建单个元素的元组时，不可被省略。
{% highlight python %}
In:  (1,)
Out: (1,)
In:  (1)
Out: 1
In:  ()
Out: ()
{% endhighlight %}

**dict**

+   创建字典    

    从两个列表中创建:   
{% highlight python %}
x = [1, 2, 3]
y = ['a', 'b', 'c']
z = dict(zip(x, y)) # z={1: 'a', 2: 'b', '3': 'c'}
{% endhighlight %}
+   如果想让类的实例像dict类型一样访问元素（通过[]方式），就需要给类加上 __getitem__和 __setitem__方法:
{% highlight python %}
class MyDict(object):

    def __init__(self, data={}):
        self.data = data

    def __setitem__(self, key, value):
        self.data[key] = value

    def __getitem__(self, key):
        return self.data[key]
{% endhighlight %}

或者，让类直接继承dict也是不错的选择。

+   pop, get, setdefault   
这几个方法都支持一个默认值              
当pop方法传入默认值时，如果key不存在，不会抛出KeyError，而是返回默认值；  
get方法类似，但是当提供的key不存在时，不会抛出异常，而是返回None；    
setdefault(key, value)等价于 get(key, value)，且当key不存在时d[key] = value    

+   深拷贝和浅拷贝  
    深拷贝是值拷贝，浅拷贝是引用拷贝
    {% highlight python %}
    x = {1: [1, 2, 3], 2: 3}
    y = x # x,y指向同一个字典对象，任何对y的操作都会影响x，反之亦然
    z = x.copy() # z是x的浅拷贝，x与z指向不同的对象
    z[1].append(4) # x也受到影响
    z[2] = 5 # x不受影响，因为这相当于z中的引用更改了指向的对象，而x中的引用不受影响
    p = copy.deepcopy(x) # p是x的深拷贝，x与z互不影响
    {% endhighlight %}                
    
                                    
参考:  
    [stackoverflow](http://stackoverflow.com/questions/3975376/understanding-dict-copy-shallow-or-deep?answertab=votes#tab-top)的讨论    
    [shallow and deep copy](http://www.python-course.eu/deep_copy.php)
    
