---
layout: post
title: Python包/模块管理tips
tags: [Python]
---

**import**

Python中的module不会被重复导入，只在第一次导入的时候进行:

1.  编译成.pyc文件
2.  创建module对象，执行module文件
3.  将module对象加入当前名字空间（赋值）。

当执行from module import *
时，除了名称以下划线开头的变量和没在__all__列表中出现的变量外，module中的所有其它变量被加入当前的名字空间

reload方法会重新导入module。

**package**

package是一个目录，里面有一个__init__.py文件，当导入package时，__init__.py被执行，__init__.py中初始化的变量都被加入package的命名空间中。


最后注意一点，import 和 from import语句都在当前的名字空间中创建了变量名，用于指向被导入的对象。

