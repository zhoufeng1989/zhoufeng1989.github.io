---
layout: post
title: 《learning the Bash shell》笔记-特殊字符和通配符
tags: [Bash]
---

**什么是shell**

shell 是个解释器，负责将用户的命令转换成操作系统可执行的指令。

**Bash中的特殊字符**

{% raw %}

|  字符     |     意义
|:----------|:---------------------
|    ~      |   home directory
|    `      |   command substitution
|    #      |   comment
|    $      |   variable expression
|    &      |   background job
|    *      |   string wildcard
|    (      |   start subshell
|    )      |   end subshell
|    \      |   quote next character
|    \|     |   pipe
|    [      |   start character-set wildcard
|    ]      |   end character-set wildcard
|    {      |   start command block
|    }      |   end command block
|    ,      |   shell command sepeartor
|    '      |   strong quote
|    "      |   weak quote
|    <      |   input redirect
|    >      |   output redirect
|    /      |   pathname directory seperator
|    ?      |   single-character wildcard
|    !      |   pipeline logincal NOT

{% endraw %}

**通配符**

在shell执行前，通配符用于扩展文件/路径名，若有匹配，则扩展成匹配后的形式；若没有匹配，则保持原状。但是需要注意的是，\{\}里面的通配符都会先展开，再看有没有匹配。

{% raw %}

|   通配符  |   意义
|:----------|:------------
|   ?       |   any single character
|   *       |   any string or characters
|   [set]   |   any single character in set
|   [!set]  |   any single character not in set
|   {a..z}  |   any character from a to z
|   {abc,de}|   any string in {} seperate by comma

{% endraw %}

若不想让特殊字符发挥特殊作用，三种方法将它们转义 \"\"、\'\'、\\

注意单双引号的区别：

双引号作用与单引号类似，区别在于它没有那么严格。单引号告诉shell忽略所有特殊字符，而双引号只要求忽略大多数，具体说，括在双引号中的三种特殊字符 不被忽略：$(dollar),\\,\`(command substitution)
,即双引号会解释字符串的特别意思,而单引号直接使用字符串.如果使用双引号将字符串赋给变量并反馈它，实际上与直接反馈变量并无差别。如果要查询包含空格的字符串，经常会用到双引号
