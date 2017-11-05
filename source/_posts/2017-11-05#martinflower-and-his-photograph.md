---
title: 老马博客的里的图片
date: 2017-11-05 15:23:28
categories: ThoughtWorks
tags:
- 爬虫
- 其他
---

由于工作和学习的需求我订阅了[老马的博客](https://martinfowler.com)，今天突然收到一则更新。



是这样的一张图片:

![](https://martinfowler.com/photos/110.jpg)

原来老马还有在博客里放图片的习惯。由于 URL 是序列的，我往前翻了几张：

![](https://martinfowler.com/photos/109.jpg)

![](https://martinfowler.com/photos/108.jpg)

![](https://martinfowler.com/photos/107.jpg)



都很美，透露着淡淡的温情。猜想应该是博客主人出去玩时随手拍摄后筛选的比较好看的图片，没有 PS 痕迹，没有故意加到很高的饱和度，看起来很日常，引发了我探究这个老外业余生活的兴趣，于是我决定下载他的所有图片。

一行 Shell 命令:

```
for i in {1..110}; do curl -O https://martinfowler.com/photos/$i.jpg &; done
```

两分钟就把一百多张图片下到了本地。脑海里不觉浮现出了刚开始做爬虫的时候把一个人所有的社交账号发布的言论装到一个数据库里的场景。

一个人的网络画像，你的邮箱，你的社区圈子，你在网上说过的话，你买过东西，你注册过的网站，甚至于你习惯使用的密码。这些信息一旦进入网络世界就不可能被擦除，如果将来 85 后 90 后登上国家领导人的位置，这些信息会怎样，我们又将如何来看待我们的过去？

> 最后给一个老马图片收藏的入口：
>
> <https://martinfowler.com/photos/>