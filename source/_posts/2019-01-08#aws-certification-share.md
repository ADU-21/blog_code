---
title: AWS 认证分享
date: 2019-01-08 23:09:40
categories: Amazon Web Services
tags:
- AWS
- 云计算认证
---

2019 年在办公室做的第一个分享，话题是AWS认证，整理成文，记录在此。

<!--more-->

# 目录

今天的主题主要分为三个部分：首先我们为什么要做这个认证，从自己来讲为什么会想要做这个认证，第二个相信是很多大家来参加这个 Session 关心的问题，就是这个认证是什么样子，考了有没有什么用，然后如何准备，这样的一些相关的问题。然后我最后会给大家分享一下我的学习路线一些学习资料，如果大家有兴趣去了解这方面的内容，这个是比较直接的可以帮到大家。

# 为什么要认证

这是 Gartner 的一个活力象限，大家都比较熟悉。我们来讲为什么我们要学习 AWS 的认证，跟我们有什么关系。我自己是这样看的，因为我自己本身不管是在国内的云厂商也好或者其他的厂商待的时候发现的一个问题是：知识和技能在某一个专业方向的积累一定是要有一个长期的规划的，所谓长期规划呢就是说，如果说有这么一个厂商他自己有这么一套非常完善的方法论，而目前他在整个行业上属于一个非常领先的位置，注意我这个地方说领先我会按照 Gartner 的说法，他首先是个 Visionaries，Visionaries 什么意思，我举个例子就是说他在云这个领域他出什么东西都会比别人早这个就叫 Visionarie；他出了这个产品以后后面有人会跟随，这个就叫 Visionarie，比如说2014年，大家都还在说云计算都只是做一个就是还在上虚拟机的时候，他出了 Lambda， Serverless，这个就很牛了，然后 Serverless 腾讯云和阿里云什么时候跟进的，是去年年初的时候才跟进的，晚了三年，等阿里云介入的时候 AWS 已经 把这个产品做成了一个事实上的标准。所以这就是一个 Visionarie 的表现。然后我们再往上走，Leader 什么叫做 Leader，大家都用，就叫 Leader。AWS 今年 2018 年的市场占有率大约是40%，超过2、3、4 名微软、谷歌、IBM的市场占有率总和。我在项目上有一个感觉，就是你的客户在选择技术栈的时候他要不要去上 AWS。据我所知澳洲，微软的云也有一些 exposure 吧， 但是从全球来看 AWS 的受接受程度真的是蛮高，所以从这个角度呢，这个技术我认为是可以去投资的，至于你投资的具体方式是不是考个证我觉得另说。

![](/images/cloud-computing-marketing-rank-2018.png)

<https://www.skyhighnetworks.com/cloud-security-blog/microsoft-azure-closes-iaas-adoption-gap-with-amazon-aws/>

这里有一个看上去比较鼓舞士气的，别人说 2018 年你如果是一个 Certified 的 Solution Arhciture 你可以大概能够拿到12.5万美金吧。这个数字看看就行了，这个严格来说在统计学上是不靠谱的。为什么？大家想一想，这个结论，我觉得在阿里有一个很好的文化就是我们要去看这些事情背后的东西，如果你是一个统计学家你怎么才能证明这一点，这一点必须要有几个因素是说，我同样的一个人，他的能力是基本一样的，他背景一样，教育程度一样，他就多了一个证，所以他的薪水高。对吧，我通过这样的样本来我才能得到这样的结论，但这样的调查其实很难做。

所以你可以认为说，有一波很牛的人，他们薪水本来就很高，然后他顺带考了一个证。是吧，你可以这样来解释吧，他们本来就很牛，他工资本来就很高了，然后为了证明自己，欸我这个已经很不错了我考个证玩玩儿呗，啪薪水就上去了，这个就被 AWS  拿来做一个很牛逼的数据说，你们都来学我这个证。

![](/images/people-said-aws-certifiy-is-valuable.png)

<https://www.forbes.com/sites/louiscolumbus/2018/03/12/15-top-paying-it-certifications-in-2018/#17711e85623b>

**Don’t be high I’ve seen what had happened.**

那我们来说一个，就大家看到这个数据不要太 High，十五年前很流行考的一个 证，CCIE 当时CCIE 的薪酬大家知道是多少，猜一个数字，猜一个薪酬，十五年前，年薪。十五年前一个 CCIE 的薪酬大概是在 50w 左右，那个时候为什么很强势，因为 CCIE 必须要挂靠在合作伙伴这里，合作伙伴必须要有 CCIE 的认证才能拿到 思科的一些资质，这个很重要，所以其实到现在如果说你要纯招一个 CCIE，我觉得人民币大概开到月薪 5000 左右应该可以招到了，5-7k应该可以找到了，就是你不考虑他的任何项目背景，当然我只是说你只是找一个人有 CCIE 这个资质的，你应该是可以找到的，但是如果你要求他有真正匹配的能力和这个丰富的项目经验那是不一定的，那肯定市值是远远超过了我刚刚说的这个价值。

那这个给大家一个什么样的概念， 就是说，从过去的历史来看，这样的一个认证的出现到他最后肯带是有一个变化，但是目前我仍然比较看好 AWS原因在于我个人认为是比较健康的，比如CISCO 的认证体系包括培训体系和认证体系其实被社会上这种帮你拿证这种风气给搞混乱了。所以目前来看我认为 AWS 是比较有投资价值，就相当于说，他的这个难度，可以吸引很多人去考，而他的难度并不能够保证你能够轻易地通过，所以我认为这个是比较有价值的。然后第二个投资价值就是我刚才讲的 AWS 在市场上是有号召力的。

# 为什么 AWS 需要认证体系

为什么AWS 或者思科这样的公司会推出这样的 Ecosystem。其实认证，是可以帮助他去建立这样的 Ecosystem 的，比如说 AWS 要求他现在的合作伙伴，据我所知，在中国区现在他最牛的一个 AWS 合作伙伴公司，是需要公司有 8 个 professional 这样资质的人，然后有 20 个 Associate 这样 level 的人，所以他会对他建立他的合作体系会有好处。然后第二点，就是在企业我招人的时候我可以很轻松地 Identify 这个 skill，能够很快地选择合适的候选人，这个作为入门的门槛。然后第三部分呢他也为这种规范化比较系统化的学习提供了一个合适的框架，这个是为什么企业巨头要去设置这种认证体系的几个原因吧。

![](/images/aws-partners-consulting.png)

<https://aws.amazon.com/partners/consulting/>

# 我为什么要考 AWS 的认证

那么从个人来说，我为什么去选这个证。首先第一个就是，我认为他就是一个 Measure，一个度量，我自认为我对这个AWS有比较多的了解，但是到底到了哪个阶段到了哪个水平，我需要一个相对客观的标准，这个是我考这个认证的一个初衷。然后接下来一点我认为是一个自驱动，很多时候你会去学一个东西，或者说是去想掌握很多东西的时候你需要设定一个目标，那这个 Goal 是最容易设的，我通过几个月的学习，我可以拿到这样一个认证，设定这个目标是一个比较容易的办法；然后第三个呢就是持续学习，这个持续学习我想强调的是，即便你通过了这个认证，首先他在这个规则上是要求你重认证的，每两年你要重复认证，所以这是给你一个持续学习的压力，然后第二个他还可以给你提供持续学习的机会，就是当你取得这个认证之后，很多人就会来向你咨询很多关于AWS的问题，或者公司有项目也可以过去支持一下，很多问题我刚听到也不知道怎么办，但是可以查资料，可以去继续深入了解，在这个过程中我获得的收货，可以说不亚于在考证时候学习的收货；然后第四个 Endorsement 就是你在做这种行业上的交流的时候你可以证明你可以具备相关的经验和能力，当你在跟别人沟通的时候，你自己说我是通过认证的，那么别人对你的期望值是不一样的；当然最后一个大家可能也有期望值但是我必须要浇盆冷水的就是这样的认证基本不会在你的 Money, Permotion , Salary，或者说 next job 上发挥特别大的作用。我再举一个例子，如果三个人，一个人是有认证的，一个人不光有认证还有丰富的经验，然后第三个人是没有认证但他有非常多的经验，其实这个筛选过程会很快对不对，可能第二个人他的胜算会大一点，因为别人有认证我肯定可以过第一轮筛选对不对，但是你被筛选过后你是不是具有相关的实践经验你在项目上到底用过了AWS的哪些产品，这个东西一问就出来了，所以对最后一条不要有太多的期望值。

**如果再举一个例子来形容，我觉得这个故事可能会比较形象一点，就好比说你去爬山，在爬山你启程之前你给自己做了一个 lunch box 上面你贴了一个封条说：“只有爬到封顶我才能打开它，我才能吃”，然后你就去享受你爬山这个过程克服了很多的困难，然后到最后你把这个 lunch box 打开吃掉。我认为认证在某种程度上就是这个 lunch box 。就是一个 Lunch box，你最终的收获和体会其实是在这个过程中，这个对你是最重要的。**

# 架构路线图

![](/images/aws-certification-level.png)

<https://aws-chalktalk.com/certification/>

好我们来看看不同认证的要求，我刚刚说了我做得是 Architect 的认证，我 high light 几个关键词，大家就可以去选择自己感兴趣的方向了。第一个就是你如果是 Architect 你是做 Designing 和 Deploy，Architect 你着重的是了解用户的需求，然后在架构设计上把它设计出来，说，我要什么样的架构来实现这样的功能点，然后你把它实现出来就行了。Ops 和 Develolper 就不一样，Ops 强调的是我如何通过一些 hands on 的一些操作，我能够 deploy 它，mange 它，operating 它。那 Developer 更多的是跟 AWS 已有的东西去打交道，因为 AWS 有一个非常强悍的地方是它基本上所有的东西都会有 API 和 SDK，那意味着 Developer 以前是在于开发自己的软件，现在这个概念已经变了，在于说我 Developer 可以以开发软件的方式去管理我的infrasturcture,这个就变成了 infrasturcture as code,对不对，就这样类似的一些概念，架构即代码，代码即架构。所以，如果大家去选择不同的类型的，可以和工作的相关度和自己的兴趣来看自己，从哪个点切入是比较合适的。

# 关于考试

一些关于考试的基本信息，首先这个考试都是远程，是在一个房间里面，然后不能带任何资料。然后呢，考试费用是 Associate level 是  150 美金，Profession level 是 300 美金；然后有一个时间的问题，时间是在 80 分钟内做若干道题 professional 要求是在 170 分钟。然后通过率呢其实是动态调整的，他会根据来考的人这样的一些水平来通过一些算法，保证一定比例的人通过，不是说我 60 分及格这样的，然后这个考试有一个好处就是考完马上就可以拿到成绩，然后通过以后这个认证的有效期是两年，这个有一个很合理的原因。这个有一个非常合理的原因，因为每两年 AWS 就开发出了非常多的功能。你需要 matain 你的技能，你需要继续的展示你的经验和能力，理论上你需要去更新这个考试，这个更新考试呢是类似的一个题，但是量会少一些，价格也会便宜一些。所以一般来讲你说我是这个 AWS 认证，会要求你说，这个认证有效期是多久。这个也是保证证书有价值的一个必要的手段。不是说我考完就可以管一辈子。

具体和考试有关的一些内容，几乎全部都是多项选择题，我记得应该是没有不定项，都是多项选择题，然后呢不会有这种 hands on 说你你去配一个指令这种。然后第二呢几乎不会有这个命令行的这个操作界面，你对 webconsole 足够了解就可以了，不会说你要去敲一个命令如何组合。然后第三点就是我要说的宽度而不是深度。

宽度是什么，大家看 AWS 目前已有的服务，目前的考试大纲应该是覆盖了里面 70%-90%的产品，因为考试大纲会比服务发布有一些滞后；换句话说，每一个产品，至少在构架的 trail 里你是需要了解这个产品能做什么，不能做什么，它怎么跟其他的服务集成，它基本的一些操作，它在控制台里面哪些属性是可以配的哪些是不能配的。所以，这个量很大，注定了这个考试不是一个往深了考的考试，而更多的是说我要要求你知道你在架构里你要知道这个东西是干什么的，在什么场景下需要把它组合到一起，因为它的产品实在是太多了。

**样题1**

然后呢我拿两道样题，这两道样题是公开的。我们来推敲一下他会考察大家什么样的内容。其实所有的问题在考试里面会遇到两种，一种就是客观的东西，他是一种客观事实， 这个一定会有对错，就是比如说 galcier 在那种场景下是可用的，他一定会在某种场景下是可用的，在别的场景下就不好用或者不能用的。还有一类问题是一类场景。

![](/images/aws-example-1.png)

**样题2**

他给到你一个场景之后你会有一个限制条件， Associate level 你可能选的是一个对的答案，就是说，this is good, this is  right one，其他的都是错的。但是在 Professional level 你需要做的是 chose the best answer, 他会给你一些限制条件，比如说我们来看这道题，我们只读一句话，中间这句话，他说，在这个复制应用到到 AWS 环境之后哪个选项允许你把这些数据放到 AWS 上 without losing any data and within the given timeframe，如果是 Associate level 我可能里面就一个选项说我可以把它搬过去。但是他给的条件是第一你不能丢数据，第二你必须在 given 的 timeframe，那你的选项就必须要认真地去读，可能四个都能干这个事，但是你必须要做的是 the best answer。

![](/images/aws-example-2.png)

# 如何备考

如果说要准备这个考试的话我建议说可以从哪几个点。首先第一个点就是，基础知识。这个基础知识是为了应付这个对和错的问题，这个还不涉及场景，那我举一个例子，大家能不能说说这个 durability 和 Availability 和 integrity 的区别？如果我们把他们投射到 AWS 具体的产品上面，我挑 S3 或者 Glacier 来对比，大家觉得 Durability S3 做得怎么样， 11 个 9, availability 是多少，只有四个 9，所以这个就有区别了，Durability 说我数据我放在 AWS 上，我不丢，AWS 的官方解释是 我 10w 个数据放到 AWS S3 上 我在 大概 1000w 年的时间里会丢一个。这个就是 11 个 9 的概念。那 Availability，这个业界有通用的描述，就是我在一年以内，我有多少分钟不能访问，对吧，这个 是 Availability，那 Integrity 是说我数据不会被改掉。那么这个就可能是有一个思路是，那我存一个数据，我对这三个方面的要求是什么，针对这些要求，你选择一个恰当的存储。所以这些都是非常基础的知识。你需要真的是很细地去看。

然后第二个：足够的熟悉 Web console，为什么需要足够的熟悉 Web console，是你如果光看白皮书你去看这个产品有什么功能你的理解是很肤浅的，或者说不够直观，但是只要你看了 webconsole 你就会有一个概念说，这一产品我需要有三个参数需要在第一页配，第二个参数需要在第二页配，他们之间有没有什么关联，你把这一个路径走完，你基本上会对这个理解深非常多。其实换句话说，我不算是重度用户，但是我在不是很频繁，账单可以保持在一个月十几美元，就说明你用得足够多了，其实 AWS 很便宜的，而且初始用户都有一年的免费的叫 FreeTier 可以去选，然后你足够熟悉去用它，这个对你的理解会好很多，这个投资一定是值得的。

# 解题思路

回来看样题2。我们可以用**"Scenario"->”Goals“->”Constraints“->”Components“->"Structure"**五个点来分析这道题。 Scenano，这个 Company 是一个已有的老旧的系统，900个G已经在 Server 上了，就是他的现状。它会讲说更新的这个频率，百分之多少更新，这些都叫 Scenario；Goals，很明确要做迁移，要迁移到 AWS；再进一步分析，Constrains 在哪里，不能丢数据，然后是在 given 的 time frame，一旦涉及到 given 的 timeframe 就立刻会想到这个 45M 的 internete connection 是不是个瓶颈，那个这时候就会提醒你了，把这些信息整合到一起了，在你熟悉 component 的前提下，你先得熟悉 component，每个产品知道他干什么的，他的优势在哪里，不能做的是什么，能做的又是什么，他的成本高不高，然后，以一个合适的逻辑方式把它组合起来，Structures。其实这一点我不得不赞一下 AWS 的考试，我觉得他这个考试很贴合实际，因为这些都是你实际在考试工作中遇到的，比如说我作为一个真正的就是说在实际的工作环境下我要做一个架构师，我其实面对的就是这个场景。就是客户说，我跟你讲一讲我们公司现在什么什么情况，然后要达成什么目的，有什么限制条件，来给我出一个方案，这个就是架构师应该做的事情，所以我觉得 AWS 这个考试的含金量在这个地方也是我比较认同他的这一点，我认为这个跟现实的环境其实蛮像的。

<https://aws.amazon.com/architecture/>

<https://media.amazonwebservices.com/architecturecenter/AWS_ac_ra_loganalysis_11.pdf>

# 学习资源

接下来我说一下这个学习的资源有哪些吧，最最直接的资源其实就是 AWS 的官方文档，然后还有一个是 video，video 在 yotube 上你基本上可以搜到你想看的所有主题，video 的很多东西他是往深了走，你如果光从这个考试的角度上，比如说你其实一个两个小时的 session 你真没必要听完，你就自己选自己觉得感兴趣的，或者这种纲领性的东西你听个大概半个小时其实就够了，因为他很多其实会走非常细的一些东西，从考试这个角度我觉得你没有必要全部听完，当然你如果是希望掌握这些技能，那你一定要去好好的看。然后第三方呢有很多这个 website，然后会有这个 online 的 seminar，当然有真正由AWS 认证讲师去讲的这种 trainning program，但是这种也比较贵，大概是几千块钱接近一万块钱的接近两到三天的这样的一个培训。然后最后一条就是你自己的 customer case。

**官方文档**

![](/images/aws-documentation.png)

Official Documents on [aws.amazon.com](http://aws.amazon.com/)

这个图其实AWS 的文档体系，先不看红色的框，我们只看这个就是整个的结构，他其实分为整个的这么几块，产品维度，有三个文档，一个叫 developer guide，你可以理解为这个就是 hands on 的一个指南了，就说你要用这个产品，你所有的操作怎么样，他所有的文档都叫 developer guide，你不要以为只有 developer 才会用，然后就是 API,API 很少用到，因为至少架构的考试里面是不会涉及 API 的。然后非常重要的是 FAQ，就是大概说这个产品能做什么不能做什么，就简单的包括计费。

上面这三个都是具体跟产品相关的，然后往下面就是深入到方案场景了，这个时候其实就是体现我其实不是一个产品，是多个产品的有机结合，那么这个时候首先有一个快速上手，快速上手是针对一个特定主题，比如说，我如何用 AWS 部署一套 wordpress，那么这个时候你可能会用到 S3，用到 EC2，你可能会用到 cloudfront，那么这些东西你怎么去把它部署起来，这个就是快速上手，然后呢会有客户案例，还会有白皮书。 白皮书呢其实也是针对一个特定主题去讲，包括一些合作伙伴白皮书，这个合作伙伴是指技术上的合作伙伴，那么如果他们之间，比如说 ACP Hannah 跟 AWS 会有关联，那么他会有一些介绍。然后，一些快速参考部署，然后参考架构和架构白皮书，所有里面我红框框出来的我认为是对架构考试会非常有帮助非常重要的，可以这样说，只要是 AWS 用红框框的这些内容我都会读。

**AWS 白皮书**

这部分内容可以参见我的另外一篇文章：<https://www.duyidong.com/2017/03/23/AWS-well-architected-framework/>

**AWS 架构图**

好，这个图是我刚刚讲过类似的一个图，这个图也是按我们刚刚的一个分析其实大家从这个图里面可以获得很多的信息，第一个就是用到的产品，然后，他解决的是什么样的一类问题，然后系统连接和组件。如果你能对所有场景，都能够抽象出这种图来，那么你对整个 AWS 架构就会非常熟悉了。无论是用相关的一些 Icon 还是你用白板自己在白板上画，你能够把任何的场景用这个图来表示，说明你对这个架构已经足够深的了解了。

![](/images/aws-architecture-map.png)

<https://aws.amazon.com/architecture/>

**Write it down**

好，然后这里我要特别强调一点，在学这个过程当中会发现一个东西，你不停的学，不停地看，你看了很多，你记不住，至少对我而言，因为我记性不是很好，所以我的一个习惯是一定要把它写下来，我会怎么写，恰好我通过认证了以后我也看了下我这个记录，也回顾了一下我这个准备的时间。当然也不是为了这个认证准备，就是AWS笼统的这个学习时间，我的第一篇日志是xxx实践写了，然后我的最后一篇日志也就是取得认证的时候是 xxx写的，然后我中间大概有一年大概是在xxx只有四篇日志，所以我的节奏基本上是如果我看到了这个东西，我哪怕是读了一个 vedio,我就去截屏，然后把它重点 hight light 下来，然后把它的重点，你觉得真正有价值的东西，把它记录下来，否则我觉得这个转化率或者我个人能够掌握它的程度真的会非常有限。所以我就按这个适合我个人的方法来做，当然我不确定这个所有人都适合，我举个例子，我会怎么去写。

这里附上一个我和 AWS 有关的日志列表：<https://www.duyidong.com/categories/Amazon-Web-Services/>

# 总结

最后我总结一下，目前看来我认为这个认证是值得投资的。但是如果说你不需要这样的一个目标的设定，不需要这样的方式你依然可以在项目中积累足够的经验，仍然能达到这样的一个的水平。

更多的信息，可以参见我两年前写的一篇博客：https://www.duyidong.com/2017/04/05/How-to-pass-the-aws-certification/。谢谢！