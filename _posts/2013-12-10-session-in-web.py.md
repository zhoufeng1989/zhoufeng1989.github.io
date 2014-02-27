---
layout: post
title: Webpy 之 session 解析
tags: [Python]
---

和session相关的类主要有Session类和Store类，Session类进行session管理，Store类实现session的存储,可以选择磁盘存储(DiskStore)、数据库存储(DBStore)等。

#Session类

先看__init__方法:  
{% highlight python %}
def __init__(self, app, store, initializer=None):
    ...
    ...
    if app:
        app.add_processor(self._processor)
{% endhighlight %}

其中,store为类Store某个子类的实例对象,确定了session的存储方式，app为应用对象,如果存在，
将session的处理逻辑加在应用处理请求链中，这样处理请求时会执行self._processor的逻辑，
Session._processor方法即为核心的session逻辑。

##_processor的处理逻辑：   

**在每个请求处理前**:

+   清理(cleanup) sessions:
    session初始化时会设置一个timeout参数,周期性对session进行清理,如果session过期则删除session。     

    {% highlight python %}
    # 距上次清理时间大于timeout则进行清理
    if current_time - self._last_cleanup_time > timeout:
            # 如果session超时,则在存储中删除session
            # 注意是会清理所有过期的session数据，而不只是session_id对应的session数据
            self.store.cleanup(timeout)
            self._last_cleanup_time = current_time
    {% endhighlight %}

+   加载session
    从cookie中获得session_id,判断session_id是否合法,检查session是否过期（过期的session都已经在存储设备上被删除）
    有三种情况:     

    1.  如果session不合法,重新初始化session    
    2.  如果session过期,根据配置抛出异常或重新初始化session    
    3.  如果session有效，加载session     

            {% highlight python %}
            d = self.store[self.session_id]
            self.update(d)
            {% endhighlight %}       

            注意，由于session 的 __getattr__方法定义如下：     

            {% highlight python %}
            def __getattr__(self, name):
                return getattr(self._data, name)
            {% endhighlight %}

            而self._data是dict的子类实例，这样，self.update其实是调用了self._data.update方法，这样，self._data里面就保存了session数据。
    另外，session类还重写了__setattr__方法：    

            {% highlight python %}
            def __setattr__(self, name, value):
                if name in self.__slots__:
                    object.__setattr__(self, name, value)
                else:
                    setattr(self._data, name, value)

            __slots__ = [
                "store", "_initializer", "_last_cleanup_time", "_config", "_data", 
                "__getitem__", "__setitem__", "__delitem__"
            ]
            {% endhighlight %}     

            这样，对__slots__成员以外的属性进行修改，就相当于对_data(session 数据）的修改了。


**处理请求,对session对象进行读写**

**处理完请求**     

如果session仍有效:      

    1.  写cookie    
    self._setcookie(self.session_id)
    2.  保存session
    self.store[self.session_id] = dict(self._data)
    以磁盘存储（DiskStore）为例，__setitem__如下: 

    {% highlight python %}
    def __setitem__(self, key, value):
        path = self._get_path(key)
        pickled = self.encode(value)    
        try:
            f = open(path, 'w')
            try:
                f.write(pickled)
            finally: 
                f.close()
        except IOError:
            pass
    {% endhighlight %}
    字典式的赋值实质是将数据保存到了磁盘上
    另外 __getitem__定义如下：
    {% highlight python %}
    def __getitem__(self, key):
        path = self._get_path(key)
        if os.path.exists(path): 
            pickled = open(path).read()
            return self.decode(pickled)
        else:
            raise KeyError, key
    {% endhighlight %}
    所以store[session_id]实际上是在相应的存储设备中获取了session信息。


如果session无效,则将cookie删除:
{% highlight python %}
self._setcookie(self.session_id, expires=-1)
{% endhighlight %}


#Store类及其子类  
实现了对session_id为key的字典式的存储，如:
{% highlight python %}
store[session_id] = data
del store[session_id]
{% endhighlight %}
具体存储逻辑在 \_\_getitem\_\_/\_\_setitem\_\_/\_\_delitem\_\_方法中,不同的存储方式暴露出的存取接口都一样，这样改变存储方式时不用更改接口方法，不会影响到session数据的迁移。
