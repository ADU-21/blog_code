---
title: 基础设施代码测试
date: 2017-04-13 13:41:29
tags:
- Infrastructure as Code
- ThoughtWorks技术雷达
- 基础设施即代码
- 持续交付
---

在DevOps的文化中有一个已经被广泛认可的概念叫做[基础设施及代码](https://martinfowler.com/bliki/InfrastructureAsCode.html)，意在将基础设施以配置文件的方式纳入版本管理以达到更灵活且便于管理的目的，从而更加适应基础设施频繁变更需求。

虽说此举俨然已将[CI/CD](https://www.duyidong.com/2016/05/19/CI-CD/)（持续集成和持续交付）概念应用在了基础设施代码上，然而在应用产品的[持续部署与发布](https://www.duyidong.com/tags/%E9%83%A8%E7%BD%B2%E4%B8%8E%E5%8F%91%E5%B8%83/)实施过程中，基础设施变更仍然是一件令人*胆战心惊*的事，原因很简单——没有**反馈及时**且**覆盖充分**的测试。

随着基础设施即代码的不断推广，这一痛点变得越来越明显，最近一期[ThoughtWorks技术雷达](https://assets.thoughtworks.com/assets/technology-radar-vol-16-cn.pdf)中列出了两个工具**Molecule**和**Testinfra**一个是用于测试Ansible Role(如果还不知道Ansible是什么请移步我的另一篇博客：[Ansible学习小计](https://www.duyidong.com/2016/06/15/Ansible%E5%AD%A6%E4%B9%A0%E5%B0%8F%E8%AE%B0/))，一个用于测试基础设施，下面我就来试用一下这两个工具。

## Testinfra介绍

[Testinfra](https://github.com/philpep/testinfra)是由Python社区贡献的基础设施实际状态自动化测试工具，其目标是成为[Serverspec](http://serverspec.org/)在Python中的等价物，并且作为**Pytest**测试引擎的插件来使用。

官方文档：<http://testinfra.readthedocs.io/en/latest/index.html>

### Quick Start

环境准备：

```
$ pip install testinfra      # 安装Testinfra
$ pip install paramiko       # Python的SSH支持以执行远程测试
$ pip install pytest-xdist   # 添加多线程支持
```

来感受下Testinfra的写法:

```python
def test_passwd_file(File):         # 定义文件检查
    passwd = File("/etc/passwd")    # 定义文件
    assert passwd.contains("root")  # 文件是否包含关键字”root“
    assert passwd.user == "root"    # 文件owner是否为root
    assert passwd.group == "root"   # 文件所属组是否为root
    assert passwd.mode == 0644      # 检查文件权限


def test_nginx_is_installed(Package):
    nginx = Package("nginx")
    assert nginx.is_installed       # nginx是否安装
    assert nginx.version.startswith("1.10") # 检查nginx版本


def test_nginx_running_and_enabled(Service):
    nginx = Service("nginx")
    assert nginx.is_running         # 检查nginx是否已启动
    assert nginx.is_enabled         # nginx是否配置开机自启
```
基本和Python的单元测试一模一样，下面来看运行结果：

```
$ testinfra -v test_myinfra.py
```

![](/images/Testinfra_local_success.png)

报错也是PyTest的尿性：

![](/images/Testinfra_local_fail.png)

### 远程测试虚拟机

通过配置ssh并在启动时加入如下参数：

```
# -n 为指定并发数 --connection为指定链接方式
$ testinfra -n 1 -v --host="hostname" --connection=ssh test_myinfra.py
```
感觉执行方式和Ansible有些相似，以下为执行结果：

![](/images/Testinfra_remote_success.png)

耗时6s，中规中矩吧。

### 其他功能

除了SSH协议链接Testinfra还支持paramiko，docker，salt，kubectl等连接方式，对于Docker的测试，无外乎也是在起起来的容器上使用```docker exec```的方法对基础设施即环境配置进行验证。

另外Testinfra支持自定义module的形式保证测试方法的灵活性，同时Testinfra可以再PyTest之外用于调用SSH连接从而进行测试，形如：

```
>>> import testinfra
>>> conn = testinfra.get_backend("paramiko://root@server:2222", sudo=True)
>>> conn.File("/etc/shadow").mode == 0640
True
```

### Testinfra评价

#### 优点

 - 基本涵盖了应用所需环境测试的内容，且可灵活扩展。
 - 可读性好，学习成本低.
 - 环境依赖简单，能够与项目完美结合。

#### 缺点

 - 测试未能覆盖到IaaS层的测试，仅能够在操作系统之上进行测试。对于云环境的基础设施还包括网络，负载均衡，数据库等等资源，这些都不能照顾到。
 - 测试覆盖率虽然有所提升，但反馈不够及时。在开发和Debug过程中最痛苦的应该是每次使用新代码创建基础设施都要等很久才能得到反馈，这个问题并没有得到解决。
 - 报错信息不够友好，个人认为基础设施的测试应该不能等同于代码层面的测试，所以PyTest的报错方式并不能让用户一眼就看到是哪方面出了错误；往远了说，要根据这个报错信息Trigger一个行为也很难做到。

## Molecule介绍

[MOLECULE](https://github.com/metacloud/molecule)旨在帮助开发和测试Ansible的Role。通过在虚拟机或容器上为正在运行的Ansible Role的测试构
建脚手架，我们无需再手工创建这些测试环境。Molecule利用Vagrant，Docker和OpenStack来管理虚拟机或容器，并支持Serverspec、Testinfra或Goss来运行测试。在sequence facility model中的默认步骤包括：虚拟机管理，Ansible语法静态检查，幂等性测试和收敛性测试。

话不多说，我们来看看这个工具究竟能解决什么样的问题吧。

### 版本支持

目前Molecule支持的Ansible版本为```2.1.4.0```和```2.2.1.0```，好吧，这一点，虽然项目上还在用```1.7.2```（一直担心*Breake change*所以没有升上去），但我觉得大部分公司应该使用的是```2.2```以上的版本，这一点我没有异议。

### Quick Start

环境准备

```
$ yum install ansible -y      # 为了避免踩坑不使用pip的ansible
$ pip install docker          # 安装python的docker支持
$ pip install molecule        # 主角登场
```
运行

```
$ molecule init --role foo --driver docker
$ cd foo
$ molecule test
```

运行结果：

![](/images/Molecule_Test_Success.png)

第一次运行速度比较慢，因为运行Ansible需要到Docker镜像仓库拉取一个Ubuntu的镜像，可以从Log里看出，整个测试过程包含如下步骤：

 - 从基础镜像（Ubuntu）创建一个测试需要的镜像（docker image）
 - 从镜像启动一个测试运行需要的容器（docker container）
 - 开始测试，运行playbook
 - 使用Testinfra判断准备环境是否正确
 - 测试完毕，停止并删除容器

整个过程容器的创建和销毁并没有占用很多时间，和跑一个Ansible时间相差不多。

### 配置文件

Molecule的配置文件采用的yml，可以在项目下也可以放到```~/.config/molecule/config.yml```作为默认配置，示例如下：

```
---
dependency:
  name: galaxy         # Molecule使用Ansible Galaxy拉取Role
driver:
  name: docker         # 使用Docker引擎跑测试
docker:
  containers:          # 容器配置
    - name: foo
      image: ubuntu
      image_version: latest
      ansible_groups:  # 目标机器组
        - group1
verifier:
  name: testinfra      # 验证最终执行结果
```
其中Driver除了Docker还支持OpenStack及Vagrant，Verifier除了Testinfra还可选Serverspec及Goss。

### 测试方法

关于测试内容的定义，看一下```test/test_default.py```文件：

```
import testinfra.utils.ansible_runner

testinfra_hosts = testinfra.utils.ansible_runner.AnsibleRunner(
    '.molecule/ansible_inventory').get_hosts('all')


def test_hosts_file(File):
    f = File('/etc/hosts')

    assert f.exists
    assert f.user == 'root'
    assert f.group == 'root'
```
简单看来就是利用Testinfra看了下/etc/hosts是否存在，所属用户和所属组是否为root

### Molecule评价

与其说Molecule是一个新的Ansible测试工具不如说是一种组合docker，Testinfra和Ansible的测试方法，换总说法它并不是一个集成在Ansible之中的更细粒度的测试方法，而是高于Ansible的更适合于放在Pipeline中作为测试Ansible运行最终结果的一个工具集，其效果大致和运行```docker run XXX ansible```之后再运行```Testinfra -v```两行shell命令是一样的

#### 优点

 - 开始关注自动化配置脚本的测试，不失为一种尝试
 - 使用Docker作为引擎，速度还可以
 - 能够提升更改Ansible脚本后的构建信心

#### 缺点

 - 仍然要运行完Playbook才能看到结果，并没有解决**反馈不及时**的痛点
 - 不支持细粒度的测试，且对一个playbook使用不同参数构建不同环境的应用场景也没有很好的支持
 - 不管是使用Docker还是Vagrant，都增加了*空间*的消耗，且增加了*知识成本*

## 综上所述

随着DevOps的普及，已经有人开始关注基础设施代码测试这一块的工作，但这些测试工具还在初级阶段，反馈不能及时，增加覆盖面的成本也还较高，且不能很好地与云环境结合。
由于在软件开发中基础设施对业务价值的贡献并不明显，提升Ops技能与其说能过帮助公司挣钱不如说是帮公司省钱；而且在大部分项目中基础设施变更并没有那么频繁，紧急的问题通常还是由Ops（或者是被称为DevOps的一个角色）手动来解决，这种现象在国内公司尤其明显。大家对DevOps的认识还没有达到不一致，DevOps中的测试也还没有被定义清楚，基础设施代码的测试，还有很长的路要走。
