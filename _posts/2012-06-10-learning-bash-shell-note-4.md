---
layout: post
title: 《learning the Bash shell》笔记4 变量
tags: [Bash]
---

**变量引用和赋值**

变量引用: $varname  
变量赋值: varname=value


**特殊变量**

0  脚本名称或shell名称   
1, 2..    脚本的参数  
#  脚本参数个数，从$1开始算起   
$  当前shell的pid  
?  上一条shell的退出状态

$\*与$@的不同:   
都被展开为位置参数($1,$2...), 仅在""(双引号)中表现不同:
"$\*"是单一字符串 ，其中的参数用$IFS的第一个字符分隔;而"$@"是位置参数的数组

例子如下:

{% highlight bash %}
#parameter.sh
#!/bin/sh
echo there are $# parameter!
IFS=,
echo '$*' is $*        # arg1 arg2 arg3
echo '"$*"' is "$*"    # arg1,arg2,arg3
echo '$@' is $@    #arg1 arg2 arg3
echo '"$@"' is "$@"    #arg1 arg2 arg3

{% endhighlight %}

执行 sh parameter.sh arg1 arg2 arg3的结果为:  

{% highlight bash %}
there are 3 parameter!
$* is arg1 arg2 arg3
"$*" is arg1,arg2,arg3
$@ is arg1 arg2 arg3
"$@" is arg1 arg2 arg3
{% endhighlight %}


**变量作用域**

默认是全局的，在函数中定义的变量在函数外可见  
在函数中,$0仍为程序文件名,而$1、$2等是函数自身的参数   
在函数中定义局部变量需用关键字 local   

**${}与字符串操作**

第10个位置参数应该写为${10}，而非$10。   

如果引用变量varname时，变量后面是字母、数字、下划线时，需要写做${varname}，以示区分。

字符串操作有以下几种:

+   测试变量
<figure>
<a href="/images/test-variable.jpg"><img src="/images/test-variable.jpg"></a>
</figure>

+   模式匹配
<figure>
<a href="/images/pattern-matched.jpg"><img src="/images/pattern-matched.jpg"></a>
</figure>

模式匹配主要用于处理路径上面
{% highlight bash %}
path=/home/a/b/c/d.txt
# basename
echo ${path##*/}
# dirname
echo ${path%/*}
{% endhighlight %}

将$PATH变量分行输出各路径:
{% highlight bash %}
echo -e ${PATH//:/'\n'}
{% endhighlight %}

**变量类型**

declare命令

默认显示所有已定义的变量

常用选项
<figure>
<a href="/images/declare.jpg"><img src="/images/declare.jpg"></a>
</figure>

**算数操作**

$(( ))里面的表达式被shell当做算术运算，里面的变量在引用时无需加$符号。

let:整数赋值，无需$(())封装
{% highlight bash %}
let x=1+5
echo $x   # 6
let x='1+6'
echo $x   #7
let x="1+7"
echo $x  #8
{% endhighlight %}

算数循环:

{% highlight bash %}
for ((initialization; ending condition; update))
do
    statements
done
{% endhighlight %}

**array**

array赋值
{% highlight bash %}
arrayname[1]="array1"
arrayname[2]="array2"

arrayname=([1]="array1" [2]="array2")

arrayname=("array1" "array2")
{% endhighlight %}

访问某一元素
{% highlight bash %}
${arrayname[index]}
{% endhighlight %}

当array的下表为\*或@时，会被展开成数组中所有的元素；当用双引号("")括起来时，\*和@的处理方式和参数一致,参考**特殊变量**部分

{% highlight bash %}
# iterate array
# 也可以用 ${arrayname[@]} 或 ${arrayname[*]} 来替代 "${arrayname[@]"
for i in "${arrayname[@]}";do
    echo $i
done
{% endhighlight %}

数组长度: ${#arrayname[*]}, ${#arrayname[@]}    
数组元素长度 ${#arrayname[index]}

更多数组操作    
[The Ultimate Bash Array Tutorial with 15 Examples](http://www.thegeekstuff.com/2010/06/bash-array-tutorial/)
