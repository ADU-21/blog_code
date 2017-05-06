---
title: HTTP协议详解
date: 2016-03-30 21:02:31
categories:
- 学习总结
tags: HTTP
---

这是一篇想写了很久却迟迟没有动笔的题目。
从开始接触爬虫，或者说接触web开始，你就是在和http协议打交道了，我想web这个职业只要http协议不过时应该就不会有太大的变化。
<!-- more -->
好，下面是正文:

----
先说说HTTP协议是怎样工作的：
步骤1：浏览器首先向服务器发送HTTP请求，请求包括：

方法：GET还是POST，GET仅请求资源，POST会附带用户数据；

路径：/full/url/path；

域名：由Host头指定：Host: www.sina.com.cn

以及其他相关的Header；

如果是POST，那么请求还包括一个Body，包含用户数据。

步骤2：服务器向浏览器返回HTTP响应，响应包括：

响应代码：200表示成功，3xx表示重定向，4xx表示客户端发送的请求有错误，5xx表示服务器端处理时发生了错误；

响应类型：由Content-Type指定；

以及其他相关的Header；

通常服务器的HTTP响应会携带内容，也就是有一个Body，包含响应的内容，网页的HTML源码就在Body中。

步骤3：如果浏览器还需要继续向服务器请求其他资源，比如图片，就再次发出HTTP请求，重复步骤1、2。

Web采用的HTTP协议采用了非常简单的请求-响应模式，从而大大简化了开发。当我们编写一个页面时，我们只需要在HTTP请求中把HTML发送出去，不需要考虑如何附带图片、视频等，浏览器如果需要请求图片和视频，它会发送另一个HTTP请求，因此，一个HTTP请求只处理一个资源。

HTTP协议同时具备极强的扩展性，虽然浏览器请求的是 http://www.sina.com.cn/ 的首页，但是新浪在HTML中可以链入其他服务器的资源，比如
```
<img src="http://i1.sinaimg.cn/home/2013/1008/U8455P30DT20131008135420.png">
```

，从而将请求压力分散到各个服务器上，并且，一个站点可以链接到其他站点，无数个站点互相链接起来，就形成了World Wide Web，简称WWW。


----

## 超文本传输协议
http就是用于规范客户端和服务器之间文本传输的一个协议，我们在访问网站的时候大多数时候都是通过这个协议从服务器上获取想要的数据的。
### 从get方法和post方法谈起
最早让我想要弄清楚HTTP协议是源于我在写爬虫的时候使用python的requests模块，经常会弄不清楚到底该用get方法还是post方法，比较通俗的说法是post方法比较安全，可以带data，get方法就是url请求，请求体全部都暴露在url里面，较不安全，然而研究后发现，HTTP的四种方法get,post,put,delete方法并没有本质上的区别，协议本身并没有强制规范什么情况下必须使用什么请求，事实上这几种请求方法的格式是一样的。
### 请求方法
请求方法（所有方法全为大写）有多种，各个方法的解释如下：

	GET     请求获取Request-URI所标识的资源
	POST    在Request-URI所标识的资源后附加新的数据
	HEAD    请求获取由Request-URI所标识的资源的响应消息报头
	PUT     请求服务器存储一个资源，并用Request-URI作为其标识
	DELETE  请求服务器删除Request-URI所标识的资源
	TRACE   请求服务器回送收到的请求信息，主要用于测试或诊断
	CONNECT 保留将来使用
	OPTIONS 请求查询服务器的性能，或者查询与资源相关的选项和需求

## header
### General Header（通用头）
HTTP里的通用头既可以包含在请求头中也可以包含在响应头中，作用主要是描述HTTO协议本身，下面举例几个常见的通用头：
#### Cache-Control头域
Cache-Control指定请求和响应遵循的缓存机制。在请求消息或响应消息中设置Cache-Control并不会修改另一个消息处理过程中的缓存处理过程。请求时的缓存指令包括no-cache、no-store、max-age、max-stale、min-fresh、only-if-cached，响应消息中的指令包括public、private、no-cache、no-store、no-transform、must-revalidate、proxy-revalidate、max-age。各个消息中的指令含义如下：
> * Public指示响应可被任何缓存区缓存。
> * Private指示对于单个用户的整个或部分响应消息，不能被共享缓存处理。这允许服务器仅仅描述当用户的部分响应消息，此响应消息对于其他用户的请求无效。
> * no-cache指示请求或响应消息不能缓存
> * no-store用于防止重要的信息被无意的发布。在请求消息中发送将使得请求和响应消息都不使用缓存。
> * max-age指示客户机可以接收生存期不大于指定时间（以秒为单位）的响应。
> * min-fresh指示客户机可以接收响应时间小于当前时间加上指定时间的响应。
> * max-stale指示客户机可以接收超出超时期间的响应消息。如果指定max-stale消息的值，那么客户机可以接收超出超时期指定值之内的响应消息。

#### HTTP Keep-Alive
Keep-Alive功能使客户端到服务器端的连接持续有效，当出现对服务器的后继请求时，Keep-Alive功能避免了建立或者重新建立连接。市场上的大部分Web服务器，包括iPlanet、IIS和Apache，都支持HTTP Keep-Alive。对于提供静态内容的网站来说，这个功能通常很有用。但是，对于负担较重的网站来说，这里存在另外一个问题：虽然为客户保留打开的连接有一定的好处，但它同样影响了性能，因为在处理暂停期间，本来可以释放的资源仍旧被占用。当Web服务器和应用服务器在同一台机器上运行时，Keep- Alive功能对资源利用的影响尤其突出。
KeepAliveTime 值控制 TCP/IP 尝试验证空闲连接是否完好的频率。如果这段时间内没有活动，则会发送保持活动信号。如果网络工作正常，而且接收方是活动的，它就会响应。如果需要对丢失接收方敏感，换句话说，需要更快地发现丢失了接收方，请考虑减小这个值。如果长期不活动的空闲连接出现次数较多，而丢失接收方的情况出现较少，您可能会要提高该值以减少开销。缺省情况下，如果空闲连接 7200000 毫秒（2 小时）内没有活动，Windows 就发送保持活动的消息。通常，1800000 毫秒是首选值，从而一半的已关闭连接会在 30 分钟内被检测到。 KeepAliveInterval 值定义了如果未从接收方收到保持活动消息的响应，TCP/IP 重复发送保持活动信号的频率。当连续发送保持活动信号、但未收到响应的次数超出 TcpMaxDataRetransmissions 的值时，会放弃该连接。如果期望较长的响应时间，您可能需要提高该值以减少开销。如果需要减少花在验证接收方是否已丢失上的时间，请考虑减小该值或 TcpMaxDataRetransmissions 值。缺省情况下，在未收到响应而重新发送保持活动的消息之前，Windows 会等待 1000 毫秒（1 秒）。 KeepAliveTime 根据你的需要设置就行，比如10分钟，注意要转换成MS。 XXX代表这个间隔值得大小。
#### Date头域
Date头域表示消息发送的时间，时间的描述格式由rfc822定义。例如，Date:Mon,31Dec200104:25:57GMT。Date描述的时间表示世界标准时，换算成本地时间，需要知道用户所在的时区。
#### Pragma头域
Pragma头域用来包含实现特定的指令，最常用的是Pragma:no-cache。在HTTP/1.1协议中，它的含义和Cache-Control:no-cache相同。
### Entity header(实体头)
请求消息和响应消息都可以包含实体信息，实体信息一般由实体头域和实体组成。实体头域包含关于实体的原信息，实体头包括Allow、Content-Base、Content-Encoding、Content-Language、Content-Length、Content-Location、Content-MD5、Content-Range、Content-Type、Etag、Expires、Last-Modified、extension-header。extension-header允许客户端定义新的实体头，但是这些域可能无法被接受方识别。实体可以是一个经过编码的字节流，它的编码方式由Content-Encoding或Content-Type定义，它的长度由Content-Length或Content-Range定义。
#### Content-Type实体头
Content-Type实体头用于向接收方指示实体的介质类型，指定HEAD方法送到接收方的实体介质类型，或GET方法发送的请求介质类型
#### Content-Range实体头
Content-Range实体头用于指定整个实体中的一部分的插入位置，他也指示了整个实体的长度。在服务器向客户返回一个部分响应，它必须描述响应覆盖的范围和整个实体长度。一般格式：
Content-Range:bytes-unitSPfirst-byte-pos-last-byte-pos/entity-legth
例如，传送头500个字节次字段的形式：Content-Range:bytes0-499/1234如果一个http消息包含此节（例如，对范围请求的响应或对一系列范围的重叠请求），Content-Range表示传送的范围，Content-Length表示实际传送的字节数。
#### Last-modified实体头
Last-modified实体头指定服务器上保存内容的最后修订时间。
例如，传送头500个字节次字段的形式：Content-Range:bytes0-499/1234如果一个http消息包含此节（例如，对范围请求的响应或对一系列范围的重叠请求），Content-Range表示传送的范围，Content-Length表示实际传送的字节数。
### HTTP Request Header（请求头）
这就是我们最常见的header了，请求报头允许客户端向服务器端传递请求的附加信息以及客户端自身的信息。
以下为常见的请求报头：
#### Accept
Accept请求报头域用于指定客户端接受哪些类型的信息。eg：Accept：image/gif，表明客户端希望接受GIF图象格式的资源；Accept：text/html，表明客户端希望接受html文本。

#### Accept-Charset
Accept-Charset请求报头域用于指定客户端接受的字符集。eg：Accept-Charset:iso-8859-1,gb2312.如果在请求消息中没有设置这个域，缺省是任何字符集都可以接受。

#### Accept-Encoding
Accept-Encoding请求报头域类似于Accept，但是它是用于指定可接受的内容编码。eg：Accept-Encoding:gzip.deflate.如果请求消息中没有设置这个域服务器假定客户端对各种内容编码都可以接受。

#### Accept-Language
Accept-Language请求报头域类似于Accept，但是它是用于指定一种自然语言。eg：Accept-Language:zh-cn.如果请求消息中没有设置这个报头域，服务器假定客户端对各种语言都可以接受。

#### Authorization
Authorization请求报头域主要用于证明客户端有权查看某个资源。当浏览器访问一个页面时，如果收到服务器的响应代码为401（未授权），可以发送一个包含Authorization请求报头域的请求，要求服务器对其进行验证。

#### Host（发送请求时，该报头域是必需的）
Host请求报头域主要用于指定被请求资源的Internet主机和端口号，它通常从HTTP URL中提取出来的，eg：
我们在浏览器中输入：http://www.guet.edu.cn/index.html
浏览器发送的请求消息中，就会包含Host请求报头域，如下：
Host：www.guet.edu.cn
此处使用缺省端口号80，若指定了端口号，则变成：Host：www.guet.edu.cn:指定端口号
#### Referer头域
Referer头域允许客户端指定请求uri的源资源地址，这可以允许服务器生成回退链表，可用来登陆、优化cache等。他也允许废除的或错误的连接由于维护的目的被追踪。如果请求的uri没有自己的uri地址，Referer不能被发送。如果指定的是部分uri地址，则此地址应该是一个相对地址。

#### User-Agent
我们上网登陆论坛的时候，往往会看到一些欢迎信息，其中列出了你的操作系统的名称和版本，你所使用的浏览器的名称和版本，这往往让很多人感到很神奇，实际上，服务器应用程序就是从User-Agent这个请求报头域中获取到这些信息。User-Agent请求报头域允许客户端将它的操作系统、浏览器和其它属性告诉服务器。不过，这个报头域不是必需的，如果我们自己编写一个浏览器，不使用User-Agent请求报头域，那么服务器端就无法得知我们的信息了。

请求报头举例：

```
GET /form.html HTTP/1.1 (CRLF)
Accept:image/gif,image/x-xbitmap,image/jpeg,application/x-shockwave-flash,application/vnd.ms-excel,application/vnd.ms-powerpoint,application/msword,*/* (CRLF)
Accept-Language:zh-cn (CRLF)
Accept-Encoding:gzip,deflate (CRLF)
If-Modified-Since:Wed,05 Jan 2007 11:21:25 GMT (CRLF)
If-None-Match:W/"80b1a4c018f3c41:8317" (CRLF)
User-Agent:Mozilla/4.0(compatible;MSIE6.0;Windows NT 5.0) (CRLF)
Host:www.guet.edu.cn (CRLF)
Connection:Keep-Alive (CRLF)
(CRLF)

```

## body
body包涵请求的实体。


## Response
### 状态码
响应消息的第一行为下面的格式：
HTTP-VersionSPStatus-CodeSPReason-PhraseCRLF
HTTP-Version表示支持的HTTP版本，例如为HTTP/1.1。Status-Code是一个三个数字的结果代码。Reason-Phrase给Status-Code提供一个简单的文本描述。Status-Code主要用于机器自动识别，Reason-Phrase主要用于帮助用户理解。Status-Code的第一个数字定义响应的类别，后两个数字没有分类的作用。第一个数字可能取5个不同的值：
1xx:信息响应类，表示接收到请求并且继续处理
2xx:处理成功响应类，表示动作被成功接收、理解和接受
3xx:重定向响应类，为了完成指定的动作，必须接受进一步处理
4xx:客户端错误，客户请求包含语法错误或者是不能正确执行
5xx:服务端错误，服务器不能正确执行一个正确的请求
### 响应头
响应头域允许服务器传递不能放在状态行的附加信息，这些域主要描述服务器的信息和Request-URI进一步的信息。响应头域包含Age、Location、Proxy-Authenticate、Public、Retry-After、Server、Vary、Warning、WWW-Authenticate。对响应头域的扩展要求通讯双方都支持，如果存在不支持的响应头域，一般将会作为实体头域处理。
### 典型的响应消息：

```
HTTP/1.0200OK
Date:Mon,31Dec200104:25:57GMT
Server:Apache/1.3.14(Unix)
Content-type:text/html
Last-modified:Tue,17Apr200106:46:28GMT
Etag:"a030f020ac7c01:1e9f"
Content-length:39725426
Content-range:bytes55******/40279980
上例第一行表示HTTP服务端响应一个GET方法。棕色的部分表示响应头域的信息，绿色的部分表示通用头部分，红色的部分表示实体头域的信息。
```
#### Location响应头
Location响应头用于重定向接收者到一个新URI地址。
#### Server响应头
Server响应头包含处理请求的原始服务器的软件信息。此域能包含多个产品标识和注释，产品标识一般按照重要性排序。
## 实体信息
请求消息和响应消息都可以包含实体信息，实体信息一般由实体头域和实体组成。实体头域包含关于实体的原信息，实体头包括Allow、Content-Base、Content-Encoding、Content-Language、Content-Length、Content-Location、Content-MD5、Content-Range、Content-Type、Etag、Expires、Last-Modified、extension-header。extension-header允许客户端定义新的实体头，但是这些域可能无法被接受方识别。实体可以是一个经过编码的字节流，它的编码方式由Content-Encoding或Content-Type定义，它的长度由Content-Length或Content-Range定义。
### Content-Type实体头
Content-Type实体头用于向接收方指示实体的介质类型，指定HEAD方法送到接收方的实体介质类型，或GET方法发送的请求介质类型
### Content-Range实体头
Content-Range实体头用于指定整个实体中的一部分的插入位置，他也指示了整个实体的长度。在服务器向客户返回一个部分响应，它必须描述响应覆盖的范围和整个实体长度。一般格式：
Content-Range:bytes-unitSPfirst-byte-pos-last-byte-pos/entity-legth
例如，传送头500个字节次字段的形式：Content-Range:bytes0-499/1234如果一个http消息包含此节（例如，对范围请求的响应或对一系列范围的重叠请求），Content-Range表示传送的范围，Content-Length表示实际传送的字节数。
### Last-modified实体头
Last-modified实体头指定服务器上保存内容的最后修订时间。
例如，传送头500个字节次字段的形式：Content-Range:bytes0-499/1234如果一个http消息包含此节（例如，对范围请求的响应或对一系列范围的重叠请求），Content-Range表示传送的范围，Content-Length表示实际传送的字节数。

```熟悉HTTP是web工程师的基本功```
