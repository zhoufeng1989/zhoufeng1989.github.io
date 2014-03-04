---
layout: post
title: 《learning the Bash shell》笔记-Bash 选项
tags: [Bash]
---

通过对选项的设置，可以改变Bash的某些行为。

{% highlight bash %}
# 设置选项:
set -o option-name
set -option-abbrev
# 去除选项
set +o option-name
set +option-abbrev
{% endhighlight %}

**一些有用的选项**

{% raw %}

|   选项    |   意义
|:----------|:-------
|   emacs   |   emacs编辑模式（默认）
|  noclobber|   输出重定向不允许覆盖已有文件
|   nounset |   使用未定义的变量时报错
|   vi      |   vi编辑模式，很多vi编辑命令可以
| noexec(n) |   检查语法错误而不执行
| verbose(v)|   执行前打印命令
| xtrace(x) |   命令解析完后打印命令

{% endraw %}

verbose/xtrace可用于debug。
