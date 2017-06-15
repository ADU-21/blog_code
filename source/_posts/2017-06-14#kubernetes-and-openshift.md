---
title: PaaS 平台（一） -- Openshift 介绍
date: 2017-06-14 21:53:00
categories: Cloud
tags: 
- DevOps
- PaaS
- Openshift
- Kubernetes
- 平台即服务
---
Openshift 是一个基于 Kubernetes 和 Docker 的 PaaS 平台。

# 历史背景

## 容器和容器云的历史

如下图所示，我整理了一个简单的容器历史年表，蓝色箭头表示容器的发展史，红色箭头表示 Openshift 重大事件。
在 2013年以前，容器还是 OpenVZ，LXC 等虚拟化技术的形态，在 2013年3月，Docker 正式发布开源版本，随即在 Github 中代码提交量盛况空前，打响了容器化技术的第一枪。
在 Docker 发布之后短短数月的时间，Redhat 就开始了基于 Docker 的 PaaS 平台研发([官网](https://www.openshift.org/)标注是在13年6月 Openshift 就已经在基于 Kubernetes 研发)，并在随后发布的 REHL6.5 中集成了对 Docker的支持，标拉开了业界各大厂商竞相支持 Docker 的序幕。次年，云计算三巨头亚马逊、微软、谷歌相继推出了支持 Docker 的云计算品台。
2015年7月，谷歌发布了 Kubernetes 的第一个版本，Kubernetes 脱胎于谷歌内部的大规模集群管理工具 Borg，且参与 Kubernetes 项目的早期贡献者正式参与过 Borg 项目的工程师，所以业界基本认同 Kubernetes 的很多概念和架构“代表了谷歌过去十余年设计、构建和管理大规模容器集群经验”的说法。虽然 Kubernest 在容器调度方面表现出众，但他的功能还元不构成一个 PaaS 平台，于是很多厂商开始基于 Kubernetes 开发自己的 PaaS 平台，其中就有目前在国内创业公司被大量使用的 Ranchard，值得注意的是 Ranchard 是在2016年才开始发布第一个版本，而 Openshift 则早在13年就开始基于 Kubernetes 的开发，但目前看来国内市场对 Ranchard 的接受度是远高于 Openshift 的，就使用体验上看，Ranchard 也确实更简单易用。
今年（2017年）四月份发生了一件引爆容器社区的事，就是 Docker 开源社区版改名为 Moby，此举为 Docker 公司的商业行为，在此不表态，不赘述。
截止到这边文章发出为止，目前 Moby 的最新版本为 17.3.2(Docker 的版本从1.2之后变更了命名方式直接跳到了17)，Kubernetes 为1.7.0(基本与 Docker 更新保持同步) ，Openshift 则为 3.6.0。

![](/images/container_timeline.png)

这里要说明一下 Openshift 目前有四个版本，分别为公有云版（类似于AWS）、企业版、企业定制版（保证专属硬件）、社区版本，笔者试用版本为社区版。社区版中有一个分支应用叫 minishift，是一个基于虚拟机的单节点 Openshift 集群，基本和公有云版功能一致，在 16G 的 Mac 上跑起来完全无压力。但还是更推荐直接使用```oc cluster up```，用容器的方式启动更简单也更轻量，也是目前官方推荐的做法。

![](/images/openshift_version.png)
<center>**图片来源：**<https://docs.openshift.com/></center>

## 什么是 PaaS

PaaS（Platform as a Service，平台即服务）最早是在云计算领域被提出。如下图所示，将企业IT服务分为九层，传统自建数据中心九层设施都需要企业自己维护，成本极高。而云计算架构就相当于吧九层架构中的底层一部分外包给云计算服务提供商，根据外包的层次不同，分为IaaS(Infrastructure as a Service，基础设施即服务)，PaaS，SaaS（Software as a Service）三层。

- IaaS 层为基础设施运维人员服务、提供计算、存储、网络以及其他硬件资源，云平台使用者可以在上面部署和运行包括操作系统和应用程序在内的任意软件，无需再为基础设施的管理而分心。
- PaaS 层为应用开发人员服务，提供支撑应用运行所需的软件运行时环境，相关的工具与服务，如数据库服务、日志服务、监控服务等，让应用开发者可以专注于交付业务价值的代码而无需关心应用所需脚手架。
- SaaS 层为一般用户（最终用户）服务，提供了一套完整可用的软件系统，让一般用户无需关心技术细节，只需通过浏览器、应用客户端的方式就能使用部署在云上的应用服务。SaaS 产品比如客户关系管理系统、邮件、虚拟桌面、通信、游戏等。

![](/images/IaaS_PaaS_SaaS.png)
<center>**图片来源：**<https://mycloudblog7.wordpress.com/2013/06/19/who-manages-cloud-iaas-paas-and-saas-services/></center>

这里要更正一个误区，有人可能认为 AWS 是一个 PaaS 服务，那我的项目全部都在 AWS 上，我是不是就已经有了一个 PaaS 平台？
答案是否定的。在一些 AWS 的使用场景中，租户往往只是用了它的 IaaS 级服务，比如 EC2 instance，出于也无需要或灵活性的考虑，租户需要自己管理操作系统以上的环境，诸如安装 Tomcat, Apache 等服务，这些工作由一些自动化脚本来完成。除此之外你或许还有一套搭建在 Jenkins 上的持续交付系统，有一个搭建在 Nexus 服务器上的版本管理系统，这些所有目前由你自己管理的组件，才真正构成了一个 PaaS 平台。
企业在自己管理 PaaS 平台的时候是会有很多痛点的，比如跨云环境不兼容各云平台厂商的基础设施，比如持续集成服务器不稳定，比如自动化配置脚本难管理。如此种种，给企业数字化平台的管理造成成本，平台成为承载业务的开发的瓶颈，企业产生了选用一套第三方 PaaS 平台的需求，这也是为什么今天我们来讲 Openshift。

## Openshift 对比 Cloud Foundry

Cloud Foundry 是一个常被用于和 Openshift 对比的产品，前者由 Pivotal 公司开发(大名鼎鼎的 Springboot，SpringCloud就是他们家的)，后者由 Redhat 开发。很难用一两句话说清两个 PaaS 孰优孰劣，但就我个人的使用体验而言，Cloud Foundry 自动化程度更高一些，而 Openshift 可定制化程度也更高，Openshift 上的应用甚至不需要是一个严格的[十二因素应用](https://12factor.net/)，这也就意味着可以将任何类型的应用迁移到 Openshift 上。

## Openshift 架构概览

Openshift 实际上由三部分组成，核心部分实现容器的调度是封装的 Kubernetes， 除此之外还有一个内置的镜像仓库（Image Registry），这个仓库是可选的，Openshift 也可以配置使用 Dockerhub 或者企业自己的镜像仓库，最外层部分是一个友好的 Web 界面，用于展示和操作 Openshift 的资源。
如下图所示，Openshift 要成为一个完整的数字化平台需要依赖于两个外部系统，一个代码库，一个是持续集成服务，事实上这两个外部服务也是可以跑在 Openshift 里面的。右边的灰色矩形就是 Openshift 的主要架构了，它的上层是一个路由（Router），用于 DNS 解析和转发，确保用户能够调用到 Openshift 集群中的服务。红色的部分是跑在 RHEL 操作系统上的 Kubernetes 集群，侧面是外部存储服务，因为集群里的计算单元是漂浮的，所以通常 Kubernetes 集群只提供计算能力，数据持久外需要依赖外部的比如说 S3，EBS 等云服务商提供的存储服务。最下层同样也是由云服务商提供的基础设施服务。

![](/images/openshift_infrastracture.png)

以上为 Openshift 的一个简单的认识，下一部分我会讲解 Kubernetes 引入的一些新概念，这些概念在 Openshift 中也被复用到，理解这些概念有助于理解 Openshift 的架构和流程设计。

> * 参考资料：《Dokcer 容器与容器云》浙江大学 SEL 实验室著
