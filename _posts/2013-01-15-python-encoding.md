---
layout: post
title: Python2 编码问题总结
tags: [Python]
---

**基础知识**

在ASCII编码中，字符被映射成0-127的二进制串，ASCII编码是单字节编码（每个字符占一个字节），无法表示多有的字符；

UNICODE并不是一种特定的编码，而是UNICODE字符集，在这个字符集中，字符被映射成码位（code point），

而码位有多种编码方式，如UTF-8（和ASCII编码兼容），UTF-16等（一般是多字节编码，每个字符占多个字节），在不同的编码方式下，统一码值被编码成不同的二进制串。

在操作系统层面，所有编码的字符串不过是二进制串而已，而编码方式，正是将二进制串转为有意义的字符的依据。

**源代码的编码声明**  

如果源代码的第一行或第二行中的注释能够匹配正则表达式
coding[=:]\s*([-\w.]+)，那么这行注释被看作是源代码的编码声明（encoding
declaration），Python编译器会依此处理源代码:
{% highlight python %}
# in emacs
# -*- coding: <encoding-name> -*-
# in vim
# vim:fileencoding=<encoding-name>
{% endhighlight %}

如果没有声明，默认会将源代码看作是ASCII编码的，如果出现非ASCII编码的字符（如汉字）将会报语法错误（SyntaxError）。

**str 与 unicode**

Python中字符串分为str和unicode两种，str类型的字符串是特定编码（如ASCII,UTF-8,UTF-16）的字符串，而unicode类型的字符串存储的是unicode的码值，是一种中间形式，并没有特定的编码信息。

str解码成unicode:  
str.decode(encoding) => unicode            
unicode编码为特定编码的str:       
unicode.encode(encoding) => str   
如果参数encoding省略，将默认使用sys.getdefaultencoding()返回的编码。

**文件处理**

+   用open打开文件时，读入的文件内容的编码是文件本身的编码，写入文件时，必须将内容转换为具体的编码方能写入；             

    用codecs.open打开文件时，需要指定文件的编码，读入文件内容时，文件内容由指定的编码转化为unicode的码值表示，写入文件时，再将unicode码值转为具体的编码写入。


+   open/codecs.open/os.walk/os.listdir等都可以接收unicode码值表示的文件/目录名，这时，根据sys.getfilesystemencoding()返回的编码方式将文件名进行编码，然后在文件系统里查找相应的文件/目录。   

    当os.walk，os.listdir等方法接收的参数是unicode的码值时，返回的结果也是unicode码值；如果接受的参数是具体的编码时，返回的结果也以具体的编码表示。

**print unicode**:

当sys.stdout.encoding 不为None时，以此编码为依据编码unicode然后输出；如果为None时，则以sys.getdefaultencoding()(ASICC)方式将unicode编码输出；

sys.stodut.encoding编码的选择与shell环境相关，当标准输出为终端时，一般是环境变量$LC_CTYPE，当标准输出为文件时，为None。

当设置环境变量PYTHONENCODING时，stdin/stdout/stderr的encoding由该环境变量决定。

Shell环境的编码与终端的编码是两回事，前者是sys.stdout.encoding取值的依据，后者只在显示结果的时候起作用。


**参考**

[The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](http://www.joelonsoftware.com/articles/Unicode.html)

[关于print sys.stdout.encoding 最让我信服的解释](http://stackoverflow.com/a/21968640/1392872)

