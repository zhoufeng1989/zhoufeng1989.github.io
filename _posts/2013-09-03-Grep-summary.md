---
layout: post
title: Grep Summary
tags: [Linux Command]
---
基本用法是:    
**grep [options] PATTERN [FILES]**  

**options**  
常用的*options*有:  

+   -i  匹配时不区别大小写   
+   -v  不匹配模式
+   -n  显示行数
+   -r  递归查找
+   -l  只显示匹配的字符串所在的文件名,对于查看哪个文件有匹配模式的字符串时有用


上下文相关:    

+   -A n after, 显示匹配行以及后面的n行
+   -B n before, 显示匹配行以及之前的n行
+   -C n 显示匹配行以及前后n行，等价于 -A n -B n


**PATTERN**   
*PATTERN*是指定的正则表达式,为了避免正则表达式被shell展开(如 `*`, `[`, `]`等),比较好的做法是给正则表达式加上引号。  如:

{% highlight bash %}
grep '*cat*' a.txt
{% endhighlight %}


grep的正则表达式属于POSIX规范的[BRE](http://www.regular-expressions.info/posix.html)流派,字符组(`[]`)、锚点(`^`(匹配行首)、`$`(匹配行尾)、`\<`、`\>`(匹配单词的起始和结束位置))都无需转义，但是量词中，除了`*`以外, 其他量词(`+`, `?`, `{n, m}`)都需要转义才能使用。

{% highlight bash %}
# 匹配文件a.txt中含有单个数字的行，使用POSIX字符组   
grep '[[:digit:]]' a.txt
# 匹配文件a.txt中能够匹配单词hello的行
grep '\<hello\>' a.txt
# 匹配文件a.txt中含有多个连续字母a的行
grep 'a\{2,\} a.txt
{% endhighlight %}

grep支持多选结构，但是必须转义`(`、`|`、`)`,捕获分组的记法是 `\1`,`\2`。


**如何匹配tab字符**   
*POSIX regular expression*没有将*tab*映射为*\t*,*grep*中如何搜索*tab*呢?   

+  使用*perl*风格的正则表达式    
    
    {% highlight bash %}
    grep -P '\t' a.txt
    {% endhighlight %}

+  使用`echo`直接输出的tab     

    {% highlight bash %}
    grep "$(echo -ne \\t)" a.txt
    {% endhighlight %}


+  CTRL+V tab: CTRL+V 使接下来的输入的字符失去特殊意义
+  如果用的是*Bash shell*, 还可以用[ansi c quoting](http://www.gnu.org/software/bash/manual/html_node/ANSI_002dC-Quoting.html#ANSI_002dC-Quoting)这个特性

    {% highlight bash %}
    grep $'\t' a.txt
    {% endhighlight %}
