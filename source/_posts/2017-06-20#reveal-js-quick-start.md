---
title: 如何制作在线幻灯片
date: 2017-06-20 16:20:33
categories: 工具
tags:
- 工具
- "reveal js"
- 在线幻灯片
---

都说不会做幻灯片的程序员不是好架构师，工作中有很多场合需要用到幻灯片。最近在准备一个对外分享，内容比较简单，顺手尝试了一下使用 `reveal js`制作一个在线幻灯片。

效果展示：<https://cdn.rawgit.com/ADU-21/cd-in-cloud-reveal-js/784e1a49/index.html>

这个幻灯片中包含文本、图片、视频，下面我会逐一讲解是如何实现的。

# 价值

- 解决微软的 PowerPoint 和 Mac keynote 跨平台不兼容问题
- 传统幻灯片需要拷来拷去，在线幻灯片只需要一个 URL 就可以访问，避免忘带 U 盘的尴尬
- 在线幻灯片本质上是 html 文件，也可以在无网络环境播放

# 用到的资源

- [reveal js](http://lab.hakim.se/reveal-js/#/) ：将幻灯片文本内容渲染为 html
- [github](https://github.com/) : 用于存放幻灯片文件
- [rawgit](http://rawgit.com/) : 将 GitHub 中的 html 正常加载，并添加 CDN 

# 开始

## 第一步： Fork 官方 Repo

前往<https://github.com/hakimel/reveal.js> 点击 `fork`将代码复制到自己账号下。

```
git clone https://github.com/<you account id>/reveal.js 
```

这时你就得到了一个可运行的 reveal js 模板，运行根目录下的 index.html 即可预览。

[README](https://github.com/hakimel/reveal.js/blob/master/README.md) 中提供了很多配置编辑的方式，你要是没耐心看就跟着我继续吧。

## 第二步： 编辑 index.html 

找到```<div class="slides">``` ，这里是幻灯片正文的开始，在这个标签中添加如下代码，开启 `markdown`编辑模式：

```
<section data-markdown data-separator="---" data-separator-vertical="--">
	<script type="text/template">
	### 正文在此
	</script>
</section>
```

现在你可使用 markdown 语法开始编辑幻灯片内容了。

### 基本编辑方式

`---` 表示横向分页。

`--` 表示竖向分页，通常一列可以作为一个章节。

`# 标题` `- 列表`

### 插入图片

插入图片有两种方式，基本的插入 markdown 语法就可以支持：

`![](https://hostname.com/image.png)` 外部引用。

考虑到一些时候我们需要在无网络的情况下播放 幻灯片，所以通常还是将图片保存到本地再添加。

`![](images/image.png)` 这种方式需要在根目录下创建一个叫 image 的文件夹，把图片放入其中命名为 imag.png。

如果要对图片又更复杂的支持，比如调整长宽，则需要写 html 代码，如：

`<img src="images/image.jpg" alt="" width="500px">`

### 插入视频

以 youtube 视频为例，首先使用 youtube-dl 下载视频：

```
brew install youtube-dl                     # 安装 youtube-dl
youtube-dl https://www.youtube.com/watch?v=oHg5SJYRHA0 
```

下载视频默认格式为 webm 格式，这是一种支持 HTML5 的视频文件格式，可以使用浏览器打开，将视频添加到幻灯片中：

```
<section data-background-video="your_video_name.webm">
```

幻灯片 播放到这一页的时候就会开始自动播放视频。

## 第三步： 对外访问

将编辑好后的 reveal js 这个项目提交到 GitHub 上：

```
git add .
git commit -m "add my slides"
git push origin master
```

从网页访问你的 repo，以示例中的幻灯片为例： <https://github.com/ADU-21/cd-in-cloud-reveal-js/blob/master/index.html> ，我们发现幻灯片并没有被正常渲染，而是以源代码的方式展示。这个时候就需要用到第三方工具将 github 代码库中的 html 文件按照期望的方式对外暴露使其访问，具体操作：

- 打开 <http://rawgit.com/>
- 在中间的输入框输入 `https://github.com/<your github id>/cd-in-cloud-reveal-js/blob/master/index.html`
- 得到 `production url` 和 `development url`两个链接

Production url 是带有 CDN 的，和 github 的同步会存在一定的延迟， developement url 的更新会及时一些，不过访问速度相对会没那么快。

现在访问生成的 URL，你的在线幻灯片就已经制作完毕。






以上，不一定是最佳实践，不过经笔者亲测，行之有效。
