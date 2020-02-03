---
layout: post
title: "对Python高阶函数的一些理解"
date: 2016-03-23 08:59:16 +0800
comments: true
categories: 
---
本人是一名Java程序员，在最近学习python的过程中，因为思维惯性和没有认真理解python的概念，导致很多例子混淆。现在写下这篇笔记，让自己以后在回头看看的时候也有个参考。LoL。。。。。

##### Python版本：3.4.3
##### Python交互环境：ipython
##### 环境：Ubuntu14.04 LTS

<!--more-->

首先，在Python这种解释型语言中，要搞明白函数，和函数调用的区别。例如，在python中有一个内置的求绝对值的函数“abs”。
```
    In[1]: abs
    Out[2]:<function abs>

    In[2]:abs()
    ---------------------------------------------------------------------------
     TypeError                                 Traceback (most recent call last)
     <ipython-input-3-3a546e8778c7> in <module>()
     ----> 1 abs()

     TypeError: abs() takes exactly one argument (0 given)

```
以上例子中，在输入`abs`时，交互环境表示这是一个function,而在输入`abs()`时,交互环境报错，提示应该传入一个参数但是实际给了0个（这个翻译有点蹩脚）。这里就说明在输入`abs()`时，实际上已经调用了abs()函数，而不是仅仅表示这个函数。Ok，在讲清楚了这点之后，后面就好理解了。

在python中，变量可以指向函数，也就是用变量代表函数，而在Java中，这个是不可以的。例如：
```
    In[2]: f = abs

    In[3]: f
    Out[3]: <function abs> 

    In[4]: f(-1)
    Out[4]:1
```
在上面的例子中，在将f指向abs函数后，可以通过f()的方式同样调用abs()函数。

这些只是高阶函数的冰山一角，高阶函数的特性还允许将函数作为参数传入另一个函数，和把一个函数作为返回值返回，闭包等等。时间有限，暂且不表，在以后的笔记中再介绍吧。

### 参考
最后，推荐一个很好的学习python的网站。

- [廖雪峰的python3教程-高阶函数](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014317849054170d563b13f0fa4ce6ba1cd86e18103f28000)