---
title: DevOps 技能树
date: 2017-05-18 16:30:47
categories: DevOps
tags:
- DevOps
- 职场
---
DevOps 是什么，在这些年的争论中似乎总难用一句话概括清楚，但随着技术社区、企业的推动和一些理念越来越多的被人们实践，这个概念已经变得越来越清晰。
最近成都 Cloud & DevOps 社区整理了一个 DevOps 技能树，很有意思。对研究 DevOps 而言，提供了一个不错的视角；对 DevOps 领域从业人员而言，可以做个自我定位和学习方向的参考。

> You build it, you run it.
> <p style="text-align:right">—— Amazon CTO：Werner Vogels</p>

# 社区技能树

<center>**成都 Cloud & DevOps Community 能力树**</center>
![](/images/Chengdu_DevOps_Community_Capability_tree.png)

这个能力树基本根据大家平时的项目经验构成，大项分为两类，**Best Practices （最佳实践）**基本包含了平时做 session 或者 proposal 需要领悟的一些概念和实践，学习方法主要是翻阅国内外大牛的博客。**Infrastructure & Tools（基础设施和工具）**是概念落地时需要的技术，学习方法主要是多用多练习，以及查阅文档。

对着个基于工作内容构成的偏技术/实践落地的版本，我对照了一下企业招聘的要求，试图梳理出 DevOps 角色画像。

# 企业如何定义 DevOps 角色能力

先来看看国内企业对 DevOps 的期望，以下是华为的招聘：

```
职位：DevOps运维专家

1.计算机相关专业本科毕业，5年以上的工作经验。（大型互联网企业背景优先）；

2.深入理解Linux操作系统、体系结构；

3.优秀的的开发能力，熟悉常用的自动化运维工具；

4.熟悉常用的持续集成工具；

5.熟悉常用的监控工具（例如Nagios、Zabbix等）；

6.熟悉常用数据库（例如MySQL, Oracle、MongoDB, Redis等）；

7.认同并坚持“Automate Everything You Can”的原则，认同DevOps文化，在开
源社群活跃并有积极贡献者优先。
```

关键字：Linux、CI/CD工具、监控、数据库、自动化。
对于开发技能只是提了一句优秀的开发技能，但却没对开发技能做要求，可以预见招进去之后应该是不会让你做开发的，这个岗位基本上是一个工作内容包含 Pipeline 搭建的，懂自动化的**运维**角色。————这和我在国内项目的感受是一致的。

再来看几家美国的企业招聘：

* **Ericsson：**

```
Title：Solution Architect - Development Operations (DevOps)

Experience with Groovy

Experience with Jenkins and the Jenkins DSL plugin.

Experience with Docker, Kubernetes, Vagrant

Experience with Mesos.

Experience with Plutora.

Experience with OpenShift / appOrbit.

Public cloud experience (such as AWS or Azure).

Experience with Splunk, Sensu or ELK.

Strong Linux system architecture experience (Red Hat/Centos/Debian/Ubuntu)

Excellent analytical and creative problem solving skills.

Independent / go - getter attitude to keep yourself well-versed with new evolving technologies in the DevOps landscape and be able to apply this knowledge to improve the software we are developing now or in future.
```

关键词：CI/CD、容器编排、云、PaaS、日志/监控、方案解决。
这个职位更像一个方案解决架构师 + DevOps 的角色，这个招聘传达的需求十分明确，要有架构的知识，持续改进持续交付意识，以及解决问题的能力，而弱化了对传统运维（日志/监控）的技能的要求。

* **ThoughtWorks：**

```
Title: Infrastructure Developer (DevOps Champion)

Experience with infrastructure as code / provisioning tools like Chef, Puppet or Ansible

Experience with one or more of the structured major PaaS platforms such as CloudFoundry or OpenShift and understand their benefits and lacks in deploying and end-to-end continuous delivery pipeline

Familiarity with Cloud Native Architecture principles and specific tooling and approaches to support high-availability architectures

Understanding of the pros and cons of the key Continuous Integration tools like Go, TeamCity, Jenkins and Concourse

Knowledge in the administration of application and web servers and servlet containers such as WebSphere, Apache Tomcat, Jetty, Nginx, Mongrel, Passenger, Microsoft ISS, etc.

Extensive experience in scripting languages (golang, Shell, Ruby, Perl, Python, PowerShell, etc) and the SCM Tools like Git(Hub), SVN, Perforce and Mercurial

Linux operating system management, operation and maintenance, and network-related knowledge

Comfort with ambiguity and high level of flexibility

High willingness to travel

```

关键词：基础设施代码、PaaS、Cloud native、CI/CD、Platform、脚本语言（自动化）、Linux、愿意出差。
相比于前两个招聘，因为是咨询公司，看得出对技能的广度要求更多，同时技能偏向于基础设施代码编写，流水线搭建，过程优化，而更加弱化了运维（监控/日志都没有出现）。

以上为挑选出来的比较典型的招聘启事，综合来看，对于 DevOps 这个角色达成一致的能力需求，包含了Linux、网络、CI/CD、自动化。除了一致的部分，国内和美国的招聘又有些不同，国内偏向运维，更加强调自动化；美国的企业对 DevOps 更要求架构能力（推测是因为贯彻了基础设施代码的概念），和过程优化的能力；过程优化的主要目的应该就是加速持续交付以支持业务连续性。

# 综上所述

DevOps 的目的是消除 Dev 与 Ops 之间的部门墙，从而加速软件迭代的反馈周期，进而实现业务连续性；基于这一目的，诞生了许多最佳实践；配合这些最佳实践，又产生了许多工具用于项目管理，自动化，可视化等等。在云服务蓬勃发展的大背景下，又给 DevOps 赋予了基础设施代码化的使命。随着软件开发流程越来越成熟，迭代周期越来越快，对业务代码以外的东西（可以把它叫做软件开发的脚手架）的稳定性和易用性要求越来越高，所以诞生了一批以 Heroku、Cloud foundry、Openshift 为代表 PaaS 平台，慢慢在将 DevOps 的工作产品化，从而达到“零运维”或者“AI运维”的效果。
在漫长的演进史中，DevOps 用灵活的架构和更快的变化帮助企业 IT 响应着市场变化。
所以 DevOps 是什么？我认为 DevOps 并不是独自一人改变了软件设计开发流程，DevOps 更像是技术变革史上传统运维消失前的一朵浪花。

> * 招聘材料来源：猎聘，领英
> * 相关链接：[一句话介绍DevOps是做什么的](https://www.duyidong.com/2016/11/11/%E4%B8%80%E5%8F%A5%E8%AF%9D%E4%BB%8B%E7%BB%8DDevOps%E6%98%AF%E5%81%9A%E4%BB%80%E4%B9%88%E7%9A%84/)