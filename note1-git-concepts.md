## 版本控制

> 版本控制系统（VCS, Version Control System）：是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。

#### 1. 本地版本控制系统

采用某种简单的数据库来记录文件的历次更新差异。rcs.

![本地版本控制系统](http://upload-images.jianshu.io/upload_images/3164619-fa6eb3c1ad147bc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

#### 2. 集中式版本控制系统（CVCS, Centralized VCS）

优点是可以让在不同系统上的开发者协同工作，缺点是中央服务器的单点故障。CVS、Subversion、Perforce.

![集中式版本控制系统](http://upload-images.jianshu.io/upload_images/3164619-2453a750d9473eb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

#### 3. 分布式版本控制系统（DVCS, Distributed VCS）

客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。因为每一次的克隆操作，实际上都是一次对代码仓库的完整备份。Git、Mercurial、Bazzar、Darcs.

![分布式版本控制系统](http://upload-images.jianshu.io/upload_images/3164619-ed801ead93880970.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/480)

更进一步，许多这类系统都可以指定和若干不同的远端代码仓库进行交互。籍此，你就可以在同一个项中，分别和不同工作小组的人相互协作。你可以根据需要设定不同的协作流程，比如层次模型式的工作流，而这在以前的集中式系统中是无法实现的。


## Git 简史

Git 的目标：

- 速度
- 简单的设计
- 对非线性开发模式的强力支持（允许成千上万个并行开发的分支）
- 完全分布式
- 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）

2005年诞生


## Git 简介

> Git 的思想和基本工作原理

#### 1. 直接记录快照，而非比较差异

Git 之外的大部分系统以文件变更列表的方式（一组基本文件和每个文件随时间逐步累积的差异）存储信息。

![文件变更列表](http://upload-images.jianshu.io/upload_images/3164619-89e3b904eea9dd2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)

Git 更像是把数据看作是对小型文件系统的一组快照。每次提交更新，或在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。Git 对待数据更像是一个快照流。

![快照流](http://upload-images.jianshu.io/upload_images/3164619-a3032cac3521f0e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/720)

Git 更像是一个小型的文件系统，提供了许多以此为基础构建的超强工具，而不只是一个简单的 VCS。

#### 2. 近乎所有操作都是本地操作

在 Git 中的绝大多数操作都只需要访问本地文件和资源，而集中式版本控制系统所有操作都有网络延时开销，Git 在速度上具有绝对优势。因为在本地磁盘上就有项目的完整历史，所以大部分操作看起来瞬间完成。

举个例子，要浏览项目的历史，Git 不需外连到服务器去获取历史，然后再显示出来——它只需直接从本地数据库中读取。这也意味着当离线或者没有 VPN 时，几乎可以进行任何操作。比如在飞机或火车上想做些工作，可以先愉快地提交，直到有网络连接时再上传。

使用其它系统，做到如此是不可能或很费力的。比如，用 Perforce，没有连接服务器时几乎不能做什么事；用 Subversion 和 CVS，能修改文件，但不能向数据库提交修改（因为本地数据库离线了）。

#### 3. Git 保证完整性

Git 中所有数据在存储前都计算校验和，然后以校验和来引用。这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。若你在传送过程中丢失信息或损坏文件，Git 就能发现。

Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。

#### 4. Git 一般只添加数据

很难让 Git 执行任何不可逆操作，或者让它以任何方式清除数据。同别的 VCS 一样，未提交更新时有可能丢失或弄乱修改的内容；但是一旦提交快照到 Git 中，就难以再丢失数据，特别是如果定期的推送数据库到其它仓库的话。

这使得我们使用 Git 成为一个安心愉悦的过程，因为我们深知可以尽情做各种尝试，而没有把事情弄糟的危险。

#### 5. 三种状态（重点）

Git 有三种文件状态：已提交（committed）、已修改（modified）和已暂存（staged）。

- 已提交表示数据已经安全的保存在本地数据库中。
- 已修改表示修改了文件，但还没保存到数据库中。
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

由此引入 Git 项目的三个工作区域的概念：Git 仓库、工作目录以及暂存区域。

![工作目录、暂存区、仓库](http://upload-images.jianshu.io/upload_images/3164619-52457ee17b08f637.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。这是 Git 中最重要的部分，从其它计算机克隆仓库时，拷贝的就是这里的数据。

工作目录是对项目的某个版本独立提取出来的内容。这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。

暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。有时候也被称作`‘索
引’'，不过一般说法还是叫暂存区域。

基本的 Git 工作流程如下：

1. 在工作目录中修改文件。
2. 暂存文件，将文件的快照放入暂存区域。
3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。

如果 Git 目录中保存着的特定版本文件，就属于已提交状态。如果作了修改并已放入暂存区域，就属于已暂存状态。如果自上次取出后，作了修改但还没有放到暂存区域，就是已修改状态。


## 命令行

在命令行中使用 Git


## 安装 Git

[Google 一下](http://www.google.cn)


## 初次运行 Git 前的配置

Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。这些变量存储在三个不同的位
置：

- /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --system 选项的
git config 时，它会从此文件读写配置变量。
- ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 可以传递 --global 选项让 Git读写此文件。
- 当前使用仓库的 Git 目录中的 config 文件（就是 .git/config）：针对该仓库。

每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。在 Windows 系统中，Git 会查找 $HOME 目录下（一般情况下是 C:\Users\$USER）的 .gitconfig 文件。Git 同样也会寻找 /etc/gitconfig 文件，但只限于 MSys 的根目录下，即安装 Git 时所选的目标位置。

#### 1. 用户信息

```
# 如果在个人计算机上可以加 --global 选项
$ git config user.name "No Name"
$ git config user.email noname@email.com
```

#### 2. 文本编辑器

```
# 当 Git 需要用户输入信息时会调用，如果未配置，Git 会使用系统默认的文本编辑器（如 Vim）
$ git config core.editor emacs
```

#### 3. 检查配置信息

```
# 列出所有配置
$ git config --list
```

```
# 检查某一项配置，比如 user.name
$ git config user.name
```


## 获取帮助

```
# Git 手册
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```
