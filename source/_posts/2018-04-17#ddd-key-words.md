---
title: 领域驱动设计（DDD）实践
date: 2018-04-17 19:49:36
categories: 学习总结
tags:
- DDD
- Java
- 架构
- 设计
---

DDD（Domain Driven Design, 领域驱动设计）是一套原则、约束和方法。广义上的 DDD 是为了解决解决跨部门间语言沟通问题，在业务、产品、开发之间建立领域通用语言（Ubiquitous Language）以提高沟通效率。落地到开发团队，DDD 可以指导我们进行架构设计、团队划分、拆分微服务、乃至代码层面的设计。主要解决系统无法随业务演变而演变的问题。

```java
// Bad
List<Item> items = order.getItems();
items.remove(item);

// Good
order.removeItem(item);
```

# 为什么要 DDD

传统的设计模式中，不管是 [MVC，MVP，还是 MVVM](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html) 都是数据驱动的。核心上基于 M 推送消息，V 或 P 来订阅 这个模型。使用者需要维护的不再是 UI 树，而是抽象的数据。(通过数据，可以随时构建出新的 UI 树) ，这种设计在短时间内可以灵活地应对 UI 的变化，但是对于真实的业务变化（比如数据结构的变化），就需要在各个模块都进行修改，修改的内容分散、影响面广、风险大，长此以往，原本架构清晰的系统随着迭代的不断演化，业务逻辑变得越来越复杂，我们的系统也越来越冗杂。模块彼此关联，谁都很难说清模块的具体功能意图是啥。修改一个功能时，往往光回溯该功能需要的修改点就需要很长时间，更别提修改带来的不可预知的影响面。

![](https://tech.meituan.com/img/ddd-in-practice/%E6%9C%8D%E5%8A%A1%E8%80%A6%E5%90%88.svg)

<center>**一个常见的系统耦合病例（图片来自：[领域驱动设计在互联网业务开发中的实践](https://tech.meituan.com/DDD%20in%20practice.html)）**</center>

之所以产生这种现象，归根到底在于随着业务的变化系统架构变得不清晰，划分出来的模块内聚度低、高耦合。理想情况下，如果我们的代码逻辑按照业务结构来组织，代码架构就可以随着业务的变化变化，这样代码就会变得易于维护；由于要去阅读代码的人首先需要具备领域知识，代码又是对业务的直接呈现，代码的可读性也会极大提高。

任何一种理念的落地都不是一蹴而就的，我下面从战术设计和代码实践的角度，围绕一个案例探讨一下 DDD 的几个核心概念。

# 六边形架构（Hexagonal Architecture）

六边形架构（Hexagonal Architecture），又称为**端口和适配器**风格，最早由[Alistair Cockburn](http://alistair.cockburn.us/Hexagonal+architecture)提出。在DDD社区得到了发展和推广，之所以是六变形是为了突显这是个扁平的架构，每个边界的权重是相等的。

我们知道，经典分层架构分为三层（展现层、应用层、数据访问层），而对于六边形架构，一般会分成三层：

- **领域层**（Domain Layer）：最里面，纯粹的核心业务逻辑，一般不包含任何技术实现或引用。
- **端口层**（Ports Layer）：领域层之外，负责接收与用例相关的所有请求，这些请求负责在领域层中协调工作。端口层在端口内部作为领域层的边界，在端口外部则扮演了外部实体的角色。
- **适配器层**（Adapters Layer）：端口层之外，负责以某种格式接收输入、及产生输出。比如，对于 HTTP 用户请求，适配器会将转换为对领域层的调用，并将领域层传回的响应进行封送，通过 HTTP 传回调用客户端。在适配器层不存在领域逻辑，它的唯一职责就是在外部世界与领域层之间进行技术性的转换。适配器能够与端口的某个协议相关联并使用该端口，多个适配器可以使用同一个端口，在切换到某种新的用户界面时，可以让新界面与老界面同时使用相同的端口。

这样做的好处是将使业务边界更加清晰，从而获得更好的扩展性，除此之外，**业务复杂度和技术复杂度分离**，是 DDD 的重要基础，核心的领域层可以专注在业务逻辑而不用理会技术依赖，外部接口在被消费者调用的时候也不用去关心业务内部是如何实现。

![](https://image.slidesharecdn.com/javadev-hexagonalarchitectureforjavaapplications-150202062634-conversion-gate01/95/hexagonal-architecture-for-java-applications-10-638.jpg?cb=1423245064)

<center>**图片来自：[Hexagonal architecture for java applications](https://www.slideshare.net/fabricioepa/hexagonal-architecture-for-java-applications)**</center>

# 聚合（Aggregate）

## 聚合根（Aggregate Root）

## 限界上下文（Bounded Context）

# 应用服务（Application Service）

# 领域模型（Domain Model）

# 资源库（Repository）

# 领域事件（Domain Event）



> * References:
> * <https://www.cnblogs.com/netfocus/archive/2011/10/10/2204949.html>
> * <https://tech.meituan.com/DDD%20in%20practice.html>
> * <https://insights.thoughtworks.cn/from-sandwich-to-hexagon/>