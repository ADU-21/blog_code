---
title: AWS_EC2
date: 2016-09-28 14:53:34
tags: AWS
---

# AWS EC2是什么？

Amazon Elastic Compulte Cloud(EC2)提供了可伸缩的计算资源，使你可以快速开发/部署你的应用，有了它你可以快速起一个应用，配置安全，网络，以及存储。他可以更灵活，更快地适应你对需求。

<!-- more -->
## EC2定义了以下规则
 - **Instance:** 虚拟计算环境
 - **AMI(Amazon Machine Images):** EC2环境的一个预配置
 - **Instance type:** instance的存储，CPU，内存，存储，网络，地位 
 - **Key pair:** 用于安全登录你的instance（Amazon保存piublic key,你需要小心保存自己那份private key）
 - **Instance store volumes:** 临时存储数据，当你stop或者terminate你的机器的时候就会被删除
 - **EBS(Amazon Elastic Block Store)** 长期存储volume的数据
 - **Regions AZ** 给你的资源多个物理地址
 - **Security Group** 防火墙，用于定义通信策略，端口，可通信网段
 - **Tags** 元数据（metadata）可以与资源绑定
 - **(VPCs)virtual private clouds** 虚拟网络，你可以配置用于和你自己的网络通信
 
 AWS提倡的是低费用，只为你用到的服务买单，可伸缩，可测量，帮助你快速将资源和需求对应起来，灵活地run你的软件，让你可以将你的想法更快地实施到市场。
 
## 学习AWS EC2总共分几步：
### basics
 - Instance and AMIs
 - Regions and Availablity Zones
 - Instance Types
 - Tags
 
### Networking and Security
 - Amazon EC2 Key Pairs
 - Security Groups
 - Elastic IP Addresses
 - Amazon EC2 and Amazon VPC
 
### Storage
 - Amazon EBS
 - Instance Store

## 和EC2有关的服务
  - **ELB（Elastic Load Balancing）** 在不同的instance之间自动分发进来的请求
  - **CloudWatch** 实现监视器功能用于监控你的instance
  - **CouldTrail** 监控所有通向EC2的call，包括AWS console commandline tools以及其他服务
 
## 如何操作EC2资源

`CLI` `PowerShell 工具`
AWS提供接口为HTTP或者HTTPs的请求提供响应。可以在 `Amazon EC2 API Reference`里面设置响应（Action）
如果你想使用语言接入，AWS也提供SDK工具

## 付费方式
可以按小时付费（大部分时候我们采用这种方式），也可以先预付一笔钱，然后保留一到三年，这样会比较便宜一些。还有竞价方式，这个不需要太了解。

## Security Best Practice
 - 使用IAM(AWS Identity and Access Management)控制AWS资源的访问权限
 - 设置Security Group用于仅允许信任的设备接入开放的端口
 - 经常review你的Security Group设置，确保你赋予了“最小可用权限”，为不同的网络需求创建不同的SG
 - 禁止密码登录
