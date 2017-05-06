---
title: 程序员入门之git
date: 2016-05-29 10:49:08
categories:
- 工具
tags:
 - 工具
 - 版本管理
---
# 代码版本管理工具
在生产环境下的开发过程中，一个工程的代码通常是有多个程序员协同完成，这就涉及到代码在不通终端的同步问题，基于此需求，我们产生了代码版本工具，目前比较主流的两种为git和SVN

<!-- more -->
## git 与 SVN
关于git和SVN的区别，网上有很多，根据笔者使用的经验，感觉git还是要比SVN先进一些，首先git是一个分布式版本管理系统，SVN更像是一个储存代码的仓库，管理员可以给不同的代码提交者提供不同的权限，仅此而已。git于SVN相比明显的优势在于不依赖网络，对分支管理有更好的支持，命令行简介好用（SVN也有命令行工具，但很多公司还是采用图形化界面）

## git介绍
git是Linux的创始人Linus于2005年花了大概两周时间用C语言编写的分布式版本控制系统。

## git使用

### 安装
在控制台输入git 如果弹出提示信息，则跳过此步骤

#### mac

可以使用homebrew安装

```
brew install git
```

homebrew作为程序员mac的标配，如果你还没有安装，请键入：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

也可以安装xcode，自带git

#### windows

到官网下载安装:
[http://msysgit.github.com/](http://msysgit.github.com/)

#### Linux

```
yum install git-core
```

或者：

```
apt-get install git
```

### 配置
在命令行输入：

```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

改配置用于识别代码提交者身份

### 使用
#### 代码提交

```
git init   #创建代码版本库
git add .  #将当前目录下所有文件加入版本库
git commit -m "message"  #提交代码
```

```
git status #查看工作区状态
git diff #查看代码更改
git log  #查看提交日志
```

#### 版本回退

```
git reflog #查看所有日志，包含head信息
git reset --hard HEAD^ 会退到上一版本
git reset --hard <commit_id> 会退到指定版本
```

撤销工作区修改：

```
git checkout -- file
```

#### 远程仓库
这里以github为例
从远程仓库克隆代码:

```
git clone https://github.com/username/projectname.git
```

如果想讲本地已有的代码推送到远程，则需要跟远程费分支建立连接
与远程分支建立联系，需要remote origin

```
git remote add origin https://github.com/username/projectname.git #添加远程分支
git push origin master #推送本地origin分支到master分支
```
如果提示有冲突，则需要先pull 下来，修改之后再push

#### 分支管理
git中，一个分支为一个工作环境，分支与分支之间可以执行创建和合并操作。
分支的一般使用：

```
git branch # 查看分支
git branch <name>  # 创建分支
git checkout <name> # 切换分支
git checkout -b <name> # 创建并切换分支
git merge <name> #合并某分支到当前分支
git branch -d <name> # 删除分支
git branch -D <name> # 强行删除分支
```

当更改同时发生在两个分支上，这时候我们有需要对两个分支进行合并，那解决冲突是很容易发生的状况这时候我们需要使用git status 查看状态，执行合并之后在当前分支解决合并冲突问题，在合并就可以了
可以使用一下命令查看分支合并情况：

```
git log --graph
```

#### 忽略别名
在git中可以通过编辑.gitignore 文件达到控制忽略文件类型的目的，当文件自动不被add 到仓库里。
忽略的语法规则：
(#)表示注释
(*)  表示任意多个字符;
(?) 代表一个字符;
 ([abc]) 代表可选字符范围
如果名称最前面是路径分隔符 (/) ，表示忽略的该文件在此目录下。
如果名称的最后面是 (/) ，表示忽略整个目录，但同名文件不忽略。
通过在名称前面加 (!) ，代表不忽略。
例子如下：

```
# 这行是注释
*.a   # 忽略所有 .a 伟扩展名的文件
!lib.a   # 但是 lib.a 不忽略，即时之前设置了	忽略所有的 .a
/TODO   # 只忽略此目录下 TODO 文件，子目录的 TODO 不忽略
build/    # 忽略所有的 build/ 目录下文件
doc/*.txt    # 忽略如 doc/notes.txt, 但是不忽略如 doc/server/arch.txt
```

关于不同编程语言，通常会有统一的忽略规则，大家可以在这里直接找到配置模板：
[https://github.com/github/gitignore](https://github.com/github/gitignore)
#### 快捷命令配置
在git里可以使用

```
git config --global alias.<shortname> <command_name>
```

的方式指定快捷命令，
以下为一些常用的快捷命令设置

```
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch
git config --global alias.unstage 'reset HEAD'
git config --global alias.last 'log -1'
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```


ok ,that's all
