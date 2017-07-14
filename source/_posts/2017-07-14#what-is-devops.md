---
title: DevOps 从理论到实践
date: 2017-07-14 18:35:34
categories: DevOps
tags:
- DevOps
- 持续交付
- 敏捷
- DevOps文化
- CALMS
---

# 什么是DevOps

##  DevOps is a Buzzword 

![](/images/devops_word_cloud.png)

如今 DevOps 已经成为一个流行词，很多公司都在说自己在做 DevOps， 但是每个人、每家公司理解的 DevOps 又不尽相同，从 DevOps 诞生的第一天起，如何定义 DevOps 就是一个争论不休的话题。

这里有一篇文章，笔者认为基本诠释了 DevOps 的定义：[DevOps 是什么不是什么](https://mp.weixin.qq.com/s?__biz=MzI3OTEzNjI1OQ==&mid=2651492848&idx=1&sn=1a644a2624633489248dda2834dc9572&chksm=f0b2609cc7c5e98a0145eead91b68db41d18dc33bc1be46f08026d1746b1063a6c177b7daf24#rd)

如果你没有耐心把这篇文章看完，[维基百科](https://en.wikipedia.org/wiki/DevOps)还给出了一个太长不读版：

>  DevOps (a clipped compound of "development" and "operations") is a software development and delivery process that emphasizes communication and collaboration between product management, software development, and operations professionals.It seeks to automate the process of software integration, testing, deployment, and infrastructure changes by establishing a culture and environment where building, testing, and releasing software can happen rapidly, frequently, and more reliably.

归纳成三点：

- DevOps 是一种强调沟通与协作的软件交付过程。它包括产品管理，软件开发及运营等各个方面。
- DevOps 自动化软件集成，测试，部署以及基础设施的变更。
- 它的目标是建立一种文化和环境，使得软件的构建、测试、交付更快，更频繁，更可靠。

![](/images/dev_ops_qa.png)

## DevOps 的由来

参见 [DevOps编年史](http://www.jianshu.com/p/f40209023006) 和一个 Youtube 小视频： [The (Short) History of DevOps](https://www.youtube.com/watch?v=o7-IuYS0iSE) 

![](/images/history_of_devops.png)

这里我想多提一句的是持续交付和 DevOps 的关系和差别，参照[维基百科](https://en.wikipedia.org/wiki/Continuous_delivery) 对 DevOps 和持续交付的区别进行解释，DevOps 涵盖的范围比持续交付更宽，它包含了文化，强调团队协作和自动化；而持续交付侧重于频繁、快速 	地执行交付流程，两者相辅相成，却又有所区别。

> Continuous delivery and DevOps are similar in their meanings and are often conflated, but they are two different concepts. DevOps has a broader scope, and centers around the cultural change, specifically the collaboration of the various teams involved in software delivery (developers, operations, quality assurance, management, etc.), as well as automating the processes in software delivery.Continuous delivery, on the other hand, is an approach to automate the delivery aspect, and focuses on bringing together different processes and executing them more quickly and more frequently. Thus, DevOps can be a product of continuous delivery, and CD flows directly into DevOps.

## DevOps 理论框架

因为DevOps源自草根，缺乏自上而下的理论支撑，所以如何定义 DevOps 成了 DevOps 社区里面的一个大难题。一些 DevOps 从业者，纷纷设定自己的 DevOps 框架。其中比较有名的框架有Damon Edwards所定义并被 Jez Humble（持续交付作者之一） 所修订的[CALMS](https://blog.rackspace.com/quantifying-devops-capability-its-important-to-keep-calms)，和Gene Kim所定义的[The Three Ways](https://www.duyidong.com/2017/07/12/the-three-ways-understanding-devops/)。

### The Three Ways:

- **The First Way：** System Thinking （系统思考：强调全局优化，避免局部优化）
- **The Second Way：** Amplify Feedback Loops （经过放大的反馈回路：创建从开发过程下游至上游的反馈环）
- **The Third Way：** Culture of Continual Experimentation And Learning（持续做试验和学习的文化：持续做试验，承担风险、从失败中学习；通过反复实践来达到精通）

![](/images/devops_CALMS_model.png)

### CLAMS:

- **Culture – 文化：**公司各个角色一起担当业务变化，实现有效协作和沟通；建立包括运维在内的跨职能协作文化,具有共同目标的一体化团队。这是DevOps运动的根本
- **Automation – 自动化：**在价值链中尽量除去手工步骤；自动化一切可以自动化的步骤，降低部署和发布的难度
- **Lean – 精益：**运用精益原则更频繁地交付价值；以精益的方式小步快跑，对过程与技术进行持续改善
- **Metrics – 度量：**度量并使用数据来优化交付周期；通过建立有效的监控与度量手段来获得反馈,推动产品和团队的持续改进, 支持业务决策
- **Sharing – 分享：**分享成功和失败的经验来相互学习。

# 为什么要实践 DevOps

了解了 DevOps 的历史，再来看为什么要实践 DevOps，笔者认为主要有以下三点：

- 打破开发、运维、测试的部门墙，加强部门间合作，甚至创造端到端交付团队增加软件交付效率
- 优化架构管理，使得架构更具弹性，容错能力更强，更易于变更
- 优化软件交付流程，使应用具备快速试错的能力

# DevOps 在技术领域的实践

DevOps运作包括**文化**（全功能，自运维）和**技术**（自动化，度量反馈）两方面，而技术能力的改进主要关注以下六个领域：

![](/images/devops_practice.png)

### 内建质量体系

通过持续代码评审，静态分析，自动化测试，自动部署验证等手段构成一套有效的质量保障体系。



### 持续部署

通过自动化的构建，部署过程快速频繁地将软件交付给用户，提高吞吐量；同时保障过程的安全，平滑，可视。

### 持续监控

持续对运行环境在系统，应用层面进行监控，及时发现风险或问题，保障系统运行的稳定性。

### 度量与反馈

通过对用户行为或业务指标的度量或反馈收集，为产品的决策提供依据。

### 环境管理

通过对服务器环境的定义，自动化建立和配置、更新等提高基础设施管理的效率，一致性，并更有效利用资源，可伸缩的架构，保证服务的健壮性。

### 松耦合架构

对传统应用架构进行领域组件化，服务化，提升可测试性和可部署性。

# 未来 & 趋势

> 参考资料：
>
> <http://insights.thoughtworkers.org/instantiate-the-principles-of-devops/>