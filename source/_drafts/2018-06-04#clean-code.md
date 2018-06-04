---
title: 代码整洁之道
date: 2018-06-04 15:42:03
categories: Java
tags:
- DDD
- Java
---

老马给开发工程师的三个建议：

```
1）学习业务领域知识；

2）将学习的重点放到软件开发和原则而不是具体技术之上；

3）提升自身与其他团队成员之间的沟通能力。
```

# 命名

- 类名和对象名应该是名词或名词短语，如 Customer, WikiPage, Account, AddressParser。避免使用 Manager, Processor, Data 或 Info 这样的类名。类名不应该是动词 
- 方法名应当是动词短语，如 postPayment, deletePage 或 save, getter/setter 应该遵从 JavaBean 标准使用 get, set 和 is 前缀。 
- <http://www.oracle.com/technetwork/java/javase/documentation/spec-136004.html> JavaBeans Spec 
- - 　　JavaBean是一个公共（public）的类。 
  - 　　JavaBean有一个不带参数的构造方法。 
  - 　　JavaBean通过setXXX方法设置属性，通过getXXX方法获取属性。 

# 函数

- 方法的参数应该越少越好，最理想的情况是没有参数。 
- 标示参数：给方法传入Boolean 类型的 flag 最好遵循这种命名方式：renderForSuite(true)，而不是 render(Boolean isSuite) 
- 使用异常而不是错误返回码，如果觉得 try catch 丑陋可以把 try 的主体部分抽离出来，形成独立的业务方法，在被抽离出来的方法里 throw Exception。（错误处理很重要，但是如果它搞乱了代码逻辑，就是错误的做法） 
- 使用异常类而不是错误码，错误码的枚举不易于维护，异常类可以继承以处理更复杂的关系，也更表意，符合 开闭原则 
- - 如果是 Error.java 这样的一个枚举来处理异常，每次都要去修改这个核心类，如果是继承的话，我需要增加异常的时候只需要多一个子类就可以，而不用去修改父类。 

# 注释

- 在我们使用注释之前应该意识到应该尽可能使用方法和变量名而不是使用注释。但是仍然有一些情况是适合于使用注释的： 
- - 法律信息：例如版权 
  - 提供信息的注释：例如某个抽象方法的返回值 
  - 警示：例如解释为什么要关闭某个测试用例 
  - TODO 

# 对象和数据结构

- 面向过程还是面向对象： 

- - 过程式代码（使用数据结构的代码）便于在不改动既有数据结构的前提下添加新函数，面相对的代码便于在不改动基友函数的前提下添加新类。 

  - 过程式代码难以添加新数据结构，因为必须修改所有函数，面向对象代码难以添加新的函数因为必须修改所有类。 

  - 所以，对于面向对象较难的事情，对于过程式代码却较容易，反之亦然！ 

  - 小结： 

    对象暴露行为，隐藏数据。便于添加新对象类型而无需修改既有行为，同事也难以在既有对象中添加新行为。数据结构暴露数据，没有明显的行为。便于向既有数据结构添加新行为，同时也难以向既有函数添加新数据结构。 

    在任何系统中，我们有时会希望能够灵活地添加新的数据类型，所以更喜欢在这部分使用对象。另一些时候我们希望更灵活地添加行为，这时候我们更喜欢使用数据类型和过程对象。优秀的程序员不带成见地了解这种情形，并依据手边工作的性质选择其中一种手段。 

- The Law of Demeter: 模块对象不应了解他所操作的对象的内部结构。对与面向过程的数据结构，就可以暴露内部结构，而面向对象的对象最好就不要，因此在使用对象之前要做好选择。 

- DTO 应该是一种符合 bean 规范的数据结构，而不是一个类，所谓类应该是有方法的。 

# 单元测试

- （测试流程上的要求）TDD 三定律： 
- - 1、You must write a failing unit test before you write production code.——测试先行 
  - 2、You must stop writing that unit test as soon as it fails; and not compiling is failing.——测试一旦失败，开始写生产代码 
  - 3、You must stop writing production code as soon as the currently failing test passes.——老测试一旦通过，返回写新测试 
- （测试本身的要求）TDD 整洁的测试应该遵循 FIRST五条规则： 
- - Fast 快速 
  - Independent 独立 
  - Repeatable 可重复 
  - Self-Validating 自足验证，以 Boolean 作为输出而不是手动对比任何 
  - Timely 单元测试应该在恰好使其通过的生产代码之前编写 

# 系统设计

- 降低耦合度带来的好处包含了依赖倒置（Dependency Inversion Principle）这个原则提倡依赖应该依赖于抽象而不是依赖于具体细节。 
- 构造与使用分离。 
- - 一种方法是将全部构造过程搬迁到main 或者被称之为 main 的模块中，设计系统的其余部分时，假设所以对象都已正确构建和设置。 
  - 有一种强大的机制可以实现分离构造和使用，就是 Dependency Injection 和 Inversion of Control 

