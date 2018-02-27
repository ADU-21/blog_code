---
title: 在 AWS 流水线中使用 CodeDeploy
date: 2018-02-26 22:24:39
categories: Amazon Web Services
tags:
- Pipeline
- AWS
- Cloud
- 工具
- 小计
---

AWS 为开发者提供了 CodeCommit -> CodeBuild -> CodeDeploy 三个服务分别用于管理应用的代码库、构建、部署三个环节，并使用 CodePipline 编排流水线，以及使用 CodeStar 以快速构建模板项目。

![](/images/aws-pipeline.png)

# AWS 上的流水线

CodeCommit 是一个私有的 git 代码版本管理库，CodeBuild 是一个基于 Docker 的构建环境，可以用一个叫```buildspec.yml```的 Yaml 配置文件定义构建规则（其实跟 Bash 脚本没啥区别），CodePipeline 是用于串联各个 Stage，这些都没啥新鲜的，对 AWS 而言，值得一书的恐怕还属 CodeDeploy。

# CodeDeploy 介绍

CodeDeploy 在产品生命周期中掌管部署环节，支持 EC2 和 Lambda，亮点是和 AWS 其他服务高度集成，提供零宕机部署（包括蓝绿部署）。CodeDeploy 的规则定义起来比较简单，还可以和 Puppet/Chef 集成，但是需要对 AWS 的基础服务比如 EC2、ASG、ELB、S3 比较了解，上手难度并不大。本文主要解释基于 EC2 的 CodeDeploy 使用。

## 概念解释

AWS  CodeDedeploy 定义了一些资源名称：

- **Application:** 一个应用，也对应项目里一个 Project，一般对应一个代码仓库，包含多个环境。可以定义多个**Deployment Groups**。
- **Deployment Groups:** 对应应用的一个环境，包含了 instance 选区规则（Tag/ASG）、ELB、触发规则、Rollback 策略、权限（Role）以及 **Deployment Configuration**。
- **Deployment Configuration:** 部署策略，有 AllAtOnce、HalfAtATime、OneAtATime 三种默认策略，也支持自己定制，即指定在部署过程中一个 Deployment Groups 至少有多少个（百分比）的 instance 要在服务中。
- **Revision:**一个新版本及为一个 Revision，需要提供 Repo（S3/Github）、Deployment Configuration、Rollback 策略等信息。

## 触发 

CodeDeploy 的对象可以来自 S3 或者 Github，和所有的 AWS 资源对象操作一样，Trigger 方式可以选择 Console、cli、或是 sdk。 

### 手动触发

触发相关的设置在 **Deployment Groups** 中，选择**Deploy new revision**，这里有两个可选方式：

- S3 object + ETag: 被部署的文件必须被打包为一个文件（```.zip```或是 ```.tar```）并上传到 S3 上，ETag 是该文件的一个哈希，会随文件变更改变，因此 Revision 里面实际上带了文件位置和版本号两个信息。
- Github repository + commit ID: 与 S3 一样，信息需要定位到 Repo + 版本号，不同的是 Github 不需要把部署文件和部署脚本一起打包，另外第一次使用这个功能需要 Github 授权给 AWS CodeDeploy。

### 命令行 Deploy to S3 触发

AWS CLI 有一个 Deploy 的 namespace 可以用来触发 Application Deploy，对于 S3 参数如下：

```
aws deploy register-application-revision --application-name DemoApp --s3-location bundleType=tar,eTag=0d088076b9baa9bd71369367119e6b33,bucket=yidong-codedeploy,key=demoRevision.tar
```

Github 如下：

```
aws deploy register-application-revision —application-name DemoApp —github-location commitID=<value>,repository=adu-21/aws-codedeploy
```

如果被部署文件在本地而还没有提交到 S3 可以使用 ```aws deploy publish```一键打包、上传、发布：

```
aws deploy push --application-name DemoApp --ignore-hidden-files --s3-location s3://yidong-codedeploy/newDemoRevision.zip --source .
```

### Github 提交自动触发

Github 的集成可以做到部署文件提交到 Github 上就有一个 Webhook 到 CodeDeploy，并 Pull 最新版代码到 instance 上，需要给 CodeDeploy 配置 Github 账户 ```repo_deployment```权限的 **access token**，并在 Repo 的 **Services** 中加入 AWS CodeDeploy 和 Github Auto-deployment 两个服务，具体可见：<https://github.com/ADU-21/aws-codedeploy/settings/installations>：

![](/images/aws-codedeploy-setting.png)

## 执行

Deploy 执行的逻辑写在被部署文件根目录的一个 ```appspec.yml```文件中，参见：<https://github.com/ADU-21/aws-codedeploy/blob/master/appspec.yml>:

```
version: 0.0                              # 版本号
os: linux                                 # 操作系统，只有 linux 和 windows 两个可选项

files:                                    # 定义某个本地文件拷贝到目标服务器的某个地址
  - source: files/index.html
    destination: /var/www/html

permissions:                              # 目标服务器上文件权限
  - object: /var/www/html
    pattern: index.html
    owner: apache
    group: apache
    mode: 644
    type:
      - file

hooks:                                     # 定义部署前后的一系列流程，通常调用本地完成
  BeforeInstall:
    - location: scripts/webserver-stop.sh
    - location: scripts/delete-old-index.sh
  AfterInstall:
    - location: scripts/sleep.sh
    - location: scripts/webserver-start.sh
```

对于 Hook 模块，有 ELB 的和没有 ELB 的有以下可选子模块：

![](https://docs.aws.amazon.com/codedeploy/latest/userguide/images/lifecycle-event-order-in-place.png)

<center>**图片来自：[官方文档](https://docs.aws.amazon.com/codedeploy/latest/userguide/reference-appspec-file-structure-hooks.html)**</center>

## 蓝绿部署

AWS CodeDeploy 提供配置简单的 Blue/Green Deploy 服务，只需要配置 ELB 和 ASG 就可以完成自动创建新环境、Switch ELB、Terminate 旧环境几个步骤（如下图），完整步骤详见[官方博客](https://aws.amazon.com/blogs/devops/performing-bluegreen-deployments-with-aws-codedeploy-and-auto-scaling-groups/)。

![](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2017/04/19/codedeployresults.png)

<center>图片来自：[Performing Blue/Green Deployments with AWS CodeDeploy and Auto Scaling Groups](https://aws.amazon.com/blogs/devops/performing-bluegreen-deployments-with-aws-codedeploy-and-auto-scaling-groups/)</center>

# 后记

今年 AWS 发布财报营收已经被微软超越。亚马逊的产品不得不说性能稳定性强大，但是易用性和商业模式上仍不及国内阿里、腾讯的产品，甚至不如 Rancher、Hashicorp 这样一些小公司的产品简洁、易上手。过去的一年在各种 PaaS 的竞争中 AWS 逐渐面临被 IaaS 化的命运，投资数年的 Serverless 因为它的不稳定、不成熟和平台绑定，在过去的几年中鲜有公司真正涉足，大多在观望状态。就我个人的使用体验而言，Serverless 的架构在较复杂的业务场景下对架构会带来非常大的挑战，资费优势也被 AWS 抖机灵的收费方式蚕食殆尽，17 年 AWS 收购 Cloud9 及迅速提升 Developer Tools 板块服务的行为就是想要补足 PaaS 这块短板。

我一直觉得像 FLAG（Facebook，LinkedIn，Amazon，Google）这种大公司，在如此体量的前提下还面临大量的服务更新和创新需求，还要求内部协作和资源共享，在多年的摸索中已经有了一套很好用的内部工具链，做 PaaS 只取决于你要如何以及以何种方式把这些工具公开出来。互联网的普及越来越广，企业越来越需要平台来支撑可持续、快速的迭代能力，大企业基于开源项目自建平台，小公司就向大公司租用 PaaS 服务。对讲求效率的小公司而言，一切非业务核心的技术能买则买，能外包就外包，能花钱的解决的问题，决不要花时间。

PaaS 仍然是很有潜力的一片蓝海，今年可以看到以阿里云效为代表的 DevOps 平台已经在发力，未来必然可以看到更加成熟的产品。