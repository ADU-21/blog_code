---
title: 再谈 DevOps
date: 2018-07-28 00:19:31
categories: DevOps
tags:
- 翻译
- DevOps
- Platform
---

最近在北美市场掀起一阵平台化的热潮，IT 公司基本上在按照”自动化工具集->DevOps平台->数据化平台-> AI 平台“的思路向前演进。加上 k8s 和各种 CloudNative 工具推波助澜，在今年 DevOps 似乎仍是一个热度不减的次词汇。

<!--more-->

这时我看到了阮一峰老师提到的[Let's talk DevOps job titles](https://mattouille.com/articles/2018-06/lets-talk-devops-job-titles)， 将  DevOps 分为”运维、发布管理、平台工程“，觉得是介于”DevOps is next generation of Operators“ 和 ”DevOps is everything“ 之间的一个理解，和我们的实践相差不大，而且易于理解，遂决定一译。我并不打算完全还原作者的意图，一些地方加入了我自己的观点。以下为正文：

这些年来 DevOps 社区一直为 DevOps 职责的定义争论不休，DevOps 作为一种哲学将是永远高于现在企业应用到 DevOps 的三个关键词：运维、平台工程，和发布管理。

## Google 的 SRE 工程师

为了简单的解释 DevOps 和运维之间的区别我想在开始引入 Google 的 SRE(Site Reliability Engineering, 站点可用工程)进行阐明。如果你有读过Google的 [SRE 系列书](https://landing.google.com/sre/book.html) 就一定会知道 SRE 是 Google 对运维的一套实践，这是最简单的从开发视角看待运维的解释。

如果你在做运维之前做过开发，或许你会发现 Google 所提倡的 SRE 和你现在做的运维没多大区别，但不幸的是这个世界上大部分公司不是在按这个方式工作，开发和运维之间是有壁垒的，这也是为什么很多人在听到 DevOps 的时候有如梦初醒的感觉。

但 SRE 也并不代表软件工程师比系统工程师更擅长解决分布式架构中的基础设施问题。但 SRE 意味着系统工程师必须学会以软件工程师的方式和从软件开发流程上解决问题。

如此一来，SRE 就和传统运维的脚本作业产生了差别，系统工程师需要了解软件层级的知识以更好地服务于产品。对企业而言，你并不需要一个资深的软件工程师，但是你需要一个有能力构建和改进流程，将运维技能服务化的工程师。

Amazon 和 Facebook 也有着和 SRE 类似的职位，然而他们对于这个职位的定义和 SRE 却是不一致，我认为 SRE 的定义是比较准确的。

## 运维

众所周知，运维领域正在经历一次革新，云时代带来的变革让传统运维发生巨大的转变，运维不能只在是写写脚本就能解决问题，同时还需要精通各种工具和软件架构。但这也并不就是说所有公司都要开始寻找 SRE 这样的人才，也不是所有公司都需要。

更多的公司是在找寻熟知CICD(Continues Integration, Continues Devlivery，持续集成与持续交付)这样的工程师，他们熟悉一些操作系统命令，能够帮助企业解决日常问题。但随着软件复杂性越来越高，我不确定这样的模式还会持续多久。

## 平台工程

Platform 和 Cloud 的定义很接近，但并不完全一致。Platform 相比于 Cloud 拥有更高的定制化。

您可能会问什么是平台？答案是它是在虚拟机或容器上运行软件（如服务发现，网络，工件存储，数据存储等）的基础结构的组合。有一些预建的堆栈，如HashiStack，SaltStack，VMWare，OpenStack，Pivotal Cloud Foundry，Nginx都有自己的，最后最后少不了 Kubernetes。

它归结为为开发人员提供抽象资源来部署他们的产品，同时需要只适当的基础架构知识就可以管理。平台是将基础设施抽象化后的产品，就像软件一样，由基础设施和代码组成，将流程粘合在一起。

## 发布管理

之所以在这里去去掉了”工程“这个词语，是因为发布管理不是那么工程化的一个实践。

发布管理非常重要，随着敏捷的普及大多数公司经常会出现一天有多个产品在同时上线的情况，也是公司在变得规模化的途中所必定经历的一个变化。

做一个好的发布管理，你必须精通 CICD和软件发布流程，不仅要考虑到系统之间的依赖，还要考虑到团队之间的协作。一些公司有专门的团队编写流水线供别的团队使用，我认为这是一种反模式，发布流程应该由各个团队自己掌握同时能做到资源共享，平台化就是一个不错的选择。各个团队有自己 way of working 也很重要。

## 结论

关于 DevOps 有很大程度上与软件部分相关的内容我在文中并没有涉及，因为我认为这部分相关内容并无多少争议。我的目标是让大家对 DevOps 有一个比较一致的认识，我们可以在共同的上下文里讨论兴趣、技术和标准。

## 我最近的一些想法

从 DevOps 的历史来看，DevOps 的缘起就是要讲敏捷推广到运维，后来和微服务、容器化、云等技术呼应，运维变得越来越简单，团队的目标逐渐由技术指标提高到了”我们要更关注产品和产品的制作流程“，我是很赞同这个观点的。不管是软件开发还是运维、运营，你可能要学习很多知识，这些知识包含各种标准、各种协议、各种框架、各种语言、各种工具，但最终目的是为了解决某个问题，让公司从中获利。我们强调速度、强调质量、强调开发者体验的同时都不能忘了这个目的。

我最近工作的一个团队很有意思，每天早上起来第一件事是看报表，昨天有多少用户来了，干了些什么，哪些人遇到了退订了我们的产品，是什么原因。这些信息让我看到了一个有生命的产品，它是如何和这个时间交互，如何成长，而不是闭门造车似的实现甲方提出的需求。同时这是一个大公司里的小团队，因为人数很少没有角色划分，负责开发某一个功能，不管是弄挂了测试还是网关给 block 住了都是自己解决，和业务人员结对写需求和做用户验收测试，一张卡做完提交到主干分支马上上线。这种方式非常高效，既保证了大公司的流程制度，又有了创业公司的效率，有时间可以展开来写一写。