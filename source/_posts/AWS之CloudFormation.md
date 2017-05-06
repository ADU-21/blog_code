---
title: AWS之CloudFormation
date: 2016-08-02 21:36:04
categories:
- Amazon Web Services
tags: [AWS, DevOps, 基础设施即代码]

---

## CloudFormation简介
通过前面的学习我们已经知道了AWS为我们提供服务的方式是将现实世界中的各种需求抽象为很多服务，CloudFormation是一种被定义为管理工具的服务，其作用是把AWS提供的服务按一定方式组合起来，自动化而且可纳入版本管理，作为基础设施及代码的典型应用

<!-- more -->
# 应用场景
CloudFormation从小到一个EC2大到一个企业级web服务都可以用一个cloudfomation自动生成。

# 如何工作
使用CloudFormation意味着使用模板（template）创建stack。
一个模板可以包含一个或者多个资源的信息，并且包含资源之间的关联关系。
当你加载一个CloudFormation，你需要一定的权限，这个权限将用于创建指定的资源。

![](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/images/create-stack-diagram.png)
