---
layout: post
title: Vimdiff tips
tags: [Linux Command]
---

**Keyboard Shortcuts**:

do - Get changes from other window into the current window.

dp - Put the changes from current window into the other window.

]c - Jump to the next change.

[c - Jump to the previous change.


**:diffu[pdate]**:

recalculate the diff, useful when after making several changes vim's isn't showing minimal changes anymore.

**compare to remote file**

vimdiff /path/to/file scp://user@host:port//path/to/file


**将svn的diff命令改为vimdiff**

首先，将下面的脚本保存，假设命名为 /path/to/svndiff
{% highlight bash %}
#!/bin/sh
# 配置vimdiff程序的路径
DIFF="vimdiff"
# SVN diff命令会传入两个文件的参数 
LEFT=${6}
RIGHT=${7}
# 拼接成diff命令所需要的命令格式
$DIFF $LEFT $RIGH
{% endhighlight %}

在.subversion/config里：
{% highlight bash %}
diff-cmd = /path/to/svndiff
{% endhighlight %}
