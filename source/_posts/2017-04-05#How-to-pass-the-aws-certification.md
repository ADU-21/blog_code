---
title: AWS助理架构师认证考经
date: 2017-04-05 14:48:45
tags:
 - AWS
 - 云计算认证
 - Certification
 - AWS认证考试
---

上周去参加了AWS助理级架构师的考试并顺利通过，是2017年的第一个小目标也是对云服务知识的一个检验，在此分享一下考试的经过和经验。

# 什么是AWS认证

亚马逊作为云计算领域的领头羊，在云计算领域已经有了十多年的积累，亚马逊的云计算不论是从服务还是性能在全球都是首屈一指的。近年来国内有很多创业公司依托于阿里云，微软云，Ucloud等IaaS, PaaS服务构建了许多SaaS服务，其中不乏客如云之类的成功案例，许多大型企业也将自己的基础设施往云上迁移。近两个月腾讯云和中国电信两次以0.01元中标政府云服务项目更是起到了推波助澜的作用。如此种种，可见云服务在IT届的地位已经越来越高，对于从业者的云计算知识要求也必然会越来越高。那么考取云服务界领头羊亚马逊的认证也会变得越来越有价值。
AWS目前仅有助理级和专家级两个级别，2017年还会加入大数据，安全，网络三个认证。
就目前有的认证而言，开发者(Developer)认证应该是最好考的，助理方案解决架构师(Solutions Architect)次之，助理级最难的是运维（SysOps）。要取得专家级认证必须先取得助理级认证，关系如图：

![](/images/AWS_Certification_Level.png)

关于八个认证的难以程度即考取顺序建议如下：

![](/images/AWS_Certification_Easy_to_Hard.png)

笔者选择先考助理架构师，意在先建立一个比较全面和高视角的认识，再考Developer和Sysops也会比较容易。
AWS认证是检测自己云计算领域知识最好的方式，同时也是增加求职筹码的一个不错的选择。

# 我的考试经历

## 背景

笔者在准备考试前有一些AWS的使用经历，大部分在项目中使用，但服务涉及很窄，基本只限于创建instance，明白不同Subnet意味着不同网段。
为公司项目做过AWS的虚拟机方案预算，基本知道Instance Type以及Cost有关的一些东西。
从开始准备到正式考试大概两个月，每天投入3-4小时，主要花费在阅读文档和动手练习，另外还买了[acloud.guru](https://acloud.guru)上的一个课程，价格29美刀，没有购买AWS的练习题。

## 考前准备

 - 参考AWS官方[考试介绍](https://aws.amazon.com/certification/certification-prep/)定位考试的大致方向
 - 仔细阅读[考试大纲](https://awstrainingandcertification.s3.amazonaws.com/production/AWS_certified_solutions_architect_associate_blueprint.pdf)，了解考试设计到的知识点和大纲
 - 根据AWS的[官方文档](http://aws.amazon.com/cn/documentation/)梳理了[AWS现有的重要服务](https://www.duyidong.com/2017/02/28/AWS-Services-Overview/)
 - 认真过了一遍关于[EC2](https://www.duyidong.com/2017/03/15/AWS-EC2/)，[IAM](https://www.duyidong.com/2017/03/06/%E6%B5%85%E8%B0%88AWS-IAM/)，[S3](https://www.duyidong.com/2017/03/13/AWS-S3/)有关的文档，这几个服务加上VPC和RDS为考察的重点，同时也是AWS的核心服务
 - 在AWS上做了一些操作练习，主要包括：
   - 在AWS上搭建了public & private subnet的[典型网络结构](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Scenario2.html)
   - 在AWS上搭建了一个ELB+Autoscaling的高可用的网站
   - [将自己的博客迁移到AWS上](https://www.duyidong.com/2017/03/07/Deploy-Hexo-to-S3/)，并[使用Cloudfront做CDN和Route53做DNS解析](https://www.duyidong.com/2017/03/20/Enable-HTTPS-and-CDN-with-Cloudfront/)
 - 仔细大量地阅读了[FAQ](https://aws.amazon.com/faqs/)
 - 看了一下AWS的几个[白皮书](https://aws.amazon.com/whitepapers/)，包括[AWS服务概览](https://d0.awsstatic.com/whitepapers/aws-overview.pdf)，[关于架构](https://d0.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)，[安全最佳实践](https://d0.awsstatic.com/whitepapers/Security/AWS_Security_Best_Practices.pdf)，[AWS最佳实践](https://d0.awsstatic.com/whitepapers/AWS_Cloud_Best_Practices.pdf)，主要认真理解了[什么样的架构是一个良好的架构](https://www.duyidong.com/2017/03/23/AWS-well-architected-framework/)
 - 做了大约两百道练习题，事实在上考试的时候有百分之二十的都是在见过的原题。

## 考试

考试首先要在网上注册预约：[https://www.webassessor.com](https://www.webassessor.com)，需要visa卡支付，并提供公司名称，地址等信息。
考试地点在[成都上普财富中心2514鸿景国际教育](https://www.google.com/maps/place/Shangpu+Fortune+Center+Plaza/@30.6525977,104.0673653,17z/data=!3m1!4b1!4m5!3m4!1s0x36efc53df2a6a879:0x5c54ecee2ff20a3c!8m2!3d30.6525977!4d104.069554)，锦江宾馆地铁站出站走几步就是，是一家代理考试点，每次同时只有一个人考试，在一个独立的房间，有一位监考员，负责帮你准备考试的环境。考试原则上不允许带手机，使用考点的笔记本电脑，并且是远程连接到美国，所以网络会比较卡顿，经常做题做着做着就白屏了，需要刷新才能继续，有些考验心理素质。
考试是80分钟60道题，据说判断是否过关是根据一段时间考试的人的成绩取排名靠前的百分之六十的人作为比较，达到这个标准就算过关，考试完后立马知道分数。根据以往的经验，一般分数在65分以上可以过关。考试分单选和多选，多选题会有提示有几个选项，每道题可以标记“Mark this item for later review”，系统会在答题完成后给出所有待检查的题号以重新作答，还是比较方便的。
根据考试经验，得分往往会低于做的时候的感觉期望，由于考试时间并不紧张（笔者在完成答题后还剩半个小时交卷），一定要认真检查，最好知道每个题要考察你什么知识，很多题都是有坑的，如果你能把考察点说出来，这道题多半是没有问题的。
考试完后随即你就会在邮箱里收到一封这样的证书：

![](/images/AWS_Certification_SAA.jpeg)

## 总结

AWS方案解决架构师助理级的考试总的来说难度不大，主要考察的就是在何种需求场景下应该选择AWS的何种服务，考试分为设计一个符合[最好架构](https://www.duyidong.com/2017/03/23/AWS-well-architected-framework/)设计原则的系统；工具和开发；安全；以及解决问题四个部分，**设计一个良好的架构**是最重要的也是最能体现云服务意义的，需要好好准备。**工具和开发**考察的是动手操作的能力，一般做够几个实验都没什么问题。**安全**方方面面都有涉及，考察的比较细致，主要是针对不同场景需要使用权限管理，网络管理，服务策略管理等不同方式，配合AWS的[安全最佳实践白皮书](https://d0.awsstatic.com/whitepapers/Security/AWS_Security_Best_Practices.pdf)问题不大。最后一个**解决问题**在AWS文档中很多部分都是Troubleshooting，比如[Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html)，[Troubleshooting IAM](http://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot.html)，[Elastic Load Balancing Connectivity Troubleshooting](https://aws.amazon.com/premiumsupport/knowledge-center/elb-connectivity-troubleshooting/)都是可能被考察的内容，需要阅读文档的时候足够仔细。
另外，给出一些考试资料供参考：

> * AWS测试题：
>  - [https://quizlet.com/152377618/aws-certified-solutions-architect-associate-practice-questions-flash-cards/](https://quizlet.com/152377618/aws-certified-solutions-architect-associate-practice-questions-flash-cards/)
>  - [http://www.aiotestking.com/amazon/category/exam-aws-saa-aws-certified-solutions-architect-associate/](http://www.aiotestking.com/amazon/category/exam-aws-saa-aws-certified-solutions-architect-associate/)
> * 一个关于考试的feedback: [https://acloud.guru/forums/aws-certified-solutions-architect-associate/discussion/-KSDNs4nfg5ikp6yBN9l/exam_feedback_-_20_specific_po](https://acloud.guru/forums/aws-certified-solutions-architect-associate/discussion/-KSDNs4nfg5ikp6yBN9l/exam_feedback_-_20_specific_po)
> * AWS博客：[https://aws.amazon.com/blogs/aws/](https://aws.amazon.com/blogs/aws/)
> * 两篇同事的博客：
>  - [http://www.huangbowen.net/blog/2014/10/27/how-to-pass-the-aws-certification/](http://www.huangbowen.net/blog/2014/10/27/how-to-pass-the-aws-certification/)
>  - [http://www.jianshu.com/p/02233ae66b80](http://www.jianshu.com/p/02233ae66b80)
> * Jayendra的博客：[http://jayendrapatil.com/](http://jayendrapatil.com/)
> * Youtube上deep dive的视频：[https://www.youtube.com/results?search_query=aws+deep+dive](https://www.youtube.com/results?search_query=aws+deep+dive)
> * AWS官方教学小视频： <https://aws.amazon.com/training/intro_series/>
