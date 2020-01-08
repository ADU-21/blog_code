---
title: 使用 Arthas 分析接口响应时间
date: 2019-11-26 21:05:48
categories: 工具
tags: 
- 工具
- 分析
- JVM
---

Arthas 是阿里巴巴开源的 Java 诊断工具，本文介绍如何使用 Arthas 分析 Java 应用的接口响应时间。

<!-- more -->

# 分析接口响应时间

在日常开发中，当我们发现应用的某个接口响应比较慢，这个时候想想要分析一下原因，找到代码中耗时的部分，比较容易想到的是在接口链路的 IO 操作上下游打印时间日志，再根据几个时间点的日志算出耗时长的 IO 操作。

这种方式没有问题，但是加日志需要发布，既繁琐又低效，这个时候可以引入一些线上 debug  的工具，arthas 就是很好的一种，除了分析耗时，还可以打印调用栈、方法入参及返回，类加载情况，线程池状态，系统参数等等，其实现原理是解析 JVM 在操作系统中的文件，大部分操作是只读的，对服务进程没有侵入性，因此可以放心使用。

本文以一个 Spring boot 应用为例，分析一个接口的耗时情况。

* 启动应用

  

  ```bash
  git clone https://github.com/ADU-21/hello_springboot
  cd hello_springboot
  ./gradlew bootRun
  curl 127.0.0.1:8080/sayHello    # 得到返回说明启动成功
  ```



- 启动 Arthas

  

  ```bash
  curl -O https://alibaba.github.io/arthas/arthas-boot.jar
  java -jar arthas-boot.jar
  ```

  在随后出现的列表中，选择`com.example.demo.DemoApplication`，这是 Spring boot 启动的进程。看到 arthas 的 loga 说明 attach 成功。

  ![](/images/arthas-start.png)

  

- 找到待分析的类，并打印方法调用时间。

  分析接口调用时间，其实就是分析 Controller 类的方法调用时间，使用 `sc` 命令可以帮助找到待分析的类完整的路径和名称，`trace` 命令可以分析调用时间，还可以使用 `watch`命令实时获取方法调用参数和返回参数。

  

  ```bash
  sc *Controller  
  trace com.example.demo.controller.MyController sayHello
  ```

  再次请求 `sayHello` 接口，就能看到实时的接口调用耗时情况：

  ![](/images/arthas-running.png)

  从调用栈和时间中可以轻易找到耗时较长的调用。

# 线上 Debug

当你发现某个线上问题却没有日志追踪时可以尝试使用这种方式事实查看方法的入参和出参。

- 使用 [watch](<https://alibaba.github.io/arthas/watch.html>) 命令可以打印出接口实时传入和返回的数据：

  

  ```bash
  watch com.example.demo.controller.MyController sayHello "{params,target,returnObj}" -x 2
  ```

  `-x` 表示遍历的深度，`"{params,target,returnObj}"` 表示打印的内容，除此之外还可以根据耗时、是否抛出异常进行过滤，执行效果如图：

  ![](/images/arthas-watch.png)

# 小结

[这里](<https://alibaba.github.io/arthas/arthas-tutorials?language=cn>)有一个官方提供的 Arthas 学习体验教程，提供了一个虚拟的环境用于感受 Arthas 的各种功能，简单好用，推荐一试。

# References

> * <https://alibaba.github.io/arthas/>