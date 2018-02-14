---
title: Java系列（五）——UML类图和面向对象五大原则
date: 2018-02-15 10:50:33
categories: Java
tags:
- 面向对象
- 学习
- 小结
---

最近在项目上经历了一次比较大的重构，把之前不太清晰一些类关系梳理了一下，突然发现 UML 图是个很好用的东西，特此温习一下。顺便复习一下面向对象五大原则，对 clean code 可以说是有很大的帮助。

# UML 类图

## 对象

UML 图中类和接口对象的表示如图所示：

![](/images/Class_Interface_UML.png)

## 关系

在UML类图中，对象之间常见的有以下几种关系: 泛化（Generalization）, 实现（Realization），关联（Association)，聚合（Aggregation），组合(Composition)，依赖(Dependency)。

### 泛化关系（Generalization）

对于面向对象语言，UML 中所说的泛化关系就是指类的**继承**关系，如图所示，Tiger 是 Animal 的子类：

![](https://upload-images.jianshu.io/upload_images/1346433-af095465558161ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/202)

<center>**泛化** 图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

### 实现（Realization）

一个类实现一个接口，如图所示：

![](https://upload-images.jianshu.io/upload_images/1346433-4cea5dc72a898986.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/204)

<center>**实现** 图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

### 组合(Composition)

体现整体与部分间的关系，整体与部分是不可分的，部分不能离开整体而单独存在，整体的生命周期结束也就意味着部分的生命周期结束；比如公司和部门的关系：

![](https://upload-images.jianshu.io/upload_images/1346433-84881e93d5ef165a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/218)

<center>**组合** 图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

### 聚合（Aggregation）

和组合一样，也是整体与部分的关系，但是程度要稍弱一些，部分可以独立于整体单独存在，如车和轮胎是整体和部分的关系，轮胎离开车仍然可以存在。

![](https://upload-images.jianshu.io/upload_images/1346433-3ee670e59b466df0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/389)

<center>**聚合** 图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

### 关联（Association)

上面提到的组合和聚合都可以说是关联关系的一种，如果 A 类中成员变量是用 B 类（接口）来声明的变量，那么 A 和 B 的关系是关联关系，称 A 关联于 B。关联关系可以是双向的。表示方式如图所示：

![](https://upload-images.jianshu.io/upload_images/1346433-f2e9554f7e2b3d20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/558)

<center>**关联** 图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

### 依赖(Dependency)

如果 A 类中某个方法的参数是用 B 类（接口）来声明的变量或某个方法返回的数据类型是 B 类型，那么 A 和 B 是依赖关系。即一个类的实现需要另一个类的协助，所以要尽量不使用双向的互相依赖。表示方式如图所示：

![](https://upload-images.jianshu.io/upload_images/1346433-1ab91901e0af0725.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/196)

<center>**依赖** 图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

各种关系的强弱顺序：
**泛化 = 实现 > 组合 > 聚合 > 关联 > 依赖**
下面这张UML图，比较形象地展示了各种类图关系：

![](https://upload-images.jianshu.io/upload_images/1346433-9ec0a861d9b58072.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

<center>图片来源于：[简书-详解UML图之类图](https://www.jianshu.com/p/4cd95d4ddb59)</center>

这里还有一篇用 UML 图来表示经典设计模式的文章：[图说设计模式](http://design-patterns.readthedocs.io/zh_CN/latest/index.html)，非常不错，值得一读。

# 面向对象五大原则（S.O.L.I.D）

面向对象有三大特征：封装(Encapsulation)、继承(Inheritance)、多态(Polymorphism)，还有五大原则：**SOLID**（**单一功能、开闭原则、里氏替换、接口隔离**以及**依赖反转**），当这些原则被一起应用时，它们使得一个程序员开发一个容易进行软件维护和扩展的系统变得更加可能。SOLID被典型的应用在测试驱动开发上，并且是敏捷开发以及自适应软件开发的基本原则的重要组成部分。

## 单一职责原则（Single-Resposibility Principle）

> 对象应该仅具有一种单一功能（职责），只有一个引起它变化的原因。	

职员类例子： 比如在职员类里，将工程师、销售人员、销售经理这些情况都放在职员类里考虑，其结果将会非常混乱，在这个假设下，职员类里的每个方法都要ifelse判断是哪种情况，从类结构上来说将会十分臃肿，并且上述三种的职员类型，不论哪一种发生需求变化，都会改变职员类，这就增加了代码的维护成本。

## 开放封闭原则（Open-Closed principle）

> “软件体应该是**对于扩展开放**的，但是**对于修改封闭**的。“

如果要在一个软件中增加新的模块的时候不需要修改现有核心模块，那这个设计就是满足开放封闭原则的。这就要求在给出一个设计时，首先考虑到用户需求的变化，将应对用户变化的部分设计为对扩展开放，而设计核心部分应该抽象出不改动或者很少改动的接口或抽象类。这个过程会在设计开始时和重构同不断发生。如下图所示， DataBaseInterface 就是系统中修改关闭的部分，而接口的实现 SqliteDatabase 和 CustomDatabase 就是对扩展开放的部分。

![](https://upload-images.jianshu.io/upload_images/458529-45767c7119b61c69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/358)

<center>**开闭原则** 图片来自：[谈谈面向对象设计(OOD)原则](https://www.jianshu.com/p/e378025920f8)</center>

## 里氏替换原则（Liskov-Substituion Principle）

> “程序中的对象应该是可以在不改变程序正确性的前提下被它的子类所替换的”

**派生类（子类）对象能够替换其基类（超类）对象被使用。**也就是任何基类可以出现的地方，其子类一定可以出现。里氏替换原则是继承和复用的基石，只有当衍生类可以替换掉基类，而软件单位的功能不受到影响时，基类才能真正的被复用，而衍生类也能够在基类的基础上增加新的行为。

## 接口隔离原则（Interface Segregation Principle）

> “多个特定客户端接口要好于一个宽泛用途的接口”

**客户端不应该被迫依赖于它所不使用的接口（方法）。**简单来说就是客户端需要什么接口，就提供给它什么样的接口，其它多余的接口就不要提供，不要让接口变得臃肿，否则当对象一个没有使用的方法被改变了，这个对象也将会受到影响。接口的设计应该遵循最小接口原则，其实这也是高内聚的一种表现，使得系统更容易重构，更改和重新部署。

举个例子（这个例子来源于[简书](https://www.jianshu.com/p/e378025920f8)）：比如我们有个自行车接口，这个接口包含了很多方法，包括GPS定位，以及换挡的方法

![](https://upload-images.jianshu.io/upload_images/458529-6413661553a89ece.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/249)

<center>**不满足ISP原则**</center>

然后我们发现即便普通的自行车也需要实现GPS定位以及换挡的功能，显然这违背了接口隔离的原则。遵循接口最小化的原则，我们重新设计：

![](https://upload-images.jianshu.io/upload_images/458529-0ae1f5ed465562dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/478)

<center>**满足ISP原则**</center>

这样一来每个接口的功能相对单一，CommonBike 类不需要实现它不使用的接口，这也就满足了ISP原则。

## 依赖反转原则（Dependecy-Inversion Principle）

> 实体必须依靠抽象而不是具体实现。它表示高层次的模块不应该依赖于低层次的模块，它们都应该依赖于抽象。

简单来说就是程序要依赖于抽象接口，不要依赖于具体实现。要对抽象进行编程，不要对实现进行编程，进而降低客户与实现模块间的耦合。

![](https://upload.wikimedia.org/wikipedia/commons/9/96/Dependency_inversion.png)

<center>图1中，高层对象A依赖于底层对象B的实现；图2中，把高层对象A对底层对象的需求抽象为一个接口A，底层对象B实现了接口A，这就是依赖反转。图片来源：[维基百科](https://zh.wikipedia.org/wiki/%E4%BE%9D%E8%B5%96%E5%8F%8D%E8%BD%AC%E5%8E%9F%E5%88%99)</center>

传统的结构化编程中，最上层的模块通常都要依赖下面的子模块来实现，也称为高层依赖低层；DIP 原则就是要逆转这种依赖关系，让高层模块不依赖低层模块，所以称之为依赖倒置原则。

# 小结

重构应该是在软件开发过程中无时无刻不在发生的事，当我们在重构之前，不妨先用 UML 图理清楚对象之间的关系，在设计的时候时时记得五大原则，对编写出可读性、稳定性以及可扩展性较高的代码肯定是有帮助的。最关键的软件开发工具是受过良好设计原则训练的思维。

> * References:
> * <https://www.jianshu.com/p/4cd95d4ddb59>
> * <https://www.cnblogs.com/olvo/archive/2012/05/03/2481014.html>
> * <https://zh.wikipedia.org/wiki/SOLID_(%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E8%AE%BE%E8%AE%A1)>
> * <https://www.jianshu.com/p/e378025920f8>
> * <https://wxs.me/2036>