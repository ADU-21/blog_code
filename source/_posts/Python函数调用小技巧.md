---
title: Python函数调用小技巧
date: 2016-04-02 09:54:08
categories:
- 学习总结
tags: Python

---

python的兴起其中一个不容忽视的因素就是他的简洁和易读，要想写好python代码以下几个方法你不得不知道：
<!-- more -->
## filter
filter(function, sequence):
对sequence中的item依次执行function(item)，将执行结果为True的item组成一个List/String/Tuple(取决于sequence类型）返回，示例如下：
``` python
>>> def f(x): return x % 2 != 0 and x % 3 != 0
>>> filter(f, range(2, 25))
[5, 7, 11, 13, 17, 19, 23]
>>> def f(x): return x != 'a'
>>> filter(f, "abcdef")
'bcdef'
```

## map
map(function, sequence):
对sequence中的item依次执行function（item），将执行结果组成一个List返回
另外map也支持多个sequence，当然这也要求function支持相应数量的参数输入，示例如下：

``` python
>>> def cube(x): return x*x*x
>>> map(cube, range(1, 11))
[1, 8, 27, 64, 125, 216, 343, 512, 729, 1000]
>>> def cube(x) : return x + x

>>> def add(x, y): return x+y
>>> map(add, range(8), range(8))
[0, 2, 4, 6, 8, 10, 12, 14]
```

## reduce
reduce（function，sequence，starting_value):
对sequence中的item顺序迭代调用function，如果有starting_value，还可以作为初始值调用，例如可以用来对List求和，示例如下：

``` python
>>> def add(x,y): return x + y
 >>> reduce(add, range(1, 11))
（注：1+2+3+4+5+6+7+8+9+10）
>>> reduce(add, range(1, 11), 20)
（注：1+2+3+4+5+6+7+8+9+10+20）
```
## lambda：
这是python支持一种有趣的语法，它允许你快速定义单行的最小函数，类似C语言中的宏，可以用在任何需要函数的地方，示例如下：
``` python
>>> g = lambda x: x * 2
>>> g(3)
6
>>> (lambda x: x * 2)(3)
6
```

我们也可以把filter map reduce 和lambda结合起来用，函数就可以简单的写成一行。例如
``` python
>>>kmpathes = filter(lambda kmpath: kmpath, map(lambda kmpath: string.strip(kmpath), string.split(l, ':')))
```

看起来麻烦，其实就像用语言来描述问题一样，非常优雅。
对 l 中的所有元素以':'做分割，得出一个列表。对这个列表的每一个元素做字符串strip，形成一个列表。对这个列表的每一个元素做直接返回操作(这个地方可以加上过滤条件限制)，最终获得一个字符串被':'分割的列表，列表中的每一个字符串都做了strip，并可以对特殊字符串过滤。

[原文链接](http://www.jianshu.com/p/81b12f4eae3a)
