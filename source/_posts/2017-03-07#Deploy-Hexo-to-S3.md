---
title: Deploy Hexo to S3
date: 2017-03-07 17:42:34
tags: [AWS, S3]
---
# 将Hexo博客发布到S3

最近在学习AWS服务，加之两次遇到github.io无法访问的情况，决定将博客迁移到AWS S3上。

## S3介绍

S3是AWS一个对象存储服务，拥有11个9的耐久度和3个9的可用性（并不算高），属于AWS底层存储服务。同时提供Host静态网站的功能

## 第一步：创建一个Bucket

 - 进入S3界面，点击**CreateBucket**，取名为www.duyidong.com
 - 选择一个Region，你可以在[这里](http://s3-accelerate-speedtest.s3-accelerate.amazonaws.com/en/accelerate-speed-comparsion.html)找到延迟最低的Region用于存放你的博客
 - 设置Policy，确保外部可访问：
 
 ```
 {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadForGetBucketObjects",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::www.duyidong.com/*"
        }
    ]
}
 ```
 > 注： ```arn:aws:s3:::www.duyidong.com/*```为bucket里对象的资源标识符
 
 - 在**Properties**下开启**Static website hosting**，设置入口文件为```index.html```，404页面为```404/index.html```
 

## 第二步：创建一个用于上传静态文件的用户

#### 使用Policy Generator创建一个Policy

这里需要一个只有对www.duyidong.com这个bucket有上传权限的用户，首先要创建Policy，这里要用到Policy Generator。
 - 进入**IAM -> Policies -> Create Policy -> Policy Generator**
 - 选择S3的PutObject Action, ARN为目标Bucket的ARN，届时会生成如下Policy:

 ```
 {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1488877494000",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::www.duyidong.com*"
            ]
        }
    ]
}
 ```
> 注：ARN需使用```arn:aws:s3:::www.duyidong.com*```

#### 创建用户组

**Groups -> Create New Group -> **将上一步创建的Policy attach到该UserGroup上，用户组创建完成。

#### 创建用户

**Users -> Add User**，勾选```Programmatic access```，输入用户名，将用户加入到上一步创建的用户组中，将用户的Access key下载到本地。


## 第三步：安装插件，上传静态文件

- 运行命令

```
npm install --save hexo-deployer-s3
```
编辑```_config.yml```:

```
deploy:
  type: s3
  bucket: <AWS bucket name>
  aws_key: <AWS id key>
  aws_secret: <AWS secret key>
  region: <AWS bucket region>
```

由于我会把博客源码放到Github上，Access key不能写在配置文件里，而采用环境变量的方式，在~/.bashrc中加入：

```
export AWS_ACCESS_KEY-ID=<access key>
export AWS_SECRET_ACCESS_KEY=<secret key>
```
再执行

```
source ~/.bashrc
```
届时将可以成功将博客发布到S3上，执行：

```
hexo d
```

## 其他

对比了一下Github和S3的速度，最大的一个页面github.io大概需要6-9秒，而S3要11秒左右，虽然github的速度不太稳定，但毕竟要比S3快，所以暂不把DNS指向S3，待日后开启CloudFront服务后再做决定。
另外，hexo是支持多路径发布的，形如：

```
deploy:
- type: git
  repo:
- type: heroku
  repo:
```

> * 参考资料: 
> * [https://hexo.io/docs/deployment.html](https://hexo.io/docs/deployment.html)
> * [http://inject.coffee/hexo-travis-s3-part-2-deploying-to-aws/](http://inject.coffee/hexo-travis-s3-part-2-deploying-to-aws/)