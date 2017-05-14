---
title: 使用AWS解锁HTTPS和CDN
date: 2017-03-20 20:23:37
categories:
- 我的博客
tags: [AWS, Blog, HTTPS, CDN, Cloudfront, Route53, 博客上云]
---

在上一篇博客中，笔者已经介绍了[将hexo博客发布到S3](https://www.duyidong.com/2017/03/07/Deploy-Hexo-to-S3/)，这一部分我将介绍如何使用AWS的Route53应用上自己的域名，以及如何使用Cloudfront开启HTTPS/HTTP2和CDN加速。

## 关于Route53

Route53是AWS提供的DNS服务，提供常见域名服务，用户可以使用Route53注册使用域名，指向自己的服务，可以除了一般域名服务都支持的A类地址（直接指向IP），及CNAME（指向另一个域名）以外，Route53还支持一个功能叫做A-record，即可以将DNS指向AWS内部的资源，该服务功能与CNAME基本相同，但是免费。此外，Route53支持以下几种路由策略：

 - **Simple Routing Policy**：通常在只有一个资源的时候使用这种策略，该种策略会自由（平均）分配负载给所属资源
 - **Weighted Routing Policy**：加权路由，这种路由策略支持用户指定的路由百分比，比如百分之20的流量导入测试环境，百分之80的流量导入产品环境，用于进行AB测试。
 - **Latency Routing Policy**：该种策略会根据客户端访问的延迟情况选择合适的服务，比如一个客户访问A延迟为20毫秒，访问B服务延迟为250毫秒，那Latency Routing就会把该用户的访问导入A服务。
 - **Failover Routing Policy**：该路由策略在其中一个被路由资源宕机的情况下会停止流量引入，从而避免用户访问到错误页面，这里涉及到Route53的另一个功能———健康检查。
 - **Geolocation Routing Policy**：地理位置策略允许用户设置将不同地理位置的用户流量导入到不同服务，例如希望悉尼的用户和北京的用户在一个网站上看到不同的页面，可以采用这种路由方式。

## 关于Cloudfront

Cloudfront是AWS的CND服务，利用AWS分布在全球的节点服务器（Edge Location）缓存用户的访问，用户在第二次访问（或同区域的另一个用户在非首次访问）页面时会直接从节点服务器取到已经缓存的数据，速度会大大加快。
同理，Cloudfront也可以用于文件上传。

## 申请Route53和Cloudfront权限

默认情况下，所有账户都是没有开启Cloudfront和Route53服务的，这个时候需要使用账号注册邮箱发送邮件到[aws-verification@amazon.com](aws-verification@amazon.com)申请开通。

## 申请域名

得到服务使用权限之后，还需要申请一个域名，这个时候可以在AWS的[Route53](https://console.aws.amazon.com/cloudfront/route53)进行购买，也可以在阿里云的[万网](https://wanwang.aliyun.com/domain/)注册一个域名，域名会按使用年数收费，不同域名收费不同。
在亚马逊购买域名，只需要进入[Console](https://console.aws.amazon.com/route53/home)，在**Register Domain**栏输入你想要的域名，点选Check，接下来如果域名可用，可以Add to Card然后付费即可。大约一小时后，这个域名就可以被使用了。

![](/images/Route53_Register_Domain.png)

## 配置Cloudfront

点击**Create Distribution**按钮，Delivery method选择Web。 Web主要针对一些html，css,js等静态文件，而RTMP则主要是一些音视频文件。点选“Get Start”。

![](/images/Clouldfront_Configure_Select_Delivery_Method.png)

下一步，要选择Origin,即要进行内容分发的源。虽然亚马逊会自动列出你的S3 bucket，但是千万不要选。而是自己手动输入example.com这个Bucket的Endpoint(Endpoint在S3 Console的Properties标签下的Static Website hosting里看得到)。为什么不直接选S3 bucket那?这是因为当我们访问一个目录时，我们期望能返回默认的object。虽然CouldFront有个Default Root Object设置，只是对根目录起作用，对子目录不起作用。如果使用Bucket的Endpoint，再加上之前已经给该Bucket配置了Default Object，就可以解决这个问题。

![](/images/Clouldfront_Configure_Origin_Domain_Name.png)

选择将HTTP重定向到HTTPS，在CNAMEs项中输入自己的域名，多个域名以逗号分隔。
在Distribution Setting下选择你自己的证书(如果没有可以点选Request or Import a Certificate with ACM免费申请)。

![](/images/Clouldfront_Configure_Distribution_Setting.png)

[可选]勾选Logging，并选择Bucket for Logs，可以打印访问信息。由于是静态网站，Cookie的Log就没有必要了。

![](/images/Clouldfront_Configure_Logging.png)

点选**Create Distribution**，创建成功，需要等待大约半小时等待Status从```In progress```变为```Deployed```，在这期间可以配置Route53。

![](/images/Clouldfront_Configure_Success.png)

配置成功后可以在管理员面板修改所有创建Distribution时设置的参数，并可以增加Error Page等设置。

![](/images/Clouldfront_Configure_Admin.png)

> * 注意：Cloudfront的配置每次更改都需要从新部署，每次重新部署都需要大约半小时时间，为了避免不必要的时间浪费，最好是一次配置成功，不然真的很痛苦。。
> * 另外，关于Cloudfront的日志，AWS不会针对日志功能进行收费，但用户需要对占用的S3 bucket存储和访问付费，日志内容大概和Nginx的access.log差不多，个人觉得AWS的[Reports & Analytics](https://console.aws.amazon.com/cloudfront/home#cache_stat_reports)已经做得很好了，日志有些多余，建议可以在学习完后关闭。

### 关于缓存时间

关于S3里的Object在Cloudfront的各节点缓存的时间，默认为24小时，也就是说当我发布一篇新博客，由于主页```index.html```名字没有变化，只是更新了新版本，我要等到24小时候Edge location中缓存的数据过期才能看到新版本，这对我的小博客来说时间太长了，需要更改这个Cache时间。更改Cache时间有两种方式：一是更改TTL（Time To Live）时间，二是增加```Cache-Control: max-age=[seconds]```的header，关于第二种方式，具体参见[官方文档](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Expiration.html#expiration-individual-objects)，这里我说一下如何更改TTL：
进入Distribution的管理员界面，选择**Behaviors**标签，勾选待编辑的Behavior，点选**Edit**

![](/images/Cloudfront_Distributions_Behaviors.png)

在Edit Behavior页面Object Caching项目勾选**Customize**自定义TTL，将Default TTL改为3600（1小时）点选**Yes，Edit**即可。
同样，更新配置要等待半小时左右方能生效。

> * 参见 [Specifying How Long Objects Stay in a CloudFront Edge Cache (Expiration)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Expiration.html)

#### 首页缓存时间

更改TTL后，首页的更新也需要一个小时，对大部分情况来说这个时间还是比较长，这个时候可以通过更改S3的Bucket里面的Object的Metadata的方式增加```Cache-Control```的Header来进一步减少首页的Cache时间。

![](/images/Cloudfront_S3_Metadata.png)

> * 这个手动的设置会在每次Deploy的时候被覆盖，所以需要给Deploy插件增加Update Metadata/Header的设置。

### 如何清除缓存

## 配置Route53

进入Route53的[Console](https://console.aws.amazon.com/route53/home)，进入**Hosted Zones**标签，点选使用的Domain Name。

![](/images/Route53_Host_Zones.png)

点选**Create Record Set**，输入二级域名下的三级域名（可以置空），选择Type为A类地址，选择一个A-record），在AWS的内部资源中选择Cloudfront下对应你博客的域名，Route Policy为默认的Simple即可，点选**Save Record Set**

![](/images/Route53_Create_Record_Set.png)

只需要数分钟，A-record即可生效。

## 验证

现在可以通过HTTPS访问我在每个Edge Location缓存的博客了：

[https://www.duyidong.com](https://www.duyidong.com)

经测试，同一个页面在加上Cloudfront之前（在S3 bucket中）访问时间是11秒，使用gitHub.io(带CDN)是6秒，使用Cloudfront之后加载时间大概是2.4秒，可见Cloudfront的加速效果是非常理想的。


> * 参考资料
> * [https://www.huangbowen.net/blog/2013/10/01/migrate-octopress-to-aws-step-2/](https://www.huangbowen.net/blog/2013/10/01/migrate-octopress-to-aws-step-2/)
> * [https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)
> * [https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Expiration.html](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Expiration.html)
