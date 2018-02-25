---
title: 使用 Kcptun 加速 Shadowsocks
date: 2018-02-25 21:40:32
categories: 工具
tags:
- Shadowsocks
- 代理
---

在[搭建完加强版 Shadowsocks](/2017/11/03/how-to-setup-ss-on-linode/)后如果希望进一步减少延迟，或者在网络比较差的地方也想流畅使用代理，这篇文章会对你有帮助。笔者亲测可观看 Youtube 2K 视频。

# 基本思路

使用代理上网，我们发出的网络请求实际上经过了“本地->代理服务器->目标服务器”两段网络请求，后半段发生在外网的网络请求在[上一篇文章](/2017/11/03/how-to-setup-ss-on-linode/)中已经介绍过可以通过开启增强版 BBR 增加代理稳定性，这篇文章里讲到的 Kcptun 用法目标则是为了以牺牲一定带宽的方式，降低本地到代理服务器这段网络请求的延迟，以提高上网速度。

# 实现原理——Kcptun 介绍

[Kcptun](https://github.com/xtaci/kcptun) 是一个非常简单和快速的，基于 [KCP 协议](https://github.com/skywind3000/kcp)的 UDP 隧道，它可以将 TCP 流转换为 KCP+UDP 流。计算机网络课上我们学过，七层网络结构中运输层的 UDP 是一个无连接、尽最大努力的数据传输服务，优点是交付效率高、速度快；缺点是不提供可靠交付，而 KCP 是一个快速可靠协议，以选择性重复发包的方式，能以比 TCP 浪费10%-20%的带宽的代价，换取平均延迟降低 30%-40%，且最大延迟降低三倍的传输效果。

![](https://blog.kuoruan.com/wp-content/uploads/2016/06/kcptun.png)

<center>Kcptun 工作示意图 图片来自：[**扩软博客**](https://blog.kuoruan.com/)</center>

# 搭建 Kcptun

那么，这么好的工具，该怎么用呢？
Kcptun 的安装比较繁琐，万幸的是有人写成了自动安装脚本，包含了常用设置和开机自启的配置，我以[上一篇文章](/2017/11/03/how-to-setup-ss-on-linode/)中搭建的 Shadowsocks 为基础，简要介绍一下搭建过程，完整版可见[脚本作者博客](https://blog.kuoruan.com/110.html)。
登录你的 Shadowsocks 服务器，运行：

```
wget --no-check-certificate https://github.com/kuoruan/shell-scripts/raw/master/kcptun/kcptun.sh # 下载脚本
chmod +x ./kcptun.sh     # 添加执行权限
./kcptun.sh              # 运行安装
```

最后一个命令运行的是一个带交互功能的命令工具，除了“<u>需要加速的端口</u>”和“<u>Kcptun 密码</u>”需要自己定义，其他所有提示都可以直接回车使用默认值，脚本运行完后，把“<u>可使用的客户端配置文件</u>”复制下来，保存到本地，命名为 ```config.json``` 文件，并记录下服务器版本（如```20171201```），在启动客户端的时候会用到。
检查时候运行成功：

```
service supervisord status    # 服务管理服务是否正常运行
supervisorctl status kcptun   # kcptun 是否正常运行
```

查看状态分别是 ```Active```和 ```running```则说明安装成功。

# 客户端配置

以命令行的代理为例，加上 Tcptun 后，一次命令行请求的顺序就变成了：**proxychains -> ss-agent -> kcptun-agent -> kcptunu-server -> ss-server**，也就是说 GoAgent 配置的 ss-agent 不再转发到远程的 ss-server 服务器地址，而是指向本地的 kcptun 监听的地址（127.0.0.1:<监听端口>），而 kcptun-agent 服务则需要知道远程的kcptun-server 的地址和端口。kcptun-agent 的配置不需要亲自操心，已经在服务器 Setup 后记录的 ```config.json``` 文件中了，因此客户端的操作是：

## 第一步：客户端 Kcptun 配置

在 <https://github.com/xtaci/kcptun/releases> 找到对应服务器版本（在安装完成后显示）的客户端版本（```darwin```是给 OSX 的），下载并解压，我们只需要```client_darwin_amd64.sh```这一个文件，在客户端执行：

```
./client_darwin_amd64.sh -c config.json
```

看到日志输出 ```connection: ...```则说明连接成功，这个时候在服务器端运行之前用于安装 kcptun 的脚步也应该能看到客户端连上的信息：

```
./kcptun.sh log
```

## 第二步：客户端 GoAgentX 配置

将 GoAgentX 中原有配置的 ServerAdress 改成 127.0.0.1，并把 ServerProt 设置为 ```config.json```中```localaddr```所指的端口即可。
正如上文所说，现在 Shadowsocks 的服务器地址由  Kcptun 记录，ss-agent 只需要指向 Kcptun 的客户端进程即可；其他所有配置，包括 [proxychains](https://github.com/rofl0r/proxychains-ng) 和 [SwitchyOmega](chrome-extension://padekgcemlokbadohgkifijomclgjgif/options.html#!/about) 都不需要变更。这个时候再用代理上网的时候就可以看到 Kcptun 有数据流开闭的日志，说明 Kcptun 启用成功！

> * References:
> * <https://blog.kuoruan.com/102.html>
> * <https://blog.kuoruan.com/110.html>
