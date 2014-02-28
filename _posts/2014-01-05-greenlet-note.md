---
layout: post
title: 解析 greenlet
tags: [Python]
---
[greenlet](http://greenlet.readthedocs.org)是Python的协程，通过各协程之间的切换，实现并发逻辑。

**greenlet 数据结构**:    
{% highlight python %}
typedef struct _greenlet {
	PyObject_HEAD
	char* stack_start;
	char* stack_stop;
	char* stack_copy;
	intptr_t stack_saved;
	struct _greenlet* stack_prev;
	struct _greenlet* parent;
	PyObject* run_info;
	struct _frame* top_frame;
	int recursion_depth; PyObject* weakreflist; PyObject* exc_type;
	PyObject* exc_value;
	PyObject* exc_traceback;
	PyObject* dict;
} PyGreenlet;

# 说明:    
# stack_start:greenlet在栈上开始的地址;
# stack_stop:greenlet在栈上结束的地址;
# stack_copy:greenlet在堆上开始的地址;
# stack_saved:greenlet保存在堆上的字节数;
# run_info:greenlet绑定的线程的信息,只允许同一线程的greenlet之间切换(switch)
# stack_prev:栈指针,指向前一个greenlet
{% endhighlight %}


**一些变量说明**:    

+   tstate:当前thread的状态字典,由PyThreadState_GET()得到,其中ts_curkey的值指向当前(current) greenlet
+   ts_current:当前执行的greenlet
+   ts_target:要切换到的目标greenlet
+   ts_origin:切换greenlet后，指向切换前的greenlet
+   ts_passaround_args ts_passaround_kwargs:切换greenlet时传递的参数


**greenlet堆栈布局**:    
{% highlight python %}

               |     ^^^       |
               |  older data   |
               |               |
  stack_stop . |_______________|
        .      |               |
        .      | greenlet data |
        .      |   in stack    |
        .    * |_______________| . .  _____________  stack_copy + stack_saved
        .      |               |     |             |
        .      |     data      |     |greenlet data|
        .      |   unrelated   |     |    saved    |
        .      |      to       |     |   in heap   |
 stack_start . |     this      | . . |_____________| stack_copy
               |   greenlet    |
               |               |
               |  newer data   |
               |     vvv       |

{% endhighlight %}
变化规则:       
当切换至其他greenlet时,会有部分数据由栈移至堆中(slp_save_state),避免被新的greenlet覆盖;
当重新切换回时,数据会由堆(如果有数据被保存在堆中)切换(slp_restore_state)回栈中

**创建main greenlet**: 

**static PyGreenlet* green_create_main(void)**   
当tstate[ts_curkey]为空时，说明该线程还没有greenlet，创建main greenlet,其中:    
{% highlight python %}
gmain->stack_start = (char*) 1;   
gmain->stack_stop = (char*) -1;
{% endhighlight %}
表示main greenlet 占据了整个stack


**greenlet 切换**:    

**static PyObject * g_switch(PyGreenlet* target, PyObject* args, PyObject* kwargs)**    
切换greenlet,将执行流切换至target：

1.  target没有执行过,stack_start被置为(char*)1，即最低地址,stack_stop被置为当前栈顶地址,表示整个栈被新greenlet占据,和该部分栈有重合的greenlet都要将数据拷贝至堆中;切换后调用target.run方法,调用结束后（target即结束),将执行流切换至target->parent,即当greenlet结束时,控制流会切换至父greenlet

2.  target执行过，则将执行流切换至target
    切换的过程会将所有stack_stop比target->stack_stop小的greenlet(stack_start~stack_stop)copy到堆中,以免发生覆盖;   
    如果target有在堆中的部分，则会resotre到栈中,总之，会保证当前执行的greenlet都在栈上;     
    切换的过程中,旧greenlet的stack_start会被置为当前堆栈地址

切换发生了什么?     
当前线程的信息(frame,exc_type,exc_value,exc_trackback)等被替换为target greenlet的对应值;   
把堆栈指针指向target greenlet的 stack_start 切换的过程本质是不同执行栈之间的切换,将greenlet在栈与堆之间进行memcpy操作。
