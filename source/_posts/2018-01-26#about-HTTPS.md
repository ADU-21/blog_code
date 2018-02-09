---
title: 细说HTTPS
date: 2018-01-26 09:41:27
categories: 学习总结
tags:
- HTTP
- HTTPS
---

最近客户公司经历了一次大的证书升级，内网许多服务之间的调用出现了证书过期或是不授信的问题。前面我写过两篇文章介绍[HTTP协议](https://www.duyidong.com/2016/03/30/HTTP%E5%8D%8F%E8%AE%AE%E8%AF%A6%E8%A7%A3/)以及[一次完整的HTTP事务是怎样一个过程](https://www.duyidong.com/2016/04/17/%E4%B8%80%E6%AC%A1%E5%AE%8C%E6%95%B4%E7%9A%84HTTP%E4%BA%8B%E5%8A%A1%E6%98%AF%E6%80%8E%E6%A0%B7%E4%B8%80%E4%B8%AA%E8%BF%87%E7%A8%8B%EF%BC%9F/)，这篇文章主要介绍一个大家日常工作中都在打交道但却不一定很熟悉的协议 —— HTTPS。

# 名词澄清

HTTPS 服务是工作在 SSL/TLS 上 的HTTP。

首先简单区分一下 HTTPS，SSL ，TLS ，OpenSSL 这四者的关系：

1. SSL：（Secure Socket Layer，安全套接字层）是在客户端和服务器之间建立一条SSL安全通道的安全协议；
2. TLS：（Transport Layer Security，传输层安全协议），用于两个应用程序之间提供保密性和数据完整性；
3. TLS的前身是SSL；
4. OpenSSL是TLS/SSL协议的开源实现，提供开发库和命令行程序；
5. HTTPS是HTTP的加密版，底层使用的加密协议是TLS。

结论：SSL/TLS 是协议，OpenSSL是协议的代码实现。Sun HotSpot 的 jdk 中提供了一个叫 keytool 的工具，有和 openssl 差不多的功能。

# 为什么我们需要 HTTPS

根据[阮一峰的SSL/TLS协议运行机制的概述](www.ruanyifeng.com/blog/2014/02/ssl_tls.html)，HTTP 通信有三大风险：

```
（1） 窃听风险（eavesdropping）：第三方可以获知通信内容。

（2） 篡改风险（tampering）：第三方可以修改通信内容。

（3） 冒充风险（pretending）：第三方可以冒充他人身份参与通信。
```

为了解决这些问题，1994年 NetScape 公司设计了 SSL 协议（Secure Sockets Layer）的1.0版，并在 1996 年得到大规模应用，1999年，互联网标准化组织 ISOC 接替 NetScape 公司，发布了 SSL 的升级版 TLS。HTTPS 在 HTTP 的基础上做到了两点：**加密（Encrypt）**和**认证（Verification）**。

# 基本工作原理

## 加密

HTTPS 的基本思路是采用[非对称加密](https://baike.baidu.com/item/%E9%9D%9E%E5%AF%B9%E7%A7%B0%E5%8A%A0%E5%AF%86%E7%AE%97%E6%B3%95)，服务器用私钥加密信息，客户端使用公钥解密，反之亦然。公钥由服务器提供，在客户端第一次请求时返回给客户端。但是这样做有两个显而易见的问题，一是因为公钥是容易获取的，任何一个客户端只要得到了公钥就可以解密传输的信息，这样做并不安全；二是非对称加密加解密速度要远远慢于对称加密，在某些极端情况下，甚至能比对称加密慢上1000倍。

正因为此，TLS 采用在客户端和服务器次握手间的三个随机数生成**“对话秘钥”**，采用对称加密加密传输信息，如下图所示：

![](/images/SSL_handshake.png)
<center>**图片来自：[Cloudfare](https://blog.cloudflare.com/keyless-ssl-the-nitty-gritty-technical-details/)**</center>

1. 在 TCP 握手完成后，客户端向服务器发送一个请求（ClientHello），携带信息包括支持协议版本（TLS 1.0）、加密方式（RSA）、压缩方法，以及一个客户端生成随机数。
2. 服务器确认使用的通信协议，如果不支持则关闭加密通信，如果支持则回应客户端请求（ServerHello），发送包括确认加密算法、压缩算法、服务器公钥，以及一个服务器端生成随机数。
3. 客户端在收到服务器确认连接的响应后，会生成第三个随机数（被称为“pre-master key”），使用上一个请求返回的公钥加密后发送给服务器。（这样做是为了方式握手期间被第三方监听），并提供前面握手的内容的 hash 值表示客户端握手结束，并用于服务器端校验。
4. 服务器端在收到客户端加密后的随机数后用私钥解密。这个时候，客户端和服务器端都同时拥有了三个在一次会话中生成的完全相同的三个随机数：客户端生成随机数、服务器端生成随机数、Pre-master key。随即客户端和服务器端用这三个数生成同一把**会话密钥**（Session key），用于加密随后通信的信息。介时，服务器端还会向客户端返回一个编码变更通知表示服务器会话结束，并提供一个前面会话的 hash 值用于客户端校验。
5. 客户端与服务器端开始对称加密通信。

## 认证

可以想象，即使做到了客户端和服务器之间通信的保密性，并不意味着就不会有人来冒充服务器端响应客户端的请求。参照网络七层协议，互联网中的设备通信只能够靠 IP 来标示对方，而因为 IP 不便记忆所以我们有了 DNS（Doman Name Service），域名和 IP 的映射除了靠 DNS 服务器记录之外还会有限去查询本地缓存，如果本地 DNS 映射的缓存被攻击者更改就会造成 DNS 劫持，许多钓鱼网站就是利用这个方式将一个域名指向的 IP 改变为钓鱼网站的服务器 IP，再做一个和官方网站一模一样的网站骗走用户的隐私信息。因此，如何确认客户端现在连接的服务器就是真正的目标服务器就变得十分重要。

要达到这个效果就要求官方网站必须去一个中心备案，客户端每次在连上服务器开始会话之前先去这个中心查阅一下该网站是不是真的就是浏览器上这个域名对应的网站，验证通过后才建立连接。这个“中心”就是 CA （Certificate Authority 证书授权中心），用于查阅校验的文件就是“证书（Certificate）”。

这个过程中涉及到两个步骤，一个是证书的签发（Signing），一个是证书的验证（Verification），如下图所示：

![](/images/Certificate_Signing_and_Verification.png)
<center>**图片出自：[什么是数字证书的证书链](https://www.pianyissl.com/support/page/10)**</center>

证书的签发：证书签发机构将服务器提供的数据经过一次 [Hash] 用 CA 的秘钥加密，再附到证书中，成为办法给服务器的证书。拿到证书的服务器会在第一次响应服务请求（ServerHello）的时候将证书发送给客户端，客户端拿到这个证书后会去请求证书签发机构，这个时候签发机构就会拿这个证书的 Hash 值和已存的证书 Hash 值对比，如果一致，则认证通过，如果不一致，你就会在流量器里看到你访问的网站不受信的错误提示，提醒用户小心访问。

除此之外，证书的认证还有一个细节，就是证书链：

![](/images/certificate_chain.png)
<center>**图片出自：[什么是数字证书的证书链](https://www.pianyissl.com/support/page/10)**</center>

一个网站的证书通常分为三级：根证书（root），中间证书（intermediate），终端证书（end-user），当用户拿到一个网站的证书，是 End-user，他会先去找这个证书 CA 的证书的颁发者（intermediate）认证 CA 的域名是否授信，intermediate 可能有很多层，最后会认证到 Root，Root 证明 intermediate 是授信的，intermediate 证明 End-user 是授信的，最后再由最后一级 CA 证明这个网站的域名是授信的。

总上所述，一次完整的 HTTPS 连接的建立，加上证书验证，应该是这样的：

![](/images/HTTPS_handshake.png)

关于这张图片的相信描述，可以参考：[图解HTTPS](http://www.cnblogs.com/zhuqil/archive/2012/07/23/2604572.html)

# 自签证书

根证书没有上层机构再为其本身作数字签名，所以都是**自签证书**，操作系统以及浏览器会预先安装可被信任的根证书，这代表用户授权了应用软件代为审核哪些根证书机构属于可靠，很多企业内部出于安全都会有内网 HTTPS 的需求，就涉及到证书自签名和授信。这里有一个完整的[教程](https://yi-love.github.io/blog/%E7%BD%91%E7%BB%9C%E5%AE%89%E5%85%A8/2017/07/15/https-ca.html)教你如何搭建一个包含自签名证书的网站，归纳一下大致分为这五步：

```
1. 准备 CA 机构所需资质：ca.key (CA机构私钥) -> ca.csr（Cerificate Signing Request， CA机构请求文件） （带机构信息）
2. CA 机构申请证书： ca.key + ca.csr -> ca.crt（CA 机构证书） 
3. 服务器准备申请所需资质：server.key -> server.csr （带机构信息）
4. CA 机构颁发给网站证书：ca.crt + ca.key + v3.ext (附带文件，记录网站有关信息) + server.csr = server.crt 
5. 客户端安装CA证书：把 ca.crt 添加到 keystore
```

# 如何部署和升级

作为面向终端用户提供服务的服务器，需要两个文件：certificate.crt 和 server.key，certificate.crt 用于向客户端发送证书及公钥，server.key 用以解密 pre-master key。以 Apache 为例，需要在配置文件中加入：（完整配置参照[这里](https://httpd.apache.org/docs/2.4/ssl/ssl_howto.html)）

```
SSLCertificateFile "/path/to/certificate.crt"
SSLCertificateKeyFile "/path/to/server.key"
```

对于后端应用，除了提供 HTTPS 服务器的角色往往还会充当客户端的角色，因此就需要把调用服务的证书 CA.crt 加入到自己的授信列表中。以 Java 为例，你需要关注 **KeyStore** 和 **TrustStore**：
从文件格式上来看，KeyStore 和 KeyStore 其实是一个东西，只是为了方便管理将其分开；Keystore可以看成一个放 key 的库，key 就是公钥，私钥，数字签名等组成的一个信息，TrustStore 中保存的是一些可信任的证书，主要是 java 在代码中访问某个 https 的时候用于对被访问者进行认证的，以确保其实可信任的。TrustStore 是必须的，如果我们没有显式的指定，那么 java 会默认指定为 $JAVA_HOME/lib/security/cacerts 这个文件。

如果要指定的话，可以在java的参数中进行指定：

```
-Djavax.net.ssl.keyStore=clientKeys   
-Djavax.net.ssl.keyStorePassword=password   
-Djavax.net.ssl.trustStore=clientTrust   
-Djavax.net.ssl.trustStorePassword=password  
```

Springboot 类似，参照[Spring Boot SSL [https] Example](https://howtodoinjava.com/spring/spring-boot/spring-boot-ssl-https-example/)。

在实际使用过程中，TrustStore 也可以被导入到 KeyStore 中成为一个文件，不过往往会有两份，一个作为 TrustStore，一个作为 KeyStore。

# 关于各式各样的证书格式

前面我们已经提到 csr（请求文件）、key （私钥文件）、jks（KeyStore文件）、crt（证书文件）几种文件类型，但我们往往还会看到证书有关的 *.pem, *.crt, *.ca-bundle, *.cer, *.p7b, *.p7s 这些文件后缀名，这些文件其实都是一个或多个证书练的压缩格式，例如 p7b 格式的文件可以在 windows 平台下展开看到证书链，并且可以直接安装在 windows 系统中。不同格式的证书包是可以互相转换的，参见：[DER vs. CRT vs. CER vs. PEM Certificates and How To Convert Them](https://support.ssl.com/Knowledgebase/Article/View/19/0/der-vs-crt-vs-cer-vs-pem-certificates-and-how-to-convert-them)

 

> * Reference: 
> * <https://yi-love.github.io/blog/%E7%BD%91%E7%BB%9C%E5%AE%89%E5%85%A8/2017/07/15/https-ca.html>
> * <http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html>
> * <https://www.pianyissl.com/support/page/10>
> * <https://support.dnsimple.com/articles/what-is-ssl-certificate-chain/>
> * <http://blog.csdn.net/chenzanlong123/article/details/11784143>
> * https://helpdesk.ssls.com/hc/en-us/articles/204093372-What-are-certificate-formats-and-what-is-the-difference-between-them-
