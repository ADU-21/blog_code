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

本周六应邀参加了 [FreeCodeCamp](https://www.freecodecamp.org/)社区的一次线下活动，主题为《DevOps从理论到实践》，借此机会整理一下近年来看到听到的对于 DevOps 的定义，并产出一些自己的观点。

# 什么是DevOps

##  DevOps 是一个流行词 

![](/images/devops_word_cloud.png)

如今 DevOps 已经成为一个流行词，很多公司都在说自己在做 DevOps，但是每个人、每家公司理解的 DevOps 又不尽相同，从 DevOps 诞生的第一天起，如何定义 DevOps 就是一个争论不休的话题。

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

### The Three Ways

- **The First Way：** System Thinking （系统思考：强调全局优化，避免局部优化）
- **The Second Way：** Amplify Feedback Loops （经过放大的反馈回路：创建从开发过程下游至上游的反馈环）
- **The Third Way：** Culture of Continual Experimentation And Learning（持续做试验和学习的文化：持续做试验，承担风险、从失败中学习；通过反复实践来达到精通）

![](/images/devops_CALMS_model.png)

### CLAMS

- **Culture – 文化：**公司各个角色一起担当业务变化，实现有效协作和沟通；建立包括运维在内的跨职能协作文化,具有共同目标的一体化团队。这是DevOps运动的根本
- **Automation – 自动化：**在价值链中尽量除去手工步骤；自动化一切可以自动化的步骤，降低部署和发布的难度
- **Lean – 精益：**运用精益原则更频繁地交付价值；以精益的方式小步快跑，对过程与技术进行持续改善
- **Metrics – 度量：**度量并使用数据来优化交付周期；通过建立有效的监控与度量手段来获得反馈,推动产品和团队的持续改进, 支持业务决策
- **Sharing – 分享：**分享成功和失败的经验来相互学习。

# 为什么要实践 DevOps

了解了 DevOps 的历史，再来看为什么要实践 DevOps，笔者认为主要有以下三点：

- 更短的交付周期，生产环境部署频率越来越快，简化生产部署流程，且自动化不停机部署
- 更高的价值，形成特性提出到运营数据、用户反馈验证的实验性交付闭环，基于实际用户反馈调整计划和需求
- 更好的质量保障，在代码检查，功能和非功能验证，以及部署各方面建立较完善的质量保障体系，尤其是自动化测试集
- 更高绩效的团队，包含业务，开发测试，和运维职能在内的一体化团队，以产品交付为共同目标紧密协作，共同承担责任

# DevOps 在技术领域的实践

DevOps运作包括**文化**（全功能，自运维）和**技术**（自动化，度量反馈）两方面，而技术能力的改进主要关注以下六个领域：

![](/images/devops_practice.png)

### 内建质量体系

通过持续代码评审，静态分析，自动化测试，自动部署验证等手段构成一套有效的质量保障体系。

主要实践包括：

- TDD：测试驱动开发的思想，保证代码质量和不偏离业务需求的技术实现
- 结对编程和代码审查，依靠团队的自治性让团队成员互相监督和审查代码质量
- 自动化测试，高自动化，且高频率运行的测试，保证测试用例质量的同时保证了交付软件的质量

### 持续部署

通过自动化的构建，部署过程快速频繁地将软件交付给用户，提高吞吐量；同时保障过程的安全，平滑，可视。

主要实践包括：

- 在已经做到持续集成的情况下，引入持续部署，每次提交均会出发构建并执行部署
- 蓝绿部署，用于实现零宕机发布新版本
- 金丝雀发布，用于使应用发布流程具备快速试错的能力

### 持续监控

持续对运行环境在系统，应用层面进行监控，及时发现风险或问题，保障系统运行的稳定性。

主要实践包括：

- 监控预警，在项目开始初期就引入监控，让整个团队实时能够收到关于产品各个维度数据的反馈
- 日志聚合，便于错误追踪和展示
- 分析，利用搜集到的数据实时分析，利用分析结果指导开发进度

### 度量与反馈

通过对用户行为或业务指标的度量或反馈收集，为产品的决策提供依据。

主要实践包括：

- 持续集成反馈，对代码构建质量，代码质量审查的反馈
- 测试反馈，对软件质量，功能性的测试，给到业务的反馈
- 运营数据反馈，新功能上线后对业务影响的反馈，用于指导业务人员提新的需求

### 环境管理

通过对服务器环境的定义，自动化建立和配置、更新等提高基础设施管理的效率，一致性，并更有效利用资源，可伸缩的架构，保证服务的健壮性。

主要实践包括：

- 弹性架构，保证服务的吞吐量和具备灵活变更的能力
- 自动化部署脚本，想胶水一样，用于解决一些工程实践不够完善的流程之间的衔接
- 基础设施即代码，用代码定义基础设施，便于环境管理，追踪变更，以及保证环境一致性

### 松耦合架构

对传统应用架构进行领域组件化，服务化，提升可测试性和可部署性。

主要实践包括：

- 采用弹性基础设施，比如公有云服务或是 PaaS（Platform as a Service） 平台
- 构建为服务应用
- 引入契约测试

![](/images/devops_full_picture.png)

<center>**典型DevOps的持续交付流水线全景图**</center>

![](/images/devops_pipeline.png)

<center>**软件开发全生命周期的持续优化**</center>

# 未来 & 趋势

- **DevOps 话语权越来越多被平台厂商掌握**

  ​    在 DevOps 实践的第一阶段，往往会是 Jenkins, Nexus, Ansible, Shell 等一系列工具的拼凑组合，上手难度大，维护成本高，开发体验不好。随着 DevOps 日渐成熟，以 AWS、Pivotal、RedHat 为代表的一些公司分别退出自己的 “DevOps产品”，或是一套完整的工具链，或者直接整合到一个 PaaS 平台，甚至一些产品直接将“敏捷”，“精益”的概念也整合到产品中，直接可以把一家公司的全部业务放到平台上，这和最近大热的“数字化平台战略”也是相吻合的。

  ​    不管怎样，这些平台厂商一边卖自己的产品一边重新定义着 DevOps，随着平台的完善，DevOps 已经变得越来越不重要，我一直觉得最好的 DevOps  团队应该是“润物细无声”的，就是一个团队不用提 DevOps，整个团队很自然地就能关注到业务价值的交付，且能有序地按照高质量，高效率的要求去做，平台或许能帮助我们做到这一点。

- **容器化 &  微服务仍然是 DevOps 应用和发展的主要领域**

  ​    容器化、微服务天然适合小而全的功能团队，且一个个自治的服务也很复合 DevOps 端到端交付团队的设计，近年随着容器化技术（Docker）的发展，容器管理（Kubernetes）的日渐成熟（据悉，github 已经将它们的一部分产品环境灰度发布到了 kubernetes 上，京东也将他们的服务百分之六十采用了 kubernetes 管理），DevOps 和微服务成为了相辅相成的两个趋势。

- **安全成为推动 DevOps 全面发展的重要力量**

  ​    安全是 DevOps 永远绕不开的话题，也往往是新技术在传统行业（例如金融和电信）应用中的最大阻碍。一方面，组织结构的转型迫使企业要打破原先的部门墙，这意味着很多原先的控制流程不再适用。另一方面，由于大量的 DevOps 技术来源于开源社区，缺乏强大技术实力的企业在应用相关技术时不免会有所担忧。

  ​    DevOps 全局优化的特点与安全社区提出的 “Build Security In”也特别吻合，加之越来越多安全易用的工具涌现，DevOpsSec 会越来越被人们熟知。

> 参考资料：
>
> <http://insights.thoughtworkers.org/instantiate-the-principles-of-devops/>
>
> <http://www.jianshu.com/p/5781489e8431>