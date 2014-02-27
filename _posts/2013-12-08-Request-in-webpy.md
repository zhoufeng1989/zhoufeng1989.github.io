---
layout: post
title: Webpy 之 请求处理流程以及debug模式 解析
tags: [Python]
---
当请求（request）到来时，webpy对请求的处理流程在Application类的handle_with_processors方法中：
{% highlight python %}
def handle_with_processors(self):
    def process(processors):
        try:
            if processors:
                p, processors = processors[0], processors[1:]
                return p(lambda: process(processors))
            else:
                return self.handle()
        except web.HTTPError:
            raise
        except (KeyboardInterrupt, SystemExit):
            raise
        except:
            print >> web.debug, traceback.format_exc()
            raise self.internalerror()
    
    # processors must be applied in the resvere order. (??)
    return process(self.processors)
{% endhighlight %}

可以看到，在处理请求时，会链式执行注册在processors列表中方法，如session的_processor方法，最后处理的是handle方法:
{% highlight python %}
def handle(self):
    fn, args = self._match(self.mapping, web.ctx.path)
    return self._delegate(fn, self.fvars, args)
{% endhighlight %}
根据url来确定请求的处理方法并调用（路由）。     
如果在创建application时设置autoreload=True时，修改程序自动重新加载，不需要重新启动应用，常用于开发时调试程序。
{% highlight python %}
self.add_processor(loadhook(Reloader()))
{% endhighlight %}
将Reloader()加入调用链中。    
另外，还将重新加载url路由表的逻辑加入调用链
{% highlight python %}
self.add_processor(loadhook(reload_mapping))
{% endhighlight %}
当(url,handler)被修改时,application.mapping（请求路由表）能够跟着改变。

Reloader类的代码如下：
{% highlight python %}
class Reloader:
    """Checks to see if any loaded modules have changed on disk and, 
    if so, reloads them.
    """

    """File suffix of compiled modules."""
    if sys.platform.startswith('java'):
        SUFFIX = '$py.class'
    else:
        SUFFIX = '.pyc'
    
    def __init__(self):
        self.mtimes = {}

    def __call__(self):
        #sys.modules.values()返回所有module的元组
        for mod in sys.modules.values():
            self.check(mod)

    def check(self, mod):
        # jython registers java packages as modules but they either
        # don't have a __file__ attribute or its value is None
        if not (mod and hasattr(mod, '__file__') and mod.__file__):
            return

        try: 
            #mod.__file__记录该module的文件(.pyc)路径
            mtime = os.stat(mod.__file__).st_mtime
        except (OSError, IOError):
            return
        #检查该module对应的py源文件的修改时间,取py/pyc修改时间的较大值
        if mod.__file__.endswith(self.__class__.SUFFIX) and os.path.exists(mod.__file__[:-1]):
            mtime = max(os.stat(mod.__file__[:-1]).st_mtime, mtime)
            
        if mod not in self.mtimes:
            self.mtimes[mod] = mtime
        elif self.mtimes[mod] < mtime:
            try: 
                reload(mod)
                self.mtimes[mod] = mtime
            except ImportError: 
                pass
{% endhighlight %}

在request的调用链中__call__方法被调用,检查import过的每个module是否在上一次check时修改过,如果修改过,就reload这个module,并记录这次该模块的修改时间。
