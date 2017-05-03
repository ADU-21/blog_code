---
title: 云原生应用
date: 2017-05-03 11:04:03
tags:
- Cloud Native
- Cloud
- 翻译
- 云原生应用
---
原文链接：<https://pivotal.io/cloud-native>

##  更快部署，更低风险，和业务一同扩展

云原生应用程序是专为云上的应用而设计的。这些应用程序通过小型专用功能团队实现快速构建和频繁部署，提供了轻松扩展和硬件解耦的平台，让组织更敏捷，更具有弹性和云端可移植性。

## 什么是云原生应用？

**云原生是一种充分利用云计算优势，用于构建和部署应用的方式。**在过去的十几年，云已经重新定义了几乎所有行业的竞争格局，消除了对资本投资的关注，企业也不用雇佣员工去维护一个自建的数据中心，取而代之的是无限的计算存储能力，并按时按需付费。降低IT支出以为着降低了行业壁垒，使得初创公司可以很快地实践自己的想法并应用到市场。这正是为什么软件正在吞噬世界，而创业公司正在使用云原生的方式颠覆传统行业。

现在的组织需要一个集成了DevOps，持续交付，微服务和容器化得平台用于构建和运维云原生应用和服务。

![](/images/Cloud_Native_With_four.png)

 - **DevOps** 是开发和运维的合作，目标是自动化软件交付和基础设施更改过程。它创造了一个文化和环境，让构建，测试和发布软件可以快速，频繁，更可靠地发生。

![](https://d1fto35gcfffzn.cloudfront.net/images/topics/cloudnative/infographic-devops.png)

 - **持续交付**让单个应用随时处于可发布状态，而不用等待与其他变更绑定到一次发布中，比如一个维护的窗口。持续交付使得发布编程一个频繁且平常的过程，因此组织可以以更低的风险经常交付，并从最终用户获得更快的反馈，直到部署成为业务流程和企业竞争力的重要组成部分。

![](https://d1fto35gcfffzn.cloudfront.net/images/topics/cloudnative/infographic-cd.png)

 - **微服务**是将应用程序开发为小型服务的集合的架构方法;每个服务实现业务功能，运行在自己的进程中并通过HTTP API进行通信。每个微服务器都可以独立于应用程序中的其他服务进行部署，升级，扩展和重新启动，通常作为自动化系统的一部分，可以在不影响终端客户的情况下频繁更新线上应用。

![](https://d1fto35gcfffzn.cloudfront.net/images/topics/cloudnative/infographic-microservices.png)

- **容器**比虚拟机（VM）提供了更高的效率和更快地速度。使用操作系统（OS）级别的虚拟化，单个操作系统实例被动态划分为多个相互独立的容器，每个容器具有唯一可写的文件系统和资源配额。创建和销毁容器的低开销以及单个instance可高密度运行多个容器的特性使得容器成为部署各个微服务器的完美工具。

![](https://d1fto35gcfffzn.cloudfront.net/images/topics/cloudnative/infographic-containers.png)



Refer:
> * <https://pivotal.io/cloud-native>
> * <https://12factor.net/>