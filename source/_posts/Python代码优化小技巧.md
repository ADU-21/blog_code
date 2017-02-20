---
title: Python代码优化小技巧
date: 2016-03-29 20:26:33
tags: [Python, 代码]
---
# python代码优化小技巧
一个良好的算法能够对性能起到关键作用，因此性能改进的首要点是对算法的改进。在算法的时间复杂度排序上依次是：

>  O(1) -> O(lg n) -> O(n lg n) -> O(n^2) -> O(n^3) -> O(n^k) -> O(k^n) -> O(n!)

因此如果能够在时间复杂度上对算法进行一定的改进，对性能的提高不言而喻。但对具体算法的改进不属于本文讨论的范围，读者可以自行参考这方面资料。下面的内容将集中讨论数据结构的选择。
<!-- more -->
## 字典 (dictionary) 与列表 (list)
Python 字典中使用了 hash table，因此查找操作的复杂度为 O(1)，而 list 实际是个数组，在 list 中，查找需要遍历整个 list，其复杂度为 O(n)，因此对成员的查找访问等操作字典要比 list 更快。


清单 1. 代码 dict.py

``` python
from time import time 
 t = time() 
 list = ['a','b','is','python','jason','hello','hill','with','phone','test', 
'dfdf','apple','pddf','ind','basic','none','baecr','var','bana','dd','wrd'] 
 #list = dict.fromkeys(list,True) 
 print list
 filter = [] 
 for i in range (1000000): 
     for find in ['is','hat','new','list','old','.']: 
         if find not in list: 
             filter.append(find) 
 print("total run time:")
 print(time()-t)
 ```
 
上述代码运行大概需要 16.09seconds。如果去掉行 #list = dict.fromkeys(list,True) 的注释，将 list 转换为字典之后再运行，时间大约为 8.375 seconds，效率大概提高了一半。因此在需要多数据成员进行频繁的查找或者访问的时候，使用 dict 而不是 list 是一个较好的选择。
## 集合 (set) 与列表 (list)
set 的 union， intersection，difference 操作要比 list 的迭代要快。因此如果涉及到求 list 交集，并集或者差的问题可以转换为 set 来操作。


清单 2. 求 list 的交集：
``` python
from time import time 
t = time() 
lista=[1,2,3,4,5,6,7,8,9,13,34,53,42,44] 
listb=[2,4,6,9,23] 
intersection=[] 
for i in range (1000000): 
    for a in lista: 
        for b in listb: 
            if a == b: 
                intersection.append(a) 
print("total run time:")
print (time()-t)
```
上述程序的运行时间大概为：

 	total run time: 
 	38.4070000648

清单 3. 使用 set 求交集
``` python
from time import time 
t = time() 
lista=[1,2,3,4,5,6,7,8,9,13,34,53,42,44] 
listb=[2,4,6,9,23] 
intersection=[] 
for i in range (1000000): 
    list(set(lista)&set(listb)) 
print("total run time:")
print(time()-t)
```
改为 set 后程序的运行时间缩减为 8.75，提高了 4 倍多，运行时间大大缩短。读者可以自行使用表 1 其他的操作进行测试。 
表 1. set 常见用法 
语法	操作	说明

	set(list1) | set(list2)	union	包含 list1 和 list2 所有数据的新集合
	set(list1) & set(list2)	intersection	包含 list1 和 list2 中共同元素的新集合
	set(list1) - set(list2)	difference	在 list1 中出现但不在 list2 中出现的元素的集合

清单 4. 利用 Lazy if-evaluation 的特性
``` python
from time import time 
t = time() 
abbreviations = ['cf.', 'e.g.', 'ex.', 'etc.', 'fig.', 'i.e.', 'Mr.', 'vs.'] 
for i in range (1000000): 
    for w in ('Mr.', 'Hat', 'is', 'chasing', 'the', 'black', 'cat', '.'): 
        if w in abbreviations: 
        #if w[-1] == '.' and w in abbreviations: 
            pass
print("total run time:")
print(time()-t)
```
在未进行优化之前程序的运行时间大概为 8.84，如果使用注释行代替第一个 if，运行的时间大概为 6.17。
## 字符串的优化

python 中的字符串对象是不可改变的，因此对任何字符串的操作如拼接，修改等都将产生一个新的字符串对象，而不是基于原字符串，因此这种持续的 copy 会在一定程度上影响 python 的性能。对字符串的优化也是改善性能的一个重要的方面，特别是在处理文本较多的情况下。字符串的优化主要集中在以下几个方面：

在字符串连接的使用尽量使用 join() 而不是 +：在代码清单 7 中使用 + 进行字符串连接大概需要 0.125 s，而使用 join 缩短为 0.016s。因此在字符的操作上 join 比 + 要快，因此要尽量使用 join 而不是 +。

清单 5. 使用 join 而不是 + 连接字符串
``` python
from time import time 

t = time() 
s = ""
list = ['a','b','b','d','e','f','g','h','i','j','k','l','m','n'] 
for i in range (10000): 
    for substr in list: 
        s+= substr     
print("total run time:")
print(time()-t)
```

同时要避免：
``` python
s = ""
for x in list: 
   s += func(x)
```
而是要使用： 
``` python
slist = [func(elt) for elt in somelist] 
s = "".join(slist)
```
当对字符串可以使用正则表达式或者内置函数来处理的时候，选择内置函数。如 str.isalpha()，str.isdigit()，str.startswith(('x', 'yz'))，str.endswith(('x', 'yz'))
对字符进行格式化比直接串联读取要快，因此要使用
``` python
out = "<html>%s%s%s%s</html>" % (head, prologue, query, tail)
```
而避免
``` python
out = "<html>" + head + prologue + query + tail + "</html>"
```

## 其他优化技巧

如果需要交换两个变量的值使用 a,b=b,a 而不是借助中间变量 t=a;a=b;b=t；
``` python
>>> from timeit import Timer 
>>> Timer("t=a;a=b;b=t","a=1;b=2").timeit() 
0.25154118749729365
>>> Timer("a,b=b,a","a=1;b=2").timeit() 
0.17156677734181258
>>>
```

> * 使用局部变量，避免"global" 关键字。python 访问局部变量会比全局变量要快得多，因 此可以利用这一特性提升性能。
> * if done is not None 比语句 if done != None 更快，读者可以自行验证；
> * 在耗时较多的循环中，可以把函数的调用改为内联的方式；
> * 使用级联比较 "x < y < z" 而不是 "x < y and y < z"；
> * while 1 要比 while True 更快（当然后者的可读性更好）；
> * build in 函数通常较快，add(a,b) 要优于 a+b。