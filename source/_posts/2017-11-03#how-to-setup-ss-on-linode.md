---
title: 如何在 Linode 上搭建加强版 Shadowsocks 
date: 2017-11-03 10:04:05
categories: 工具
tags:
- 云服务
- 代理
---

 本文介绍如何在 Linode 上搭建 Shadowsocks，并开启 TCP BBR 拥塞算法达到稳定代理的目的。

# 为什么选用 Linode

- **便宜** 一台最小的 Linode 服务器(单CPU/1G内存/20G硬盘/1TB流量) 只需 5$ 一月，1TB 的流量只要不被攻击基本可以放心使用而不用操心流量超的问题。
- **方便** 相比于 AWS, Linode 的服务器资源申请和维护很简单，不需要你具备太多的平台知识，从这个角度来说定制化的反面就是易用性。付费也很简单，一台机器就是一台机器的钱，没有很多隐藏收费的地方。除此之外，机器重启公网 IP 也不会变，不需要像 AWS 一样还有绑定 EIP。你可能会担心这台服务器 IP 被封，Linode 有服务器 clone 功能可以很快克隆一台一模一样的服务器（with different public ip）。
- **速度** 亲测按照本文配置的 ss 外网下载速度可达 4MB/s，Youtube 1080p 可以说是无压力了。

# 注册/申请服务器

## 注册账号

申请注册，邀请码：<https://www.linode.com/?r=1edbed5f98d2e15d9866e174f7d6653dbe63a0bf>

你需要准备一张支持美元的信用卡，并在注册时预先扣费 5$，接下来注册邮箱会收到一封确认邮件，确认后还有等一段时间的人工 Review, 大概半小时后账号就可用了。

## 申请服务器

- **Add a Linode** 创建服务器
- **Select an instance type** 因为这里只做流量转发，所以选最小的类型:1024
- **Location** 服务器位置选新加坡或日本（注意服务器位置并不等于 IP 地区）
- 点击 **Add This Linode**

![](/images/linode_create_instance.jpg)

## 配置操作系统镜像

- 回到 Linodes 界面，点选 **Deploy Images**
- 选择交换分区大小保持默认，应用操作系统镜像 **Ubuntu 17.04**，输入密码（服务器被创建）
- Boot 服务器

接下来可以再 Linodes 界面看到服务器的公网 IP，可以通过 ssh 登录服务器了。

```
ssh root@<public-ip>
```

## 禁用密码登录（可选）

密码可以被猜测到，所以为了安全性通常会禁用密码登录而改用秘钥对登录。

首先将本地的公钥放到 Linode 服务器上：

```
ssh-copy-id root@<public-ip>
```

然后禁用服务器上的密码登录，方法是编辑 Linode 服务器上的 ```/etc/ssh/sshd_config```文件，设置```PasswordAuthentication no```，再重启 sshd ```service sshd restart```

# 搭建 Shadowsocks

## 安装 pip

登录到服务器上，依次执行以下命令：

```
apt update -y           # apt 更新
apt install python3-pip # 安装 pip3
```

## 安装并开启 shadowsocks

``` 
pip3 install shadowsocks              # 安装 Shadowsocks
ssserver -p 8388 -k PassWord -d start # 启动 Shadowsocks, 8388是代理端口，PassWord为连接口令，-d 表示后台启动，看到 Started 即表示启动成功
```

## Trouble shooting 

### apt update 很慢

如果遇到 ```apt update``` 特别慢或者出现 ipv6 地址无法解析，可以参照[这篇博客](http://www.wuce.org/disable-linode-ipv6-solve-google-error/) 禁用 IPV6。

再不行就把 ```/etc/apt/sources.list``` 里的```http://security.ubuntu.com/ubuntu```源（最后三行）给注释掉

# 安装客户端

## 系统配置

- Mac客户端： [**goAgentX**](/software/GoAgentX.zip) ，解压后放到 ```Application``` 目录里，启动。
- Windows: [**Shadowsocks-4.0.6.zip**](https://github.com/shadowsocks/shadowsocks-windows/releases/download/4.0.6/Shadowsocks-4.0.6.zip), 解压后运行。

配置：

![](http://7q5cfr.com1.z0.glb.clouddn.com/kxsw3.png)

Windows 和 Mac 的客户端的配置是一样的，原理是把本机从代理端口出去的流量全部转发到 Shadowsocks 服务器，配置五个参数：

- 本地端口：本地代理端口
- 服务器地址：Linode 服务器公网 IP
- 端口：启动 Shadowsocks server 时的端口
- 密码：启动 Shadowsocks server 时的密码
- 加密方式：默认为 ```aes-256-cfb```

启动，看到 ```server listening at port xxx…``` 就表示启动成功了。

goAgentX 有两种可选项目，一种是全局代理（Global Proxy Model），一种是局部代理（Stand alone Model）。全局代理是将服务器出去的流量全部转发到代理服务器，局部代理是需要将本机流量先转发到代理端口才能启用代理。一般建议配置局部代理，这样操控性更强。

## Chrome 配置

使用插件[**SwitchyOmega**](https://chrome.google.com/webstore/search/SwitchyOmega)，

- 添加默认代理： SOCKS5/localhost/<代理端口>
- 配置转发规则（如图所示），<https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt> 是一个自动更新的 GFW 白名单，文本里用```非```语句表示白名单里的站点可不通过代理访问，这样就避免了代理访问国内网站速度变慢的问题。

![](/images/SwitchOmega_configure.png)

使用方法，在插件的下拉菜单中选择```auto switch```，访问 Google，测试通过。

## Shell 配置

使用环境变量可以将 Shell 的所有流量转发到代理端口，需要始终生效可以将以下命令加入 ```.xshrc```:

```
export http_proxy=http://localhost:<代理端口>
export https_proxy=https://localhost:<代理端口>
```

```ping google.com``` 测试通过。

# 开启 TCP BBR 增强版

这部分内容来自[Debian/Ubuntu 开启 TCP BBR 拥塞算法](https://moeclub.org/2017/06/06/249/)，[Debian/Ubuntu TCP BBR 改进版/增强版](https://moeclub.org/2017/06/24/278/)，BBR 算法介绍可以参考[知乎：Linux Kernel 4.9 中的 BBR 算法与之前的 TCP 拥塞控制相比有什么优势？](https://www.zhihu.com/question/53559433)，太长不读版就是通过重复发包来减少流量转发过程中的丢包现象从而使代理服务更加稳定。这个功能需要 Linux 内核支持，具体操作如下：

## 修改 Linode 服务器的 Boot Kernel

将 Linode 服务器管理界面中 **Boot Settings** 部分的 **Kernel**设置为 ```GRUB2``` 

![](/images/Linode_kernal_change.png)

## 开启 TCP BBR

在 Linode 服务器上运行：

```
wget --no-check-certificate -qO 'BBR.sh' 'https://moeclub.org/attachment/LinuxShell/BBR.sh' && chmod a+x BBR.sh && bash BBR.sh -f  v4.11.9
```

期间服务器会重启一次，如果你没有给 ss 配置自动重启则需要手动起一下。

## 增强 TCP BBR 性能

在 Linode 服务器上运行：

```
wget --no-check-certificate -qO 'BBR_POWERED.sh' 'https://moeclub.org/attachment/LinuxShell/BBR_POWERED.sh' && chmod a+x BBR_POWERED.sh && bash BBR_POWERED.sh -f v4.11.9
```

提示 Success 则表示安装成功，Trouble Shooting 可以参考[这篇文章](https://moeclub.org/2017/06/24/278/)

> 如果你完成了上面所有操作，那么恭喜你，可以享受畅通无阻的翻墙体验了。

# 一点题外话