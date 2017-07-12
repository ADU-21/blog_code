---
title: PaaS 平台（三）-- Openshift 使用
date: 2017-06-15 18:27:41
categories: Cloud
tags: 
- DevOps
- PaaS
- Openshift
- Kubernetes
- 平台即服务
---
Openshift 是一个基于 Kubernetes 和 Docker 的 PaaS 平台。

# Openshift 概念补充

我们在上文--[Kubernetes 介绍](https://www.duyidong.com/2017/06/15/kubernetes-infrastructure/)中已经提到 Kubernetes 引入了一些概念，Openshift 在此基础之上又对这些概念进行了封装。了解 Openshift 除了了解它的架构以外，还要了解他抽象出来的概念。

- **ImageStream:** 对 Docker Image 的抽象，Docker image 只是一个二进制文件实体，而 ImageStream 可以带有状态，可以对外部程序的访问做出相应。
- **BuildConfig:** 对 Dockerfile 的抽象，上文已经说到 Openshift 中的 Pod 实际上是由多个 Container 构成的，这些 Conatainer 仍然是运行时的 Image，这些 Image 的构建过程就是由 BuildConfig 决定的，不同于 Dockerfile，BuildConfig 可以支持将 gitrepo, Dockerfile, Binary, Image, Input Secrite(Openshift 定义的另一种资源类型，用于存储敏感信息), 二进制软件包，这些资源类型统统打到一个 ImageStream 里面，在“构建”这个动作的上下游还支持事件触发，比如定义“gitrepo的提交会触发一次构建”，以及“在构建完成后执行一次测试”。
- **DeploymentConfig:** 对 Replication Controller 的封装，定义了应用的运行时架构。
- **Route:** Kubernetes 定义了 Service 用于实现服务之间的隔离和调用，Openshift 在此之上增加了一层 Route，至此 Service 可以面向内网环境，而由 Route 来真正对外提供服务。

将以上流程串起来，在 Openshift 上一个应用的诞生就应该是如下流程：

![](/images/openshift_deploy_process.png)

熟悉了 Openshift 的基本概念，下面我们可以动手搭建一个 Openshift Origin 版，并在上面部署一个应用。

# Openshift 安装

这里安装的是 Openshift 社区版，官方也有提供 minishift（需要使用虚拟机）以及 Container 的方式安装，这里使用的方法是用 oc (openshift-cli)，是最简单，也是官方现在推荐的安装方式。
安装环境可以选择在 Mac 上或者是云虚拟机（Windows 不确定是否支持），我这里选择的是 AWS EC2，关于其他平台的安装，具体可参照<https://github.com/openshift/origin/blob/master/docs/cluster_up_down.md>

## 第一步：在亚马逊上申请一个 Ubuntu 的机器

有几个要注意的地方：

- CentOS 的 Dcoker 安装可能会遇到一些问题，是由于 Docker 社区版不支持，详见[这里](https://docs.docker.com/engine/installation/)。
- 如果你想实现 hook 功能，即看到代码提交触发 Openshift 内构建的效果，你需要赋予 Instance 一个公网 IP。
- 安全组开启外部可访问 8443 端口。

## 第二步：安装 Docker 

这里要说明的是现有 Openshift 在 docker 的最高版本下支持有些问题，经测试 1.12.1版本是稳定的。
安装脚本：

```
# Install Docker on Xenial 16.04.1 x64
# Ref https://docs.docker.com/engine/installation/linux/ubuntulinux/
# No interactive for now.
export DEBIAN_FRONTEND=noninteractive
# Update your APT package index.
sudo apt-get -y update
# Update package information, ensure that APT works with the https method, and that CA certificates are installed.
sudo apt-get -y install apt-transport-https ca-certificates
# Add the new GPG key.
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
# Add docker.list
sudo echo "deb https://apt.dockerproject.org/repo ubuntu-xenial experimental" > /etc/apt/sources.list.d/docker.list
# Update your APT package index.
sudo apt-get -y update
# Purge the old repo if it exists.
sudo apt-get purge lxc-docker
# Verify that APT is pulling from the right repository.
sudo apt-cache policy docker-engine
# Install the recommended package.
sudo apt-get -y install linux-image-extra-$(uname -r)
# Install Docker.
sudo apt-get -y install docker-engine=1.12.1-0~xenial --allow-downgrades
# Start the docker daemon.
sudo service docker start
# Validate docker version
docker -v
```

执行最后一条命令，出现 Dokcer 版本为 1.12.1 表示安装成功。

## 第三步：配置 Dokcer registry

编辑`/etc/systemd/system/multi-user.target.wants/docker.service` docker 的 service 文件，将`ExecStart=/usr/bin/dockerd -H fd://`这一行，替换为 `ExecStart=/usr/bin/dockerd --insecure-registry 172.30.0.0/16 -H fd://`，然后执行`systemctl reload-daemon docker`，`systemctl restart docker`

## 第四步：下载、启动 Openshift

在服务器上下载[openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-linux-64bit.tar.gz](https://github.com/openshift/origin/releases/download/v3.6.0-alpha.2/openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-linux-64bit.tar.gz)oc 客户端，解压后把 oc 所在路径加入环境变量，执行：

```
oc cluster up \
--public-hostname=ec2-13-228-41-255.ap-southeast-1.compute.amazonaws.com \
--routing-suffix=13.228.41.255.xip.io 
```

把以上命令中数字替换为你的 Instance 的公网 IP，即可启动一个外部可访问，应用也可以被外部访问的 Openshift 集群。

```oc login system:admin```可以以管理员权限登录
```oc login https://ec2-13-228-41-255.ap-southeast-1.compute.amazonaws.com:8443```可以以任意用户名密码登录
也可以直接访问 https://ec2-13-228-41-255.ap-southeast-1.compute.amazonaws.com:8443 进行网页操作。

至此，Openshift 的安装全部完成。

# 部署一个应用到 Openshift 上

这一部分内容来自于 workshop，完整版可参照<https://github.com/ADU-21/openshift-workshop>

## 在本地安装 openshift 客户端

Mac 版：[openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-mac.zip](https://github.com/openshift/origin/releases/download/v3.6.0-alpha.2/openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-mac.zip)
Windows 版： [openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-windows.zip](https://github.com/openshift/origin/releases/download/v3.6.0-alpha.2/openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-windows.zip)
Linux 版：[openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-linux-64bit.tar.gz](https://github.com/openshift/origin/releases/download/v3.6.0-alpha.2/openshift-origin-client-tools-v3.6.0-alpha.2-3c221d5-linux-64bit.tar.gz)

下载到本地之后解压，将一个名为 oc 的可执行文件路径放到 PATH 环境变量里，确保随处可执行。然后执行： ```oc login https://ec2-13-228-41-255.ap-southeast-1.compute.amazonaws.com:8443```，输入任意用户名密码即可登录。

## 部署一个应用

**1** 创建一个项目

```
oc new-project [project name]
```
Project 的作用是对 Openshift 的集群中的资源进行隔离。

**2** 将一个[官方例子](https://github.com/openshift/nodejs-ex) fork 到自己的账户下，并克隆到本地：

```
git clone https://github.com/<your-github-name>/nodejs-ex.git
```
这里包含了项目所需代码，是一个 NodeJS 的应用，稍后我们将把这个应用部署到 Openshift 上。

**3** 创建一个应用

```
oc new-app https://github.com/<your-github-name>/nodejs-ex.git
```

执行这一步命令，Openshift 会自动去查找 repo，根据代码的一些特征（比如`package.json`）识别这个项目类型，准备响应环境，可以通过```watch oc get all```的命令看到 openshift 实时创建了哪些资源，按逻辑顺序如下：

- bc: BuildConfig Openshift - 会先创建一个用于 Build image 的环境，然后创建一个叫做 Build 的对象，这个对象的实体是一个 Pod，在这个 Pod 中完成 Image 的构建
- is: ImageStream - 用于管理 Image 的资源对象
- bc: DeploymentConfig - 会创建一个 rc (Replication Controller)资源，又 rc 再去创建承载应用的 Pod
- svc: Service - Pod 的负载均衡器，与 Kubernetes 中提到的 Service 相同
- Pod: 除了提供运行环境的 Pod，这里还有 Build Pod(构建完成之后还会存在，可以用于查看 log)，Deploy Pod(部署完成后会被销毁)

经历以上步骤，一个应用就已经在 Openshift 集群中被创建出来了。

**4** 创建 route

```
oc expose svc/nodejs-ex
```

得到执行成功的提示后，执行```oc get route```，就可以用返回的地址访问你的应用了。
这个时候你会看到在```oc get all```命令中看到有叫做`route`的资源被创建了。

**5** 清理资源

可以看到我们创建的资源都是被打上了 label 的，可以用`oc get all --show-labels=true`查看，删除资源也可以使用标签```oc delete all -l app=nodejs-example```，更粗暴的方式是直接删除整个 project，所有资源都会被清理掉：`oc delete project <project name>`










