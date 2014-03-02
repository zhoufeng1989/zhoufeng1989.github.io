---
layout: post
title: What every web developer must know about URL encoding(翻译)
tags: [Linux Shell]
---

**General URL syntax**:    
以下面的URL为例，来分析URL的构成: 
{% raw %}
https://bob:bobby@www.lunatech.com:8080/file;p=1?q=2#third

|   Part            |   Data            
|:------------------|:------------------
|   Scheme          |   https          
|   User            |   bob          
|   Password        |   bobby
|   Host            |   www.lunatech.com
|   Port            |   8080
|   Path            |   /file
|   Path parameters |   p=1
|   Query parameters|   q=2
|   Fragment        |   third
{% endraw %}

对于Path来说，每一层path都有可选的path parameters(也叫做matrix parameters)，在该层path后，由分号(;)分隔。
对于Fragment来说，它标记网页的一个部分，当点击此链接时，该部分将位于浏览器顶部展示。

**URL encoding**

可以看到，在URL中有很多特殊字符，如"://"分隔scheme和host部分，"?"分隔path和query parameters部分，这些字符都有特殊的含义，所以，当URL中出现上述字符作为字面值时，就要被编码。编码的方式是百分号(%)加上该字符的16进制ASCII编码，如问号(?)被编码为(%3F)。

很多浏览器在地址栏显示URL时，都会将其解码显示，向服务器发请求时用已编码的URL。这样一来，编码对用户是透明的了。

**URL encoding 中的坑**

对于不能用ASCII编码表示的字符，URL编码没有规定用哪种编码方式进行编码
最新的URI规范中已经规定了scheme/host使用UTF-8进行编码。


URL每个部分的保留字不同！  
在fragment部分，空格被编码成"%20"，而不是加号(+），这样加号就无需被编码；然而，在query parameters部分，空格被编码成加号(为了向后兼容)或"%20",这样加号就不得不被编码（"%2B"),所以，很容易出现下面的错误:
{% highlight java %}
String str = "blue+light blue";
String url = "http://example.com/" + str + "?" + str;
{% endhighlight %}
可见，URL编码并不是简单的遍历URL的每个字符去编码，还要搞清楚每个部分的保留字是哪些。   
除了加号，还有另外一些字符:   
"?/": 在query parameters中，问号(?)和斜线(/)不需要编码;
"=" 在path/path parameter/query parameter中不需要编码;
":@-._~!$&'()*+,;=" 在path中不需要编码;
"/?:@-._~!$&'()*+,;=" 在fragment中不需要编码;

**A URL cannot be analysed after decoding**   
对URL的解析应该在编码之前，编码之后解析没有意义。


# 参考  

+   [原文](http://blog.lunatech.com/2009/02/03/what-every-web-developer-must-know-about-url-encoding)
