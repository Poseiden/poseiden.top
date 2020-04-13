---
layout: post
title: Generator of Python
date: '2016-04-19 21:42:39 +0800'
comments: true
abbrlink: 16887
categories:
---
近日，趁着找工作的间隙，了解了一下Python中一直让我不解的generator。现在特此写一篇博客记录下来自己的理解，也好让自己理解的透彻一点吧。

##### 环境：Python3.4

<!--more-->

(这篇文章只是用于个人以后回顾来看，所以只会针对自己当时没有理解的点来叙述，而并不是要从零开始讲generator)

### 先举个普通function的栗子。
```
def fun_ton(value):
    return value

f = fun_ton

print(f)
<function fun_ton at 0x7f5c6a90c7b8>

f = fun_ton(10)

print(f)
10
```

在上面这个例子的输出中，可看到，在`f = fun_ton`之后，f是个普通的function；在`f = fun_ton(10)`之后，f是一个返回的value（也就是说，方法已经执行并执行完了！这点尤为重要我认为）

### 而再举个generator的栗子

```
def gen():
    while True:
        value = yield
        print(value)
            
g = gen
print(g)
<function gen at 0x7f5c6a90cf28>

g = gen()  
print(g)
<generator object gen at 0x7f5c6a90b2d0>

g.send(1)
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-20-41df5ae58f24> in <module>()
----> 1 g.send(1)

TypeError: can't send non-None value to a just-started generator

```

在上面这个例子的输出中，可看到。在`g = gen`之后，仍旧是个function，（之前我一直认为只要是函数体内有yield，这里就应该输出generator了，而现在看来并不是），而在`g = gen()`之后，g才是一个generator。（暂且不管为什么，我们先往下看）。
执行`g.send(1)`，然后就是初识generator的熟悉的错误了。查询N多文章之后，得出的结论是，`send(value)`（value不为None）的使用环境一定是让generator执行到yield之后。所以，虽然在普通的funciton中,`f = fun_ton(value)`是执行函数的意思，但是在generator中看来，`g = gen()`并不是执行的意思，而是创建（或者相当于这个意思）。这样，在一个generator中，才需要用`next()`方法或者`send(None)`方法来让generator启动。
有了上面这段理解，至少在现阶段理解generator不那么让人摸不着头脑了。


### 参考

- [Python之生成器详解](http://kissg.me/2016/04/09/python-generator-yield/)
- [Improve Your Python: 'yield' and Generators Explained](https://jeffknupp.com/blog/2013/04/07/improve-your-python-yield-and-generators-explained/)
- 以及StackOverflow上的问答。