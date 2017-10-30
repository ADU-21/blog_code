---
title: 分支模型与主干开发
date: 2017-10-29 15:47:20
categories: 持续交付
tags:
- git
- coding
- 持续集成
---

最近我和我的所在的团队在翻译一个网站： <https://trunkbaseddevelopment.com/>主干开发。本文结合笔者的开发经验，谈一谈项目中用到过的分支模型与使用场景，对主干开发做一个简单的介绍。

# Gitflow

Gitflow 最早应该是出现在一篇名叫[A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)的文章中，阮一峰的[Git 工作流程](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)做了更便于理解的阐述。正如问中提到，Gitflow 偏向控制管理，比较多地使用分支，试用 GitFlow 的项目基本上工作流会是这个样子:

![](/images/gitflow_branches.png)

<center>图片出自:[关于两种CI/CD策略以及git分支模型的思考](https://yaowenjie.github.io/devops/thinking-in-two-kinds-of-ci-cd-strategies-and-git-branch-models)</center>

Master 分支用于发布，Develop 分支用于存放待发布（不稳定）的版本。在迭代计划里，Epic 会被先划分为一个个 Feature，一个 Feature 表示一个完整的功能，这个feature 会被拆成一个个更小的 Story（卡），如果 Feature 小的话也可以是一个 Feature 对应一个 Story，一个 Story 从被创建出来到最终上线会经历以下过程：

- Dev 领卡，从 Develop 分支创建一个 Feature 分支。
- 在 Feature 分支进行开发并通过本地测试（单元测试和部分集成测试）。
- 从 Feature 分支发起一个 Pull-Request 到 Develop 分支。
- 相关人员进行 Code Review，并将 Feature 分支合并到 Develop 分支。
- 写卡 Dev 删除 Feature 分支，并将卡挪到 Test，交给 QA 测试。
- QA 在 Develop 分支测试完成，把卡挪到 Ready for release。
- 等待 Realease Master 将该 Feature 从 Devlop 分支 Merge 到 Master。
- QA 在 Master 分支进行测试。
- 测试通过后进行发布，并在Master 分支打上 Release Tag。

可以看出流程是颇为复杂的。不过在一个团队成员流动相对较小，大家对 Gitflow 都比较熟悉的情况下，实施过程倒是没有遇到任何问题。而且可以感觉得到，这样的分支模型下的发布非常有计划性，Dev 之间的开发冲突也比较少（得益于 Feature 划分合理）。但在时间长了以后，问题还是逐一暴露出来，主要有以下几个：

- **重复测试**，一个功能从开发到上线至少要经历三次内容重合度很高的测试：本地，Develop 分支合并，Master 分支合并；如果有 Fix bug，Merge 回 Master 还要多测一次，每一次都可能有意外的结果，而且 Develop 分支的测试和 Master 分支的测试内容几乎是一模一样的。
- **Release Master 的存在**，在一个目标为持续发布的敏捷团队里 Release Master 的存在是不合理的，Release Master 需要在上线前的一段时间一直盯着 Pipeline（持续交付流水线），这不但意味着一个劳动力的缺失，并且一个人要想掌握一次发布的左右更改细节和影响也是几乎不可能的，所以到后来每次上线前 Release Master 都要组织一次 Release Meeting，所有开发在一起讨论这次 Release 的 Feature，非常浪费时间。
- **并没有做到持续交付**，在 Gitflow 得分支模型下，发布是非常有计划的，一个 Feature 必须要经过以上这么多步骤才能到达生产环境，在时间上平均一个 Feature 都要等待 两周时间才能长线，这样的等待并非是需求上的“按计划发布”，而是从技术上就造成了发布瓶颈，显然难以达到持续交付的要求的。

# Github Flow

相比 Gitflow，Github Flow 就要简单很多，介绍可以参考[Github 官方文档](https://guides.github.com/introduction/flow/)，同样在[这篇文章](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)中也可以看到中文版。在项目上使用 Github Flow 基本上分支会变成这个样子：

![](/images/githubflow_branches.png)

所有 Story 直接提交到 Feature 分支，再从 Feature 分支发 Pull-Request 到主分支（Master 或 Develop），Pull-Request 是为了方便 Code Review，相比于 Gitflow，这种方式因为省去了一些分支而降低了复杂度，同时也更复合持续集成的思想，以一张故事卡为集成的最小单位，相对来说集成的周期短，反馈的速度也快，能够及早的遇到问题，从而及早的解决问题。

Github flow 的另一个好处在于，可以处理跨团队协作问题。当时的项目是一个多团队共享的基础设施代码库，大部分团队需要同样的功能，就从主库 Fork 一份代码，一旦产品团队产生定制化的需求，就可以在自己的代码库里更改，并向主库发一个 Pull-Request，如果主库的维护团队认为这是一个有通用价值的更改，则会接受合并到主库中。这种方式就既保证了分布式团队拥有代码和主库的同步，又让各团队都可以向主库贡献代码，非常适合多个独立团队工作在一个代码库的情形。

![](https://git-scm.com/figures/18333fig0502-tn.png)

<center>图片来源：[GIT 之我见](https://ruby-china.org/topics/29263)</center>

# Trunk Based Development

顺着持续集成的思想，如果我们把上一种分支模型做得再极致一点，我们不要 Feature 分支，或者把 Feature 分支只留在本地；不需要使用 Pull-Request 而是直接 Push 到远程 Master 分支，我们就做到了 Trunk based Development。（关于从 GitFlow 到 TBD 的论述，TW 同事尚齐在洞见上有一篇[Gitflow有害论](http://insights.thoughtworkers.org/gitflow-consider-harmful/)值得一读，另外想要了解得更细致可以去到[官网](https://trunkbaseddevelopment.com/)（[中文版](https://cn.trunkbaseddevelopment.com/)），里面详细列举了各种实践和反模式。）本文主要就项目上落地过程中遇到的一些问题做个简要的说明。

使用主干开发后，我们的代码库原则上就只能有一个 Master 分支了，所有新功能的提交也都提交到 Master 分支上，没有了分支的代码隔离，测试和解决冲突都变得简单，持续集成也变得稳定了许多，问题也接踵而至，主要有以下三个：

- 如何避免发布的时候引入未完成的 Feature 
- 如何进行线上 Bug Fix
- 如何重构

![](http://paulhammant.com/images/what_is_trunk.jpg)

<center>图片来自:[What is Trunk-Based Development?](https://paulhammant.com/2013/04/05/what-is-trunk-based-development/)</center>

## 如何避免发布引入未完成 Feature

答案是： Feature Toggle。(参照 Martin 的文章进行Well Less well的细化)

既然代码要随时保持可发布，而我们又需要只有一份代码来支持持续集成，在代码库里加一个特性开关来随时打开和关闭新特性是最容易想到的也是最容易被质疑的解决方案。

Feature Toggle 是有成本的，不管是在加 Toggle 的时候的代码设计，还是在移除 Toggle 时的人力成本和风险，都是需要和它带来的价值进行衡量的。事实上，在我们做一个前端的大特性变更的时候，我们确实没有因为没办法 Toggle 而采用了一个独立的 Feature 分支，我们认为即使为了这个分支单独做一套 Pipeline，也比在前端的各种样式间添加移除 Toggle 来得简单。但同时，团队商议决定在每次提交前都要先将 Master 分支 Merge 到 Feature 分支，以此避免分支隔离久以后合并时的痛苦。

## 如何进行线上 Bug Fix

在发布时打上 Release Tag，一旦发现这个版本有问题，如果这个时候Master分支上没有其他提交，可以直接在 Master 分支上 Hot Fix，如果 Master 分支已经有了提交就要做以下三件事：

- 从 Release Tag 创建发布分支。
- 在 Master 上做 Fix Bug 提交。
- 将 Fix Bug 提交 Cherry Pick 到 Release 分支。
- 在Release 分支再做一次发布。

线上 Fix 通常都比较紧急。看完这个略显繁琐 Bug Fix 流程，你可能会问，为什么不在 Release 分支直接 Fix，再合并到 Master 分支？

这样做确实比较符合直觉，但事实是，如果在 Release 分支做 Fix，很可能会忘了 Merge 回 Master，试想深夜两点你做完 Bug Fix 眼看终于上线成功，这时的第一反应就是“终于可以下班了。什么，Merge 回 Master？ 明天再来吧“ 等到第二天你早已把这个事忘得一干二净。而问题要等到下一次上线才会被暴露出来，一旦发现，而这个时候上一次 Release 的人又不在，无疑增加了很多工作量。

## 如何重构

这里指的是比较大规模的重构，无法在一次提交完成，TBD 要求每一次提交都是一个可上线的版本，所以这同时还意味着这个重构无法再一个上线周期内完成。

这种情况，需要在代码设计中增加一个抽象层，保证在重构过程中先不动原来的代码，也不破坏既有功能，类似于蓝绿部署中的负载均衡器的作用，这样的流程就是：

![](/images/TBD_Abstraction_layer.png)

- 在将要被重构的代码逻辑附近引入抽象层然后提交，对所有人可见。如果有需要可以是多个提交，这些提交都不能破坏 build，然后依次 push 到共享代码库。


- 为将要被引入的代码写抽象层的第二次实现，然后提交。但在主干上由于关闭状态所以其他开发人员暂时不依赖于它。如果需要的话，这可能像上面那样需要多次提交。第一步的抽象层也可能偶然被调整，但必须遵循同样的原则：不能破坏build。
- 切换使用重构后的代码，然后 Push。
- 删除原有的旧实现（被重构代码）
- 删除抽象层

这个流程和汽车换轮胎有那么点类似，新旧轮胎代表重构前后代码，抽象层就好比千斤顶。

![](https://cn.trunkbaseddevelopment.com/branch-by-abstraction/cars.png)

<center>图片来自: [主干开发：抽象分支](https://cn.trunkbaseddevelopment.com/branch-by-abstraction/)</center>

# 一点感受

TBD 还因为被 Google，亚马逊这样的公司采用而闻名，可以参照阮一峰的另篇文章：[谷歌的代码管理](http://www.ruanyifeng.com/blog/2016/07/google-monolithic-source-repository.html)，但并不因此意味着 TBD 就适用于所有场景。即使是是 CICD 已经被广泛接受，也不能称持续交付为软件开发的银弹。技术用的对不对，还是要看上下文。