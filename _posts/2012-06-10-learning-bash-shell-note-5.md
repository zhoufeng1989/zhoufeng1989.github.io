---
layout: post
title: 《learning the Bash shell》笔记-流程控制
tags: [Bash]
---

**if else**

if 检查该关键字后的command list中最后一条command的退出状态(exit status)，如果为0,则条件为true。    

退出状态是 if 语句唯一能识别的信息，[ condition ] 语句会返回exit status ，告知if 语句condition 是 true/false。注意[后与]前的空格

[]中condition的分类:

+   字符串比较
<figure>
<a href="/images/string-cmp.jpg"><img src="/images/string-cmp.jpg"></a>
</figure>
注：string要加双引号，否则若string为空时，会出现 [ -n ]，从而报错，加上""后为[ -n "" ],it's OK!

+   文件比较
<figure>
<a href="/images/file-cmp.jpg"><img src="/images/file-cmp.jpg"></a>
</figure>

+   整数比较
<figure>
<a href="/images/number-cmp.jpg"><img src="/images/number-cmp.jpg"></a>
</figure>


可以用()将测试条件分组，但是()必须转义 \\(\\).  -a 和 -o 表示 && 和 ||，但是必须在[ ]中才能使用。
举个例子：
{% highlight bash %}
if [ -n "$dirname" ] && [ \( -d "$dirname" \) -a  \( -x "$dirname" \) ]
then
...
{% endhighlight %}

**return 和 exit**

return只能在function或source命令执行的scripts中才能使用；

exit N 会退出整个script，而不管嵌套多深

**for**

循环，依次处理list中的元素。
{% highlight bash %}
for var in list ;do
....
....
done
{% endhighlight %}

**while 和 until**

{% highlight bash %}
while condition do 
    statements... done 
 
until command ; do 
    statements... done 
{% endhighlight %}
 
记住:   
Note that the only difference between while and until is the way the condition
is handled.     
In while, the loop executes as long as the condition is true;     
in until, it runs as long as the condition is false.

{% highlight bash %}
# 分行打印$PATH各路径
path=$PATH:
while [ $path ]
do
    echo ${path%%:*}
    path=${path#*:}
done


#以命令返回状态作为判断条件
n=0
while ! mkdir dir$n
do
    n=$((n+1))
done
{% highlight python %}
