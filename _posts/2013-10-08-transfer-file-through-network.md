---
layout: post
title: 传输文件命令scp与rsync
tags: [Linux Command]
---

**scp**

{% highlight bash %}
#将远程文件传至本地
scp -P port user@host:/path/to/src_file /path/to/dest_file

scp 常用选项：  
-r recursively copy a directory
-P port
-p Preserves modification times, access times, and modes from the original file.
-v verbose 
-l limit used bandwidth,specified in kbit/s. 
{% endhighlight %}


**rsync**

当传输大量文件时，更好的做法是用rsync，rsync只传输不同的部分，传输较快

{% highlight bash %}
rsync -avzu --progress --bwlimt=100 -e 'ssh -l username -p port' ip:path/to/src_files /path/to/dest_file

-a 归档模式，保留文件的原有属性（如权限、组、所有人等），且递归传输
-v verbose，显示详细信息
-z 传输时进行压缩
-u 如果目的文件比原文件更新，则跳过
--progress 显示传输进度
--bwlimit=KBPS 传输限速，KBytes per second
-e(--rsh=COMMAND)  选择远程shell，例子中是ssh
{% endhighlight %}
