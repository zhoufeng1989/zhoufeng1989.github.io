---
layout: post
title: Ps 命令和进程状态
tags: [Linux Command]
---
**ps**显示当前的进程信息，即process snapshot。

ps 接受一下几种选项：

+   UNIX 选项, 前面有横线（-,dash）；
+   BSD 选项, 前面没有横线；
+   GNU 长选项，前面有两条横线。

不同类型的选项可以混用，但是不保证不产生冲突。

**不加任何选项时的默认行为：**    

>selects all processes with the same effective user ID (euid=EUID) as the current user 
and associated with the same terminal as the invoker.  
It displays the process ID (pid=PID), the terminal associated with
the process (tname=TTY), the cumulated CPU time in [dd-]hh:mm:ss format
(time=TIME), and the executable name (ucmd=CMD).   
Output is unsorted by default.   

**常用选项**：     

UNIX选项：

+   -e 所以进程，等价于 -A
+   -f full-format, -F extra full-format
+   -l long-format
+   -H 以层次显示进程
+   -L 显示线程信息
+   -o 定制显示列
+   -u userlist 根据进程的real user name 或 ID 筛选进程，进程的real user 是指进程创建者
+   -U userlist 根据进程的effective user ID（EUID）或name来筛选进程，进程的effective user 是指进程的文件访问权限所来自的用户
+   -C commandname，根据进程名显示进程
+   -p pid，根据pid显示进程

BSD选项：

+   a 显示拥有终端（tty）的所有进程，当和x连用时，显示所有进程
+   u user-oriendted format   
+   f 以层次显示进程
+   m 显示线程信息
+   o 定制显示列

**一些例子**：    
{% highlight bash %}
显示所有进程
ps axu 
ps -ef
显示httpd进程树
ps -C httpd -Hf
显示自定义列
ps -eopid,tt,user,fname,tmout,f,wchan
显示pid=42的进程名，comm后面的等号表示不显示列名（COMMAND）
ps -p 42 -o comm=
{% endhighlight %}

**进程状态**：STAT列    

+   D 不可中断的睡眠状态，一般是在进行磁盘IO，进程正处于系统调用中，不响应任何异步信号，不可被中断，只有当进程从系统调用返回时，才会响应信号进行信号处理，如果进程较长时间处于该状态，可能是硬件出了问题。
+   R 正在执行或可被执行，进程控制块（task_struct）被放入了CPU的可执行队列上
+   S 可中断的睡眠状态，处于该状态的进程在等待某事件的发生（如socket连接，信号量等），进程控制块被放入相应事件的的等待队列中，当事件发生时，队列中的一个或多个进程会被唤醒
+   T 暂停状态或被跟踪状态，进程接受了sigstop信号，便进入了这个状态，进程暂停执行，当又接收sigcont信号时，进程恢复执行；当用gdb进行调试时，进程进入被跟踪状态，与此类似，当调试进程退出，进程才恢复执行
+   X 退出状态，进程即将被销毁（should never be seen）
+   Z Defunct， 进程成为僵尸进程，已经结束了进程，但是没有从进程表中删除，如果进程太多会导致进程表条目过多，导致系统崩溃，除进程表条目，不占用其他内存空间

除了上述进程状态外，ps还提供了一些其他信息：  

+   < 进程有较高优先级
+   N 进程有较低优先级
+   L has pages locked into memory (for real-time and custom IO)
+   s 进程是session leader
+   l 多线程进程
+   \+ 进程在前台进程组（foreground process group）
