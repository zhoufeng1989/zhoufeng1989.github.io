---
layout: post
title: 《learning the Bash shell》笔记3
tags: [Bash]
---

**shell 脚本执行方式**

+   source/.
执行shell命令时不要求用户对shell 脚本有执行权限，而且脚本是在本shell中执行，不会为脚本fork子进程（subshell）

+   以文件名直接执行
需要对脚本文件有执行权限，如果只以文件名执行当前目录下的脚本，需要将当前目录加入$PATH,否则需要用路径执行，即./filename

+   sh script


除去source的执行方式，shell会fork子进程执行脚本，子shell只继承父shell中export的变量（拷贝变量副本）。


**Bash查找可执行程序的顺序**

1.  alias
2.  keywords(function,if,else..)
3.  function
4.  built_ins(cd,type..)
5.  scripts and executable programs(在$PATH中查找)

一些命令可以用来bypass shell部分查找命令的途径：

+  command命令——不查找alias和function，只在built_ins和scripts中查找命令     
-p 指定$PATH    
-v 显示详细                     
+   builtin命令——只查找built_ins                       
+   enable命令——disable built_ins，能够执行同名的脚本。             
-n built_ins #disable built_ins            
built_ins   #enable 

**type命令** 

type commandname——输出shell会如何解释该命令     
type -a/all commandname——输出shell对命令的所有可能解释     
type -p commandname——如果shell会以scripts或executable programs解释该命令，则返回    
type -P commandname———只搜索scripts或executable progrograms对命令的解释。
