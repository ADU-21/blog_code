---
title: AWS Services Overview
date: 2017-02-28 14:55:01
tags: AWS 
---

# AWS Services Overview

最近在准备AWS助理级架构师认证考试，借此机会梳理一下AWS比较核心的几个服务，不会太深入细节，只做简单阐释。
首先关于AWS服务一览：

![](/images/AWS_Services_Overview.png)

以下服务会出现在助理级架构师考试中：

![](images/AWS_Services_Exam.png)

下面就按照这个顺序过一下AWS的众多服务。

## AWS Global Infrastructure

AWS拥有全球化的基础设施让你的服务可以在瞬间扩展到全球，你可以在[这里](https://aws.amazon.com/about-aws/global-infrastructure/)查看亚马逊当前拥有的全球基础设施信息。
截止到今天（2017.02.28）AWS Cloud在全球共有**42**个```Availablity Zone```和**16**个```geographic Regions```，中国区有2个Region分别在北京和宁夏（宁夏区暂未开放），每个Region各有两个AZ，受政策限制，AWS中国区与其他各地区服务分离，由[光环新网](http://www.sinnet.com.cn/home/product/indexyun/16)代理，需要单独[申请账号](https://www.amazonaws.cn/en/sign-up/)。

![](https://d0.awsstatic.com/global-infrastructure/Global_Infrastructure_12-15-2016.png)

### Region & Availablity Zone

Region是AWS按物理地理划分的节点，每个Region包含两个或以上AZ，AZ可以理解为同在一个Region的多个数据中心。之所以一个Region要有至少两个Region是因为AWS官方推荐的网络结构至少包含一个public Subnet，一个 private Subnet，而Subnet和AZ是一一对应关系，一个健康的网络至少应有两个子网，为保证子网之间的网络隔离而在每个Region至少有两个AZ，且AWS很多服务都推荐跨AZ备份，对于重要数据甚至需要跨Region备份，这也是为什么每个Region需要多个AZ。
另外AZ之间网络通信延迟远大于Region之前通信，原因也很好理解，Region的物理距离很远，而同Region的AZ是在同一个城市。

### Edge Location

Edge Location是比亚马逊数据中心覆盖面还广的服务，我们访问AWS console就使用过Edge Location，同时Edge Location还作为AWS Cloudfront（CDN）服务的节点，你可以在[这里](https://www.google.com/maps/d/viewer?msa=0&mid=z8tAG_iMI0RE.kBLn8p8h4GGw)看到AWS服务在全球分布的情况，在[这里](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/#AWS Edge Network Locations)查看AWS的Edge Location分部情况。

## Networking & Content Deivery

这一部分的内容主要包含网络和CDN。

### VPC

VPC([Virtual Private Cloud](https://aws.amazon.com/vpc/))，AWS服务中关于网络最重要的一个服务，作用是在AWS中虚拟一个定制化局域网，从而可以像管理一个虚拟数据中心一样管理AWS中的资源。一个Region最多可以有5个VPC，你可以自由定义VPC的局域网网段（当然要使用专用网段），每个VPC之间可以单点连通前提是专用网段不重复。
VPC就是一个虚拟的局域网，可以通过给VPC绑定InternetGateway的方式让VPC成为public VPC，即在外网可以访问VPC内的服务。就好比公司网络中加了一个从内网出去的路由。当然要让外网的设备找到VPC中的资源还需要给VPC中的资源绑定一个公网IP。

### Route53

[Route53](https://aws.amazon.com/route53/)是AWS的DNS服务。可以注册DNS，配置A地址和CNAME。

### Cloud Front

[Cloud Front](https://aws.amazon.com/cloudfront/)是亚马逊的CDN（Content Delivery Network）服务，用于缓存下载或上传的数据。

### Direct Connect

[Direct Connect](https://aws.amazon.com/directconnect/)是亚马逊的网络直连服务，就是用一跟又粗又长的网线，从你的数据中心翻山越岭地连接到AWS的数据中心从而建立一个稳定可靠高效的私有连接。

## Compute

[AWS Compute](https://aws.amazon.com/products/compute/)包含了亚马逊最重要的服务——计算服务

### EC2

EC2([Elastic Compute Cloud](https://aws.amazon.com/ec2/))可以理解为AWS的虚拟机，包含存储（EBS），镜像（AMI），备份（snapshot），防火墙（Security Group）等紧密相关的服务，AWS的一部分服务也是基于EC2的，比如ECS和RDS。
使用EC2你可以按照你的需要创建Instance，不仅可以自由控制Instance的数量，还可以动态调整虚拟机的各种参数，比如内存，CPU。最重要的是，使用AWS EC2你可以——按需付费。

### EC2 Contianer Service

ECS([EC2 Contianer Service](https://aws.amazon.com/ecs/))帮助管理在EC2上运行Docker或容器集群，适用于构建微服务。

> 注：该服务不会出现在SAA考试中

### Elastic Beanstalk

[Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)是一个用于方便部署web服务的一个傻瓜式应用，你不需要知道关于AWS的一切，只需要上传代码，Beanstalk会帮你选择合适的环境，运行环境，负载均衡，监控等等，目前支持语言包括 Java, .NET, PHP, Node.js, Python, Ruby, Go。

> 注：该服务不会出现在SAA考试中，只需要知道Beanstalk是什么就可以。

### Lambda

[Lambda](https://aws.amazon.com/lambda/)是一个云计算革命性的服务，关键词**Serverless**，也是AWS这几年大力在推的服务，主要特点是除了功能性的function以外的所有服务（操作系统，物理硬件，运行环境）都由AWS管理，资源只在被调用时创建并在使用完后立即销毁，最大限度地做到了高可用高伸缩，同时非常便宜，按照访问次数收费。（每百万次0.05美刀）
主要使用场景是流处理，应用后端。

> 注：因为是比较新的服务，所以暂时还没有在SAA考试或样题中出现，但毫无疑问这是AWS一个重要的服务。

### Lightsail

[Lightsail](https://amazonlightsail.com/)与Elastic Beanstalk类似，也是一个给不会使用AWS的用户提供自动化部署的一个服务。

> 注：该服务不算是AWS的核心服务，仅作为了解，考试内容不会涉及。

## Storage

[AWS Storage](https://aws.amazon.com/products/storage/)包含了AWS存储有关的服务，这里暂不列举EBS。

### S3

S3([Simple Storage Service](https://aws.amazon.com/s3/)) 差不多是和AWS一样老的一个服务，同时支撑着AWS中很多其他的服务。
S3是一个高可用的对象存储服务，可以理解为一个巨大的磁盘可以存放图片，视频一类的大小静态文件，其存储持久性可达到11个9，可用性可以达到4个9。S3的对象存储单个文件最大可达5TB，总大小不设上限。同时S3可以作为静态网站使用，可以开启版本管理。本博客也准备迁移到AWS S3上。
S3的使用场景可以用作静态网站部署，数据存储（例如数据胡泊），用于数据分析，数据备份等。当然也可以用来构建Serverless服务。

### Clacier 

[Claier](https://aws.amazon.com/glacier/)是用于存档数据长期保存的一个服务，如果你有长时间不使用但是需要保存的数据，例如医院的病例，电视台的历史影像资料，你可以使用这个服务，其主要特点是异常便宜（0.004美刀/GB），同时能做到11个9的持久度。另外归档数据在提取时大概需要4-5个小时，所以不适宜存储需要频繁读写的数据。

### EFS

EFS([Elastic File System](https://aws.amazon.com/efs/))是一个可移动的块存储设备，你可以想象成一个可以任意扩容的移动硬盘，在需要时被接到任何一台EC2 instance上，同时它还具有nfs的功能，可以同时连接多台设备。EFS在刚创建的时候大小为零，随着存放资源增多逐渐扩容，真正做到按需付费，缺点是小贵，大概0.3美刀/GB

### Storage Gateway

[Storage Gateway](https://aws.amazon.com/storagegateway/)是一个让你的数据中心可以和AWS存储设备无缝对接的服务，你可以像访问本地文件系统一样访问你存储在AWS云上的资源。

> 注：该服务在Ops认证会被重点考察，SAA涉及不多。

## Databases

[AWS Cloud Database](https://aws.amazon.com/products/databases)提供了一系列由AWS帮你管理的数据库服务。
涉及到考试，重点在针对不同的使用场景选择合适的数据库服务，另外非关系型数据库的高性能高伸缩和关系型数据库如何做到高可用，读写分离也常会作为考点。

### RDS

RDS([Relation Database Serveice](https://aws.amazon.com/rds/))是一个托管在AWS上的关系型数据库，目前支持MySQL，Postgresql，SQLserver，MariaDB，Oracle和一个亚马逊自己出的Aurora（据说很屌）。

### DynamoDB

[DynamoDB](https://aws.amazon.com/dynamodb/)是AWS提供的非关系型数据库，特点是高一致，低延迟，极易伸缩，相当灵活。

> 注：该服务在Developor认证考试中重点考察，SAA认证也有涉及。

### Redshift

[Redshift](https://aws.amazon.com/redshift/)是托管在AWS上的数据仓库，提供PB级的存储服务。

### Elasticache

[Elasticache](https://aws.amazon.com/elasticache/)AWS上一个高吞吐的缓存服务，有Redis和Memcached两种类型。

## Migration

### Snowball

AWS官方提供的物理迁移，小型的是一个手提箱，大型的有卡车大小的移动存储，帮助你将大量数据加密传输到亚马逊的云上。
关于Snowball一个重要的概念就是Import和Export，你可以从S3导出或导入数据到Snowball。

### DMS

DMS([Database Migration Service](https://aws.amazon.com/dms/))帮助你将本地数据库中的数据迁移到AWS上，同时还支持在AWS中不同类型的数据库之间做零宕机的数据迁移。比如从本地的Oracle中将数据迁移到AWS的Aurora上。

> 注：DMS是AWS中一个重要的服务，但暂未出现在SAA的考试中。

### SMS

SMS([Server Migration Service](https://aws.amazon.com/server-migration-service/))AWS服务器迁移服务是一种无代理服务，使租户能够快速将本地服务增量式地迁移到云上，具体就是将本地服务器的卷打成镜像放到云上再跑起来。

> 注：该服务暂时没有在所有考试中出现。

## Analytics

[Analytics](https://aws.amazon.com/products/analytics/)主要用于处理，分析，可视化数据。

### Athena

[Athena](https://aws.amazon.com/athena/)让租户可以使用SQL交互式地查询S3中的数据，Athena是Serverless的，所以用户只需按使用次数付费。

### EMR

Elastic MapReduce，就是托管在AWS上的map reduce，用于管理大数据集群（EC2 instance集群）

> 注：SAA考试会涉及。

### Cloud Search & Elastic Search

用于构建站内搜索或应用内搜索。

> 注：助理级考试均不涉及。

### Kinesis

[Kinesis](https://aws.amazon.com/kinesis/)强大的实时数据流处理服务，速度可达TB每小时，用于上传，分析实时数据流。比较重要。

### Data Pipeline

提供有序处理数据，洗数据，数据迁移的工作。

### Quick Sight

为你的数据创建可视化面板。

## Security & Identity

### IAM

IAM([Identity & Access Management](https://aws.amazon.com/iam/))是AWS的权限管理服务，是一个非常重要的服务。这个服务是免费的，而且Global，即不收Region限制的。
关于IAM主要需要知道关于User Group, User, Role, Polic几个概念即可；User Group和User都类似于Linux中的用户组和用户的概念，不同的是AWS根据Polic赋予用户权限，关于Polic，主要就是规定可以对“何种资源”进行“何种操作”两个参数，Role的定义类似于User，但是Role不是赋予用户使用，而是赋予给AWS的资源的，例如租户需要创建一个EC2 instance，这个Instance需要操纵S3的资源，那么你需要在创建这个EC2 instance的时候赋予一个带S3操作权限的Role，需要注意的是Role只能在创建资源的时候被赋予，且不能更改不能解除绑定，另外在包含Role的instance中你无法看到AWS ACCESS KEY 之类的文本文件，这是AWS出于安全的设计。

### Inspector

[Inspector](https://aws.amazon.com/inspector/)是一个安装在EC2 instance上的客户端，用于生成安全报告。

> 注：暂不在考试中出现

### Certificate Manager

[Certificate Manager](https://aws.amazon.com/certificate-manager/)是一个用于管理SSL证书的服务。

### Directory Service

[Directory Service](https://aws.amazon.com/directoryservice/)让租户可以将AWS权限管理和自建数据中的AD group集合在一起，可以直接使用Microsoft AD Group创建AWS中的资源。


### WAF

WAF([Web Application Firewall](https://aws.amazon.com/certificate-manager.com/waf/))给予租户应用级别的安全防护，比如SQL注入，跨站攻击，等。

### Artifact

这是让你能访问到AWS compliance document 的服务，可以从中获取各种合规文件。

## Management Tools

### Could Watch

Cloud Watch 是AWS重要的Ops工具，作用为监控AWS资源状态的工具，可以设置连个级别，分别是五分钟一个心跳（默认）和一分钟一个心跳。该服务监控类目(Metrics)繁多，甚至包括花费的监控预警，可以设置Alarm触发SNS给到租户通知实现报警的功能。 

### Cloud Formation

Cloud Formation是AWS一个重要的DevOps工具，实践了基础设施及代码，租户可以使用一套模板（Template），即一个Json或者Yaml的文件定义整个AWS服务的使用，包括资源，网络，权限。是一个可以投入大量精力使用学习的一个服务。

> 注：Cloud Formation是一个重要的服务，但不会在SAA考试中出现很多，只需要一定程度的了解即可，但想成为一个真正的架构师，这个服务必须要熟悉。

### Cloud Trail

用于监控AWS API调用的一个服务，哪些操作属于AWS API调用——创建一个资源，改变一个资源的配置，销毁一个资源，往S3上传一个对象，都算。这些操作被Cloud Trail记录下来保存在S3中作为log供租户使用，AWS的一些边缘服务也有依赖于Cloud Trail的。但笔者在使用该服务时发现其存在延迟时间长（10-20min），无法划分日志级别，且日志冗余不易分析等缺点，仍不是很好用。

### Opsworks

AWS提供的有一个基础设施及代码的工具，使用[Chef](https://www.chef.io/chef/)。

### Config

[Config](https://aws.amazon.com/waf/)是用于记录资源配置规则变更，并判断资源配置是否合规的一个服务。

> 注：该服务不会出现在助理级考试中，但会在Security的考察中出现。

### Service Catalog & Trusted Advisor

Trusted Advisor是一个有意思的服务，从安全，省钱，性能，容错四个角度给你的AWS资源配给一个全面的分析，这四个角度也是AWS架构师设计一个良好的架构必须要考虑的四个角度。且该服务与AWS收费等级强相关，AWS的服务等级分为基础版、开发版、商业版、企业版四个级别，交的钱不同会高度体现在这个服务的内容上。[https://aws.amazon.com/premiumsupport/compare-plans/](https://aws.amazon.com/premiumsupport/compare-plans/)

## Application Services

### Step Functions

[Step Functions](https://aws.amazon.com/step-functions/)是在2017年初才推出的一个新服务，可用于执行批处理脚本。

> 注：因为该服务还很新，暂未出现在认证考试中

### SWF

SWF([Simple Workflow Service](https://aws.amazon.com/swf/))让开发者可以按顺序执行一些后台命令，如果您的应用程序的步骤需要超过500毫秒的时间来完成，您需要跟踪处理状态，如果任务失败，您需要恢复或重试，Amazon SWF可以做到。

> 注：这个服务比较重要，需要动手实践。

### API Gateway

API Gateway是AWS一个Serverless有关的重要的服务，常用于触发Lambda function。

> 注：这个服务在构建无服务架构的时候非常重要，但不会在考试中涉及很多细节，只需要了解其作用，用法即可。


### Elasic Transcoder

用于视频转码。

## Message

消息服务是AWS一个非常重要的服务，我们可以通过SNS(Simple Noticfication Service)发送短信，邮件提示，使用SQS（Simple Queue Service）构建一个保证交付的消息队列，使用SES(Simple Email Service)收发邮件。

## Desktop & App Streaming

### workspace

一个在AWS云端的远程办公桌面。

### AppStream2.0

[AppStream](https://aws.amazon.com/appstream2/)可以将桌面应用程序流式传输到运行Web浏览器的任何设备，安全地从任何地方即时访问桌面应用程序，如AWS console。


> * 参考资料
> * [http://www.linuxnix.com/amazon-aws-regions-vs-availability-zones-vs-edge-locations-vs-data-centers/](http://www.linuxnix.com/amazon-aws-regions-vs-availability-zones-vs-edge-locations-vs-data-centers/)

