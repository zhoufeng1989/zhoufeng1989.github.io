---
layout: post
title: Python包/模块管理tips
tags: [Python]
---

**import**

有以下几种import方式:

+   **import X**:
导入module X，并在当前的名字空间创建引用指向该module对象。

+   **from X import **
导入module X，module X 中除了名称以下划线开头的对象和没在\_\_all\_\_列表中出现的对象外，module中的所有其它对象在当前名字空间中创建引用，指向该对象。

+   **from X import a, b, c**
导入module X，并在当前命名空间中创建导入对象的引用。

+   **\_\_import\_\_**

import 过程:

1.  在sys.module列表中查看module是否已被import，如果已经import，则使用现在存在的引用;reload会重新import 模块；
2.  创建module对象；
3.  将新创建的对象加入sys.module列表中；
4.  编译module文件，加载(load)对象；
5.  在module的命名空间下执行module文件。

循环import

{% highlight python %}
# X.py
import Y   #(1)
def spam(): #(2)
    print 'function in module X'
{% endhighlight %}

{% highlight python %}
# Y.py 
from X import spam #(3)
{% endhighlight %}

执行import X：当执行到(1)时，转而去执行Y.py，当执行到(3)时，此时X虽然在sys.module中注册，但是还没有属性spam，报错。
执行import Y：当执行到(3)时，转而执行X.py，执行(2)时，因为Y已经存在，无需重新import，顺利执行成功。

当以脚本的方式运行X.py/Y.py时，因为以脚本执行时，module名字是\_\_main\_\_，而不是X/Y，结果Y报错，X执行成功。

当出现循环import时，尽量把import语句写在后面，如X.py将import Y放在最后，就不会出错了。
**package**

package是一个目录，里面有一个\_\_init\_\_.py文件，当导入package时，\_\_init\_\_.py在package的名字空间下被执行。

