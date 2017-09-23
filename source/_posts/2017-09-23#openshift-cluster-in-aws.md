---
title: Openshift 多节点部署
date: 2017-09-23 22:10:00
categories: Cloud & PaaS
tags: 
- DevOps
- PaaS
- Openshift
- Kubernetes
- 平台即服务
- AWS
---

本文介绍如何在 AWS 中搭建一个 Openshift 集群，网上有一些这方面的教程，但不多，且颇为繁琐。本文力求简化操作描述，更加关注在 Openshift 架构理解上。

主要用到的资源：<https://github.com/openshift/openshift-ansible-contrib/tree/master/reference-architecture/aws-ansible>

开始之前你最好对这这些知识点有一定了解：[AWS](https://www.duyidong.com/2017/02/28/AWS-Services-Overview/)([EC2](https://www.duyidong.com/2017/03/15/AWS-EC2/), ELB, [IAM](https://www.duyidong.com/2017/03/06/%E6%B5%85%E8%B0%88AWS-IAM/), [CloudFormation](https://www.duyidong.com/2016/08/02/AWS%E4%B9%8BCloudFormation/))，[Ansible](https://www.duyidong.com/2016/06/15/Ansible%E5%AD%A6%E4%B9%A0%E5%B0%8F%E8%AE%B0/)，[Bash](https://www.duyidong.com/2016/08/05/Bash%E5%AD%A6%E4%B9%A0%E5%B0%8F%E8%AE%A1/)，同时建议先看看本博客中关于 Openshift 和 Kubernetes 的基础知识：[Part 1](https://www.duyidong.com/2017/06/14/kubernetes-and-openshift/) [Part 2](https://www.duyidong.com/2017/06/15/kubernetes-infrastructure/) [Part 3](https://www.duyidong.com/2017/06/15/openshift-quick-start/)

# 准备

- 使用 Route53 申请一个域名。这里注册为 ```oc-tw.net``` ，后文中出现这个 oc-tw.net 的部分请替换为注册域名。
- 选择一个至少有 3 个 AZ 的 Region。这里选择为悉尼，Region id（可在console的URL 中获得）为：ap-southeast-2。

# 开始

## 创建堡垒机

这个堡垒机是用于运行 Ansible 脚本及配置集群使用，并非 Openshift 集群的一部分。所以可以用手动创建的方式，可以选择在本地运行。

从 Marketplace 找一个 Centos7 的 AMI （如图所示）创建一个 EC2 instance 并绑定一个 EIP，开启 22 端口。

![](/images/aws_marketplace_ami_centos7.png)

Instance 启动完成后，登录到堡垒机上，执行如下安装命令：（后面的全部命令行操作均在堡垒机上执行）

```
yum -y install atomic-openshift-utils ansible openshift-ansible-playbooks
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum -y install python2-boto \
                 pyOpenSSL \
                 git \
                 python-netaddr \
                 python-six \
                 python2-boto3 \
                 python-click \
                 python-httplib2
                 
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum -y install python-pip git python2-boto \
                 python-netaddr python-httplib2 python-devel \
                 gcc libffi-devel openssl-devel python2-boto3 \
                 python-click python-six pyOpenSSL
```

## Clone Ansible 脚本

```
mkdir -p /usr/share/ansible/openshift-ansible
# Openshift 官方配置脚本
git clone https://github.com/openshift/openshift-ansible.git /usr/share/ansible/openshift-ansible

# contribution 脚本
git clone https://github.com/openshift/openshift-ansible.git
cd openshift-ansible-contrib/reference-architecture/aws-ansible/
```

## 配置 SSH 

```
vi ~/.ssh/config
Host *.oc-tw.net           # 这里需要替换申请来的注册域名
     ProxyCommand               ssh ec2-user@bastion -W %h:%p   
     IdentityFile               /root/.ssh/yidong_ohio_openshift.pem

Host bastion
     Hostname                   bastion.oc-tw.net       # 替换为注册域名
     user                       ec2-user            
     StrictHostKeyChecking      no
     ProxyCommand               none
     CheckHostIP                no
     ForwardAgent               yes
     IdentityFile               /root/.ssh/yidong_sydney.pem  # 保存在本地的 ssh 私钥
```



## AWS 秘钥对写入环境变量

由于堡垒机需要申请 AWS 资源并取得配置权限，所以需要配置 AWS 秘钥对，这里因为 Ansible 是从环境变量里获得 AWS 秘钥的，所以没有使用 IAM role。

```     
export AWS_ACCESS_KEY_ID=foo
export AWS_SECRET_ACCESS_KEY=bar
```

## 设置 Github OAuth

这里需要使用 github OAuth 完成单点登录，参照[官方文档](https://help.github.com/enterprise/2.10/admin/guides/user-management/using-github-oauth/)，

**Homepage URL** 格式为：https://openshift-master.oc-tw.net （一二级域名替换为注册域名）

**Authorization callback URL** 格式为：https://openshift-master.oc-tw.net/oauth2callback/github（一二级域名替换为注册域名）

OAuth App 添加完成后将会得到一个 **Client ID** 和一个 **Client Secret**，后面会使用到。

# 执行

在堡垒机中```openshift-ansible-contrib/reference-architecture/aws-ansible/```目录下创建并执行如下脚本：

```
./ose-on-aws.py \
--keypair=yidong_sydney \                    # 所以 EC2 Instance 用于通信的 keypair
--public-hosted-zone=oc-tw.net \             # 注册域名
--deployment-type=origin \                   # 在 Region 部署(会创建 VPC )
--ami=ami-ccecf5af \                         # 注意*
--github-client-secret=<Client Secret> \     # Github Client Secret
--github-organization=<Organization name> \  # Github 中 OAuth Organization 的名字
--github-client-id=<Client ID> \             # Github Client ID
--region=ap-southeast-2 \                    # 堡垒机所在 Region，也是创建 Openshift 集群的 Region
--key-path=/root/.ssh/yidong_sydney.pem \    # 堡垒机上私钥存放位置
--containerized=true                         # 集群以容器的方式启动
```

> *这里有一个需要特别注意的地方，就是 AMI 必须使用 RHEL 的 AMI，在 AWS 中 RHEL 会比 CentOS 大约贵 30%，但是使用 CentOS 会比 REHL 缺很多包，安装起来非常麻烦，所以建议直接使用 RHEL，AMI ID 可以在直接在 AWS EC2 lunch instance 的界面中找到。

还有很多可配置项，保持默认值即可。

## 创建集群基础设施

运行脚本，大致逻辑是：

使用 Cloudformation 创建基础设施资源 -> 使用 Ansible连接 EC2 instance 并配置集群 -> 开启监控和日志搜集。

Cloudformation 里的基础设施大致包含：EC2, EIP, SG, ELB,  IAM(User, Role, Policy, AccessKey), VPC(RouteTable, Subnet, Route, InternetGateway, RouteTable), NatGateway。

> 注意：这套脚本所启动的集群至少需要 9 个 EC2 instance，（如图所示）从 t2.micro 到 m4.xlarge，在Cloudformation 创建开始后，如果你的账户没有在免费试用期每小时大概会划费 2-3$，具体参见[官方文档](https://aws.amazon.com/ec2/pricing/on-demand/)，注意选择```RHEL```和``你所在的Region``，价格会有差异。

## Trouble Shooting

Ansible 脚本的运行时间大概在一个小时，有 4000+ 个 Tasks（有的是重复执行），在执行到半个小时的时候遇到报错再来修复着实痛苦。建议在第一次执行的时候就阅读这部分内容，以便提早 fix bug。

### Anisble 连接不上目标机

### 找不到 Docker 

### 缺少依赖

第一次运行脚本会遇到一个报错

安装Docker:

```
all_hosts="master01 master02 master03 app-node01 app-node02 infra-node01 infra-node02 infra-node03"
for h in $all_hosts; do ssh ec2-user@ose-$h.oc-tw.net 'sudo yum-config-manager --enable rhui-REGION-rhel-server-extras -y'; done
```



https://openshift-master.oc-tw.net/console/

https://registry-console-default.apps.oc-tw.net/



https://openshift-master.oc-tw.net/console/command-line

oc login https://openshift-master.oc-tw.net --token=bcD3sprC9hjOgopov9VfbHEaBbPK03WQt23MXm-KtGw

oc new-app centos/ruby-22-centos7~https://github.com/openshift/ruby-ex.git













guanging:

```
./ose-on-aws.py --region=us-east-2 --keypair=lgm-oc --public-hosted-zone=oc-tw.net --deployment-type=origin --ami=ami-cfdafaaa \
--github-client-secret=6497d7521551c5e98a834c47b1f073e255fafb81 --github-organization=ThoughtWorksInc \
--github-client-id=0997d0f5c18fd096d5f5

./add-node.py --region=us-east-2 --keypair=lgm-oc --public-hosted-zone=oc-tw.net --deployment-type=origin --ami=ami-cfdafaaa \
--use-cloudformation-facts --subnet-id=subnet-1139825c \
--node-type=app --shortname=ose-app-node03 --existing-stack=openshift-infra

ansible-playbook -i inventory/aws/hosts \
    -e 'region=us-east-2 stack_name=openshift-infra ci=true' \
    -e 'extra_app_nodes=openshift-infra-ose-app-node03' \
    playbooks/teardown.yaml
```

