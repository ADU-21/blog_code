---
title: AWS Services Overview
date: 2017-02-28 14:55:01
tags: AWS 
---

# AWS Services Overview

最近在准备AWS助理级架构师认证考试，借此机会梳理一下AWS比较核心的几个服务，不会太深入细节，只做简单阐释。
首先关于AWS服务架构：

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

[AWS Storage](https://aws.amazon.com/products/storage/)包含了AWS存储有关的服务

### S3

S3([Simple Storage Service]())

> * 参考资料
> * [http://www.linuxnix.com/amazon-aws-regions-vs-availability-zones-vs-edge-locations-vs-data-centers/](http://www.linuxnix.com/amazon-aws-regions-vs-availability-zones-vs-edge-locations-vs-data-centers/)

