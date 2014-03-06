---
layout: post
title: Python内置数据类型相关
tags: [Python]
---
**数字类型**

较常用的数字类型有整数（int），长整型（long），浮点类型（float）

+   int 和 long   

int由c语言中的long实现，至少32位，具体范围由机器决定；long的取值没有限制。

+   float

float 相当于c语言里面的double类型，当进行财务方面的计算时，应该用decimal

{% highlight python %}
a = 4.2
b = 2.1
a + b  # 6.300000000000001
# These errors are a “feature” of the underlying CPU and the IEEE 754
# arithmetic performed by its floating-point unit.
# Since Python’s float data type stores data using the native representation,
# there’s nothing you can do to avoid such errors if you write your code  using float instances.
from decimal import Decimal
a = Decimal('4.2')
b = Decimal('2.1')
a + b  # 6.3
{% endhighlight %}

**sequence类型**

sequence类型包括 str,unicode,list,tuple等

sequence类型支持切片操作
{% highlight python %}
x = 'abcdefg'
x[1::3] # 'be'
x[::-1] # 倒序字符串

x = [1, 2, 3, 4]
x[1::2]  # 2, 4
x[::-1]  
x.reverse() # 倒序元素，直接原地改变x的值，而不产生拷贝

# 可以命名一个切片，程序可读性更好
slice_name = slice(2, 3)
l1 = [1, 2, 3, 4]
l1[slice_name]  # l1[2:3]

a = slice(5, 10, 2)
s = 'HelloWorld'  # a.indices return a tuple (start, stop, step)
for i in xrange(*a.indices(len(s))):
    print s[i]      # W r d
# 对列表的slice赋值，会更改原列表
x = [1, 2, 3, 4]
x[1:2] = [10, 11, 12]
print x  # x=[1,10,11,12,3,4]
{% endhighlight %}

需要注意的是，str和list的切片都返回一个新的对象，而原有的对象不发生改变；同时，list还支持reverse等可以改变自身的方法，而str就没有，因为str是不可变的，而list是可变的。

可以将sequence解包(unpack)到多个变量中，其实解包操作适用于所有可遍历对象(files,strings,iterators,generators)。
{% highlight python %}
# unpack from str
_, x, y, _ = 'abcd'  # x=='b', y=='c'

# unpack from list and tuple
_, x, y, (a, b, c) = [1, 2, 3, (4, 5, 6)]  # x==2,y==3,a=4,b=5,c=6

# unpack from generators
def gen_func():
    yield 1
    yield 2
    yield 3
x, y, _ = gen_func() # x=1, y=2
{% endhighlight %}

+   str

    连接（join）

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


    string padding            

                {% highlight python %}
                x = '4'
                print x.zfill(3)  #'004'
                {% endhighlight %}

    startswith/endswith    

    第一个参数(prefix/suffix)接受元组作为参数，如果满足prefix/suffix是元组中的任一元素，就返回True
    第二个参数（start）和第三个参数（end）可选，用来确定测试的位置

{% highlight python %}
'abc'.startswith(('a', 'b')) # True
'abcdef'.statswith('c', 2) # True
{% endhighlight %}

    maketrans/translate     

maketrans创建一个字符翻译表(映射表),translate根据翻译表翻译字符串。      
translate(table,[delete_chars]) 会先删除原字符串中包含的[delete_chars]，然后再根据table进行翻译，当table为None时,只做删除操作.
{% highlight python %}
import string
table = string.maketrans(
    string.ascii_lowercase, string.ascii_lowercase[2:] + string.ascii_lowercase[:2])
# 源字符串去掉'xy'后，进行映射
string.translate('abcxyz', table, 'xy') # 'cdeb'
{% endhighlight %}


+   list

enumerate

在遍历list时，用到index时用到。


+   tuple

tuple通过逗号构造，而不是括号   
{% highlight python %}
In:  1,
Out: (1,)
In:  1, 2
Out: (1, 2)
{% endhighlight %}
不过为了直观，还是加上括号较好。

创建元素多于一个的元组，尾部的逗号可以省略，但是创建单个元素的元组时，不可被省略。
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

+   字典推导(dict comprehension)

    {% highlight python %}
    x = [1, 2, 3, 4]
    d = {key: key for key in x if key % 2}
    {% endhighlight %}

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

**set**

+   set comprehension(set 推导) 
    {% highlight python %}
    x = [1, 2, 3, 1]
    # s=set([1, 2, 3])
    s = {key for key in x}
    {% endhighlight %}
                                    
+   list去重
    {% highlight python %}
    x = [1, 2, 3, 3]
    # s=set([1, 2, 3])
    s = set(x)
    {% endhighlight %}

    
