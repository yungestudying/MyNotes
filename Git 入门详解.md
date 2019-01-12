

# Git入门详解



---



## **1.Git简介**

官方文档：[点击访问](https://www.git-scm.com/doc)

**what is git ?** 
*Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.*



### **1.1 Git、GitHub与GitLab**

- Git是一个版本控制软件

- GitHub与GitLab都是用于管理版本的服务端软件

- GitHub提供免费服务(代码需公开)及付费服务(代码为私有)

- GitLab用于在企业内部管理Git版本库，一般叫做Git私服


### **1.2 为什么使用Git**

- 本地建立版本库
- 本地版本控制
- 多主机异地协同控制
- 重写提交说明
- 有后悔药可以吃
- 更好用的提交列表
- 更好的差异比较
- 更完善的分支系统
- 速度超快



### **1.3 Git工作模式**

![image_1cg3ne9qqd7a1o7h1ok9k9e1d0n13.png-103.6kB](http://static.zybuluo.com/huiyun/4q6uis4p7vm22boxrefav77k/image_1cg3ne9qqd7a1o7h1ok9k9e1d0n13.png)



- 版本库初始化 
  - 个人计算机从版本服务器同步
- 操作 
  - 90%以上的操作在个人计算机上
  - 添加文件
  - 修改文件
  - 提交变更
  - 查看版本历史等
- 版本库同步 
  - 将本地修改推送到版本服务器 



**集中式版本控制系统文件存储**

这类系统（CVS、Subversion、Perforce、Bazaar 等等）存储着**每个文件与原始版本的差异**。  ![image_1cg3stji3kuepbkpn6dlsjs53a.png-41.8kB](http://static.zybuluo.com/huiyun/tydof5wlr4k6vi9kals1x78l/image_1cg3stji3kuepbkpn6dlsjs53a.png)



**git文件存储**

每次你提交更新，或在Git中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git不再重新存储该文件，而是只保留一个指针指向之前存储的文件。



### **1.4 Git的特性**

- 近乎所有操作都在本地执行
- 直接记录快照，而非差异比较
- 时刻保持数据完整性
- 多数操作仅仅是添加数据



### **1.5 Git文件的三种状态**

Git文件有三种状态，我们的文件可能处于其中之一：

- **已提交(Modified)**：在工作目录修改Git文件
- **已暂存(Staged)**：对已修改的文件执行Git暂存操作，将文件存放入暂存区
- **已提交(Committed)**：将已暂存的文件执行Git提交操作，将文件存入版本库

由此引入了Git项目的三个工作区域的概念：**Git对象仓库、工作目录**以及**索引区**

**Git对象仓库**是Git用来保存项目元数据和对象数据库的地方

**工作目录**是对项目的某个版本独立提取出来的内容。

**索引区**是一个文件，保存了下次将提交的文件列表信息，一般在Git仓库目录中，有时也称为暂存区

**Git的基本工作流程如下**：

​	1.在工作目录中修改文件

  	2.暂存文件，将修改文件后生成的快照存放在索引区中

​	3.提交更新，将存放在索引区中的快照永久存储在Git对象仓库中



## **2.Git的安装与配置**



### **2.1 在Linux上安装Git**

**1.在CentOS上安装**

```sh
$ yum install git -y
```

**2.在Ubuntu上安装**

```sh
$ sudo apt-get install git
```



### **2.2 在Windows上安装Git**

GitBash下载地址:[点击下载](https://git-scm.com/) 
 ![image.png-87.6kB](http://static.zybuluo.com/huiyun/wr0eb8gc4ej3i2pomziy80ku/image.png)

下面是安装git bash的操作 

 ![image.png-31.3kB](http://static.zybuluo.com/huiyun/8eswnamskd5060mww68i7xe5/image.png)

![image.png-25.7kB](http://static.zybuluo.com/huiyun/p85n4foz4wpvq651quka7jhc/image.png)![image.png-32.8kB](http://static.zybuluo.com/huiyun/h5u91pbpj4dtj4fud6zyipiv/image.png)![image.png-35.6kB](http://static.zybuluo.com/huiyun/zzoszv425qh9objqpsil5h5b/image.png)

后面的操作都点击下一步即可



### **2.3 使用Git前的配置**

**相关配置文件** 
 Git自带一个`git config`工具来帮助设置控制Git外观和行为的配置变量。

这些变量存储在三个不同的位置：(文件优先级越往下越高)

1.`/etc/gitconfig`文件: 包含系统上每一个用户及他们仓库的通用配置。 

```sh
$ git config --system  ...
```

2.`~/.gitconfig`或 `~/.config/git/config`文件：只针对当前用户。

```sh
$ git config --global ...
```

3.`.git/config`：针对于某个特定项目

```sh
$ git config --local ...
```

当安装完Git之后应该做的第一件事就是设置你的用户名称与邮件地址。这样做很重要，因为每一个Git的提交都会使用这些信息，并且它会写入到你每一次提交中，不可更改:

```sh
$ git config --global user.name "why"
$ git config --global user.email "823051124@qq.com"
```

> **说明：**如果使用了--global选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情，Git都会使用那些信息。

当你想针对特定项目使用不同的用户名称与邮件地址时，可以这样做：

```sh
$ git config --local user.name "ali"
$ git config --local user.email "823051124@qq.com"
```

**检查配置信息** 
 如果想检查配置信息，可以使用`git config --list`命令来列出所有Git当时能找到的配置

```sh
$ git config --list
user.name=why
user.email=823051124@qq.com
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
```

还可以通过输入`git config <key>`来检查Git的某一项配置

```sh
$ git config user.name
why
```

**删除某个变量信息**

```sh
git config --local --unset user.name
git config --local --unset user.email
git config --global --unset user.name
git config --global --unset user.email
```



## **3.Git的简单使用**

### **3.1 git init**

示例：

```sh
$ git init
Initialized empty Git repository in /root/mygit/.git/
```

我们可以看一下 .git 目录的内容

```sh
[root@huyiun mygit]# tree .git
.git
├── branches
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   └── update.sample
├── info
│   └── exclude
├── objects
│   ├── info
│   └── pack
└── refs
    ├── heads
    └── tags
```



### **3.2 git add**

该命令的作用是将已修改的文件纳入到索引区中

**Git有4种类型的对象**：

- blob：用来存储文件的数据，通常是一个文件对象
- tree：一串指向blob对象或其它tree对象的指针
- commit：指向某一个tree，用来标记项目某一特定时间点的状态
- tag：某一次提交的标记

几乎所有的Git功能都是使用这四个简单的对象类型来完成的。![1546404769787](/home/why/.config/Typora/typora-user-images/1546404769787.png)



比如，我们建立一个文件

```sh
$ cat text.txt 
Hello World
```

此时可以使用`git status`查看其状态

```shell
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        text.txt

nothing added to commit but untracked files present (use "git add" to track)
```

可以看到上面显示的是`Untracked files`，这说明文件现在还没有被加入到索引区或还未提交。

于是我们使用`git add`命令来进行操作

```sh
$ git add text.txt 
```

然后再查看其状态

```java
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   text.txt
```

此时，我们可以查看 .git 目录发现多了一个 INDEX 目录以及 Object 中有对象存在

```sh
$ tree .git             
.git
├── branches
├── config
├── description
├── HEAD
├── hooks
│   ├── applypatch-msg.sample
│   ├── commit-msg.sample
│   ├── fsmonitor-watchman.sample
│   ├── post-update.sample
│   ├── pre-applypatch.sample
│   ├── pre-commit.sample
│   ├── prepare-commit-msg.sample
│   ├── pre-push.sample
│   ├── pre-rebase.sample
│   ├── pre-receive.sample
│   └── update.sample
├── index
├── info
│   └── exclude
├── objects
│   ├── 55
│   │   └── 7db03de997c86a4a028e1ebd3a1ceb225be238
│   ├── info
│   └── pack
└── refs
    ├── heads
    └── tags
```

我们可以使用`git ls-files`命令来查看索引区中的内容

```sh
$ git ls-files -s
100644 557db03de997c86a4a028e1ebd3a1ceb225be238 0       text.txt
```

于是可以发现，在索引区中存放的其实就是**hash值和与其对应的文件名的映射关系**

于是，如果当我将文件添加至索引区后，如果后悔，想退回去，可以使用如下操作来删除索引区中的映射关系。

```sh
$ git rm --cached text.txt
rm 'text.txt'
```

此时，你再使用`git ls-files`命令查看索引区的内容

```sh
$ git ls-files -s
$
```

发现已经没有内容了。

我们再使用`git status`查看文件的状态

```sh
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        text.txt

nothing added to commit but untracked files present (use "git add" to track)
```

发现文件又变成了`Untracked files`的状态了。



### **3.3 git commit**

该命令的作用是将索引区中的文件提交到版本库中去

我们将刚才的文件添加至索引区

```sh
$ git add text.txt 
```

然后使用`git commit`命令进行提交

```sh
$ git commit
```

此时进入一个文本界面![图片.png-142.4kB](http://static.zybuluo.com/huiyun/wsqzp2g0g3shua8viosbnw0y/%E5%9B%BE%E7%89%87.png)

编辑你的提交信息，保存退出即可

如果你的注释不是特别多的话，可以使用如下操作：

```sh
$ git commit -m "v1"
[master (root-commit) 5dfcd54] v1
 1 file changed, 1 insertion(+)
 create mode 100644 text.txt
```

提交完成后，如果想查看提交信息，可使用`git log`

```sh
$ git log
commit 5dfcd54a9e005087301646ad6ad2a9c63e671711 (HEAD -> master)
Author: why <823051124@qq.com>
Date:   Wed Jan 2 14:51:05 2019 +0800

    v1
```

当提交后会有一个提交对象，我们可以使用`git cat-file`命令来查看这些对象的内容

```sh
$ git cat-file -t 5dfcd54a9e005087301646ad6ad2a9c63e671711
commit

$ git cat-file -p 5dfcd54a9e005087301646ad6ad2a9c63e671711
tree d0e74797aabca3dbb16126f1da1766e5301324ce
author why <823051124@qq.com> 1546411865 +0800
committer why <823051124@qq.com> 1546411865 +0800

v1
```

`git cat-file`命令常用的两个选项：

​	-t：显示对象的类型

   	-p：显示对象的内容

可以看到，5dfcd54a（此处为简写）这个提交对象指向了一个tree对象 d0e74797，于是我们同样查看一下这个tree对象的内容

```sh
$ git cat-file -p d0e74797
100644 blob 557db03de997c86a4a028e1ebd3a1ceb225be238    text.txt
```

tree对象 d0e74797 指向了一个blob对象 557db03，而这个对象对应的正是 text.txt 文件。

经过实验可以得知： 
**当 git commit 后，会生成一个commit对象，该对象中包含了一个tree对象，该tree对象又有blob对象和tree对象，依次类推。**

现在我们往text.txt文件追加内容：

```sh
$ cat text.txt 
Hello World
Welcome to you
```

使用`git status`查看当前状态

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   text.txt

no changes added to commit (use "git add" and/or "git commit -a")

```

此时，我们可以使用`git commit -a`来自动将文件添加至索引区并提交它

```sh
$ git commit -am "v2"
```

另外，假如你在本地提交后发现还有内容并未提交上去，但是你又不想等到下一次提交，可以使用下面的操作来改写最近一次提交

```sh
git commit --amend -am "v2.1"
```



### **3.4 git rm**

接下来，我们再创建一个文件然后提交它

```sh
$ echo "I am Devops" >> text2.txt
$ git add text2.txt 
$ git commit -m "v3"
```

假如，我发现误提交一个文件到了版本库里，想删掉它，那么可以这样做：

```sh
$ git rm text2.txt 
```

然后我们再使用`git status`查看一下状态

```sh
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    text2.txt
```

如果你确定要删除这个文件，则使用`git commit`命令提交即可。

但是假如我们发现删错了，想回退到未删除的状态，那么就应该使用`git reset HEAD <file>`这个命令了。它可以理解为是`git add`的一个逆操作，即取消已暂存的内容。

```sh
$ git reset HEAD text2.txt
Unstaged changes after reset:
D       text2.txt
```

然后我们再使用`git status`查看其状态

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    text2.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

此时，我们再使用`git checkout -- <file>`命令即可还原文件

```sh
$ git checkout -- text2.txt
```

`git checkout -- <file>`命令是用来取消对工作目录的修改。

结合这整个过程，我们可以知道`git rm`的执行步骤：

​	1.使用rm命令删除工作目录中的文件以及

​	2.再使用`git add`命令添加至索引区，即删除了索引区中的映射关系

回顾一下，我们之前还学习了`git rm --cached`这个命令，它表示的是只删除索引区中的映射关系，而不删除工作目录中的原文件。

```sh
$ git rm --cached text2.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    text2.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        text2.txt
```



### **3.5 git mv**

如果我们想重命名一个文件，可以这样：

```sh
$ git mv text.txt text1.txt
```

然后我们再使用`git status`查看其状态

```sh
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    text.txt -> text1.txt
```

然后执行`git commit`即可

如果我们在未提交前想回退，可以使用如下操作：

```sh
$ git reset HEAD text1.txt
```

然后我们再使用`git status`查看

```sh
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    text.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        text1.txt
```

看到这里，我想你应该明白了`git mv`的原理

`git mv`的操作其实分为两个步骤：

​	1.先创建一个与text.txt文件内容相同的text1.txt文件

​	2.然后再删除text.txt文件

​	3.然后再一起提交

相当于如下操作：

```sh
$ cat text.txt >> text1.txt
$ git rm text.txt
$ git add .
```



### **3.6 git diff**

我们先来看看Linxu系统上自带的`diff`命令

```sh
$ diff -u text1.txt text2.txt 
--- text1.txt   2019-01-02 16:16:50.173221997 +0800
+++ text2.txt   2019-01-02 16:16:29.393129011 +0800
@@ -1,4 +1,2 @@
-Hello World      
-Welcome to you
-1111    
-2222
+I am Devops
+I am Devops

```

`--- text1.txt`表示源文件是 text1.txt

`+++ text2.txt`表示目标文件是 text2.txt

`-1,4`表示源文件的第1行到第4行

`+1,2`表示目标文件的第1行到第2行

`-文件内容`表示源文件需要删除该行内容才与目标文件的该行内容相同

`+文件内容`表示源文件需要添加该行内容才与目标文件的该行内容相同

如果前面没有+或-，则说明两个文件的某一相同行都存在该内容



回到`git diff`命令上，存在如下比较：

**1.工作区和索引区的比较**

命令：`git diff`

首先我们先将一个文件添加到索引区中

```sh
$ git add text1.txt 
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   text1.txt

```

然后改变工作目录中该文件的内容

```sh
$ git diff
diff --git a/text1.txt b/text1.txt
index 8a6fb99..c9c586c 100644
--- a/text1.txt
+++ b/text1.txt
@@ -1,2 +1,3 @@
 Hello World
 Welcome to you
+How old are you
```

可以看到，

`--- a/text1.txt`表示的是索引区中的内容

`+++ b/text1.txt`表示的是工作目录的内容



**2.工作区与版本库的比较**

命令：`git diff HEAD`

首先我们查看当前已提交的文件的内容

```sh
$ cat text1.txt 
Hello World
Welcome to you
1111
2222
```

然后修改工作目录中文件的内容

```sh
$ cat text1.txt 
Hello World
Welcome to you
How Old are you
```

使用`git diff HEAD`查看

```sh
why@huiyun:~/mygit$ git diff HEAD
diff --git a/text1.txt b/text1.txt
index 568a156..d05d8b3 100644
--- a/text1.txt
+++ b/text1.txt
@@ -1,4 +1,3 @@
 Hello World
 Welcome to you
-1111
-2222
+How Old are you
```

可以看到，

`--- a/text1.txt`表示的是版本库中的内容

`+++ b/text1.txt`表示的是工作目录的内容



**3.索引区与版本库的比较**

命令：`git diff --cached`

将上面例子中的 text.txt 添加至索引区中

```sh
$ git add text1.txt
```

然后使用`git diff --cached`查看

```sh
$ git diff --cached 
diff --git a/text1.txt b/text1.txt
index 568a156..d05d8b3 100644
--- a/text1.txt
+++ b/text1.txt
@@ -1,4 +1,3 @@
 Hello World
 Welcome to you
-1111
-2222
+How Old are you
```

可以看到，

`--- a/text1.txt`表示的是版本库中的内容

`+++ b/text1.txt`表示的是索引区中的内容



### **3.7 git log**

`git log`的常用选项：

​	-p：展开显示每次提交的内容差异

​	-n：仅显示最近的n次提交

​	--stat：仅显示简要的增改行数统计

​	--pretty=oneline：以一行显示提交的简短内容

​	--pretty=format:"%h-%an,%ar:%s"

示例1：

```sh
$ git log --stat -p -n 1
commit f0dbbd9c7eb20f5ec83ea7bcf35008fcd1ddd31e (HEAD -> master)
Author: why <823051124@qq.com>
Date:   Wed Jan 2 17:03:18 2019 +0800

    v6
---
 text1.txt | 3 +--   ##此处为--stat的效果，表示增加1行，删除两行
 1 file changed, 1 insertion(+), 2 deletions(-)

diff --git a/text1.txt b/text1.txt  ##此处为-p的效果，相当于git diff
index 568a156..d05d8b3 100644
--- a/text1.txt
+++ b/text1.txt
@@ -1,4 +1,3 @@
 Hello World
 Welcome to you
-1111
-2222
+How Old are you
```

示例2：

```sh
$ git log --pretty=oneline
f0dbbd9c7eb20f5ec83ea7bcf35008fcd1ddd31e (HEAD -> master) v6
9c47aee8d6548bfe9bcd1cacefbde5e33d80bd08 v5
d3a1a4d9c6d6ee20deedbb222d6e9cf3a52b5812 v3
872a0c29bc4061c8b48fed58d638444ee0a7c71f v2.2
a6964dc178e48c6e0f5d2c2f990fbb23a97eaf1b v2
5dfcd54a9e005087301646ad6ad2a9c63e671711 v1
```

示例3：

```sh
$ git log  --pretty=format:"%h-%an,%ar:%s"
f0dbbd9-why,2 minutes ago:v6
9c47aee-why,45 minutes ago:v5
d3a1a4d-why,2 hours ago:v3
872a0c2-why,2 hours ago:v2.2
a6964dc-why,2 hours ago:v2
5dfcd54-why,2 hours ago:v1
```



### **3.8 .gitignore**

假如现在我们的工作目录有如下文件：

```sh
$ ls -l
total 12
-rw-rw-r-- 1 why why 15 1月   2 17:08 setting.xml
-rw-rw-r-- 1 why why 43 1月   2 16:46 text1.txt
-rw-rw-r-- 1 why why 24 1月   2 16:16 text2.txt
```

使用`git status`查看

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   text1.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        setting.xml

no changes added to commit (use "git add" and/or "git commit -a")
```

可以看到，这里有两个提示，一个是文件已经修改，需要add，另一个是文件已经创建，需要add，前面我们也学过一个指令`git add .`，即将所有修改的内容放到暂存区。但是现在我的需求是，当使用`git add .`的时候，如何只将test1.txt的操作添加至索引区，而不将setting.xml添加至索引区呢？

针对这种问题，我们可以通过`.gitignore`文件来设置规则

```sh
$ cat .gitignore 
.gitignore    #将.gitignore本身也加进来
setting.xml   #将不需要提交的文件写在.gitignore文件里
```

然后我们再查看

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   text1.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

下面介绍一些`.gitignore`的规则

```
*.b             忽略所有 .a 结尾的文件
!a.b            不忽略(跟踪)a.b
/TODO           忽略根目录下的TODO文件，不包括，subdir(子目录)/TODO
build/          忽略build/目录下的所有文件
doc/*.txt       忽略doc/目录下所有的.txt结尾的文件，但不包括其子目录下的.txt文件
/*/*.txt        忽略根目录下子目录中以.txt结尾的文件
/**/*.txt       忽略根目录下所有以.txt结尾的文件，包括子目录下的.txt文件
```



### **3.9 git的工作原理**

假设我们进入到一个新目录，其中有一个文件，我们称其为v1版本，现在运行`git init`，这时会创建一个Git仓库。![图片.png-46.4kB](http://static.zybuluo.com/huiyun/04c84e5dhdkgvuz6ppr252q7/%E5%9B%BE%E7%89%87.png)

此时，只有工作目录有内容。

> **说明**：HEAD 是当前分支引用的指针，它总是指向该分支上的最后一次提交。 这表示 HEAD 将是下一次提交的父结点。 通常，理解 HEAD 的最简方式，就是将它看做你的上一次提交的快照,下一次提交的父节点。

现在我们使用`git add`将该文件添加至索引区![图片.png-65.1kB](http://static.zybuluo.com/huiyun/cmmcug8ipsa1wxkt87rseiss/%E5%9B%BE%E7%89%87.png)

然后我们查看其状态

```sh
$ git status
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   file.txt
```

你会发现，此时不能使用`git reset HEAD <file>`来取消暂存，因为HEAD的指向为空

接着运行`git commit`，它会取得索引区中的内容并将它保存为一个永久的快照，然后创建一个指向该快照的提交对象，最后更新master来指向本次提交。![选区_665.png-63.7kB](http://static.zybuluo.com/huiyun/n2cswr7jh8yp6j5ogds4wza3/%E9%80%89%E5%8C%BA_665.png)

此时如果我们运行`git status`，会发现没有任何改动，因为现在三个区域完全相同。

现在我们想要对文件进行修改然后提交它，首先在工作目录中修改文件，我们称其为该文件的v2版本![图片.png-83.6kB](http://static.zybuluo.com/huiyun/1fa7f9rc70xmydvsk2q0j41h/%E5%9B%BE%E7%89%87.png)

如果现在运行`git status`，我们会看到一个提示，这个提示正是因为索引区中的内容与工作目录的内容不同而导致的。

```sh
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   file.txt

no changes added to commit (use "git add" and/or "git commit -a")
```



于是我们运行`git add`将它添加至索引区中![选区_667.png-63.3kB](http://static.zybuluo.com/huiyun/oa5zct7oa6ni6yx6krj0lajz/%E9%80%89%E5%8C%BA_667.png)

此时，由于索引和HEAD不同，若运行`git status`的话，接下来我们会收到一个提示表明现在预期的下一次提交与上一次提交不同。

```sh
why@huiyun:~/mygit$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   file.txt
```

此时我们有两种选择，要么使用`git reset HEAD <file>`将索引区中的内容退回至上一次提交的状态，要么运行`git commit`来完成本次提交。

现在我们运行`git commit`来完成提交![图片.png-96.3kB](http://static.zybuluo.com/huiyun/cdicieqsqjx7lyt6i52xl34a/%E5%9B%BE%E7%89%87.png)

此时HEAD指向最新一次提交，而且三个区域又变得相同了。



### **3.10 git reset**

为了演示这些例子，假设我们再次修改了 file.txt 文件并第三次提交它。

现在的提交历史看起来是这样的![图片.png-101.1kB](http://static.zybuluo.com/huiyun/qv7qiepe5mgds3dc5iu98bpz/%E5%9B%BE%E7%89%87.png)

现在我们来说说`git reset`的3个操作

**第1步：移动HEAD**

reset做的第一件事就是移动HEAD的指向。这意味着如果 HEAD 设置为 master 分支（例如，你正在 master 分支上），运行 `git reset 9e5e64a` 将会使 master 指向 9e5e64a。 ![选区_670.png-89.2kB](http://static.zybuluo.com/huiyun/3eq88e90453dor4xt61cimsd/%E9%80%89%E5%8C%BA_670.png)

现在看一眼上图，理解一下发生的事情：它本质上是撤销了上一次 `git commit` 命令。 当你在运行 `git commit` 时，Git 会创建一个新的提交，并移动 HEAD 所指向的分支来使其指向该提交。 当你将它 reset 回 HEAD~（HEAD 的父结点）时，其实就是把该分支移动回原来的位置，而不会改变索引和工作目录。 

```sh
$ git reset --soft HEAD~

$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   file.txt

```

现在运行`git status`可以查看到当前提交的版本是v2版本，但是索引区中的内容和工作目录的内容是v3的版本。

**第2步：更新索引**

接下来，reset会用 HEAD 指向的当前快照的内容来更新索引![图片.png-121.8kB](http://static.zybuluo.com/huiyun/c08euv9c96m0zd3fx7vv2d1j/%E5%9B%BE%E7%89%87.png)

上图的意思是，如果我们执行`git reset [--mixed] HEAD~`，那么它首先会指向**第1步：移动HEAD**的操作，然后再执行**第2步：更新索引**的操作。

```sh
git reset HEAD~

$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   file.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

现在运行`git status`可以查看到当前提交的版本和索引区的版本是v2版本，但是工作目录的内容是v3的版本。

**第3步：更新工作目录**

如果你想直接回退到上一个版本，那么运行`git reset --hard HEAD~`![图片.png-122.9kB](http://static.zybuluo.com/huiyun/ewq6j8u9aon1t5cbrmh4pwqq/%E5%9B%BE%E7%89%87.png)

它会先指向前2步，再指向第3步。运行此命令后，版本库、索引区和工作目录的内容都是上一个版本的内容。

于是我们可以总结一下：

​	`git reset --soft`表示当运行了第一步后就不执行后面的操作了，这一步相当于取消提交 。

​	`git reset [--mixed]`表示只运行前两步，相当于取消提交与暂存。

​	`git reset --hard`表示三步都运行之后，回到上1次提交的内容。

好的，假如现在我们回退到了第一个版本，想倒回去最新的版本，可以使用`get reflog`命令查看提交日志，再回退。

现在我们来实验一下：

1）查看提交历史。

```sh
$ git log
commit 9e2e2997fe563233150dbe28aa320f08f34d85aa (HEAD -> master)
Author: why <823051124@qq.com>
Date:   Wed Jan 2 18:19:39 2019 +0800

    v3

commit 77a32a903b3015906d6d32074541fa32137b0a48
Author: why <823051124@qq.com>
Date:   Wed Jan 2 17:55:01 2019 +0800

    v2

commit eaaf579272fdebb4e60726ad3857c0377c563997
Author: why <823051124@qq.com>
Date:   Wed Jan 2 17:38:29 2019 +0800

    v1

```

2）回退到第一个版本

```sh
$ git reset --hard eaaf579
```

3）查看提交历史

```sh
$ git log
commit eaaf579272fdebb4e60726ad3857c0377c563997 (HEAD -> master)
Author: why <823051124@qq.com>
Date:   Wed Jan 2 17:38:29 2019 +0800

    v1
```

4）使用`git reloag`查看提交历史

```sh
$ git reflog
eaaf579 (HEAD -> master) HEAD@{0}: reset: moving to eaaf579
9e2e299 HEAD@{1}: commit: v3
77a32a9 HEAD@{2}: reset: moving to HEAD~
35658a3 HEAD@{3}: commit: v3
77a32a9 HEAD@{4}: reset: moving to HEAD~
4fe790b HEAD@{5}: commit: v3
77a32a9 HEAD@{6}: commit: v2
eaaf579 (HEAD -> master) HEAD@{7}: commit (initial): v1
```

5）回到最新版本

```sh
$ git reset --hard 9e2e299
```

另外，`git reset`还可以指定file，格式如下：

```sh
$ git reset HEAD FILENAME
```

此时，它与`git add`的操作相反，表示取消暂存区



## **4.Git分支**

### **4.1 分支的创建**

Git的分支，其实本质上仅仅是指向提交对象的可变指针

Git的默认分支名字是master。在多次提交操作之后，你其实已经有一个指向最近一次提交对象的master分支。它会在每次的提交操作中自动向前移动。

![image.png-46.5kB](http://static.zybuluo.com/huiyun/mt0wc8sxczpheb0aw2wpjr2w/image.png)

查看当前分支：`git branch`

```sh
$ git branch
* master
```

创建分支：`git branch NEW_BRANCH`

```sh
$ git branch testing
$ git branch
* master
  testing
```

此时会在当前所提交的对象上创建一个指针

![image.png-31.3kB](http://static.zybuluo.com/huiyun/d9lunl136lfumhw9oydzp6xn/image.png)

那么，Git又是怎么知道当前在哪一个分支上呢？很简单，因为它有一个`HEAD`指针。

![image.png-35.4kB](http://static.zybuluo.com/huiyun/2zxatvkxm2ncksz4xpqp80hl/image.png)

我们可以使用`git checkout NEW_BRANCH`来切换分支

```sh
$ git checkout testing
Switched to branch 'testing'

$ git branch
  master
* testing
```

此时`HEAD`指针指向testing分支了。

![image.png-38.8kB](http://static.zybuluo.com/huiyun/m4t281evp72c3wmqmwiszsik/image.png)

那么，这样的实现方式会给我们带来什么好处呢？现在不妨再提交一次

```sh
$ vim file.txt 
$ git add file.txt 
$ git commit -m "testing v1"
```

此时，`HEAD`分支随着提交操作自动向前移动

![image.png-30.9kB](http://static.zybuluo.com/huiyun/omgm50x0eg3xw0okb6p3d81s/image.png)

如果我们再切换会master分支，那么`HEAD`又会移动到master分支上

```sh
$ git checkout master
```

这条命令做了两件事。 一是使 HEAD 指回 master 分支，二是将工作目录恢复成 master 分支所指向的快照内容。 也就是说，你现在做修改的话，项目将始于一个较旧的版本。 本质上来讲，这就是忽略 testing 分支所做的修改，以便于向另一个方向进行开发。

> **说明：**  
> 分支切换会改变你工作目录中的文件
>
> 在切换分支时，一定要注意你工作目录里的文件会被改变。如果是切换到一个较旧的分支，你的工作目录会恢复到该分支最后一次提交时的样子。 
>
> 如果 Git 不能干净利落地完成这个任务，它将禁止切换分支。

此时如果我们在master分支创建文件，就产生了两个方向上的分支

![image.png-31.6kB](http://static.zybuluo.com/huiyun/xjvvberuojqzyy8hijp9cu85/image.png)

说到这里，你可能会想到一个命令`git reset`，这个命令也是要移动`HEAD`指针的，那么它和`git checkout`的区别在哪里呢？

checkout与reset最重要的区别在于：reset 会移动 HEAD 分支的指向(改变master指向)，而 checkout 只会移动 HEAD 自身来指向另一个分支

例如，假设我们有 master 和 develop 分支，它们分别指向不同的提交；我们现在在 develop 上（所以 HEAD 指向它）。 如果我们运行 `git reset master`，那么 develop 自身现在会和 master 指向同一个提交。 而如果我们运行 `git checkout master 的话`，develop 不会移动，HEAD 自身会移动。 现在 HEAD 将会指向 master。 

![选区_673.png-61.3kB](http://static.zybuluo.com/huiyun/i28bew9bjqbooyad8g740p5h/%E9%80%89%E5%8C%BA_673.png)



### **4.2 分支的合并**

现在，我们来看一个例子：

首先，我们在master分支上已经有了一些提交。

![image.png-22kB](http://static.zybuluo.com/huiyun/k2e4utkc4hqx1ehmj3cgdtr1/image.png)

然后突然发现系统出现了bug，于是我们新建一个分支专门来解决这个bug

```sh
$ git checkout -b iss53
Switched to a new branch 'iss53'
```

> **说明：** `git checkout -b`表示新建一个并切换到其上面

![image.png-30.1kB](http://static.zybuluo.com/huiyun/w4965zwlqr1jptu6h7ian7fz/image.png)

然后，我们在这个iss53分支上修改了很多bug，并做了一些提交。

```sh
$ vim bug.txt
$ git add bug.txt 
$ git commit -m "bug v1"
```

此时分支的情况是这样的：

![选区_655.png-18kB](http://static.zybuluo.com/huiyun/m09n6rpj63rezbzbk7466pld/%E9%80%89%E5%8C%BA_655.png)

现在，我们接到了一个紧急电话，说线上的系统出现了问题，于是我们切回到原先的master分支

```sh
$ git checkout master
```

接下来，你要修复这个紧急问题。 于是你又建立一个针对该紧急问题的分支（hotfix branch），在该分支上工作直到问题解决：

```sh
$ vim index.html
$ git add index.html 
git commit -m "hotfix v1"
```

此时分支的情况是这样子的：

![image.png-35.2kB](http://static.zybuluo.com/huiyun/thaemf4yqkjd84aq8vyafyvj/image.png)

当把这个紧急情况修复完后，我们就可以合并hotfix与master分支了

```sh
$ git checkout master
Switched to branch 'master'

$ git merge hotfix 
Updating b96c70a..4604644
Fast-forward
 index.html | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 index.html
```

此时的分支情况是这样的：

![选区_657.png-25.3kB](http://static.zybuluo.com/huiyun/3pys5ggt4r998zq5826oce60/%E9%80%89%E5%8C%BA_657.png)

合并分支后，此时就可以删除hotfix分支了

```sh
$ git branch -d hotfix
```

现在我们可以回到iss53分支上继续找Bug了

```sh
$ git checkout iss53
Switched to branch "iss53"
$ vim text2.html
$ git add text2.html
$ git commit -m "1th commit"
```

此时的分支是这样的

![image.png-25.6kB](http://static.zybuluo.com/huiyun/0so7c5jvz8bjl4dvfbs6hplq/image.png)

好的，现在假如你已经将iss53上的问题修复了，于是又要开始合并分支了

```sh
$ git checkout master 
切换到分支 'master'

$ git merge iss53 
Merge made by the 'recursive' strategy.
 bug.txt   | 1 +
 text4.txt | 1 +
 2 files changed, 2 insertions(+)
 create mode 100644 bug.txt
 create mode 100644 text4.txt
```

这和你之前合并 hotfix 分支的时候看起来有一点不一样。 此时，master 分支所在提交并不是 iss53 分支所在提交的直接祖先，Git不得不做一些额外的工作。 出现这种情况的时候，Git 会使用两个分支的末端所指的快照（C4 和 C5）以及这两个分支的工作祖先（C2），做一个简单的三方合并。 

![image.png-42.5kB](http://static.zybuluo.com/huiyun/94nphz9sl8vr9hooo0gcqboc/image.png)

需要指出的是，Git 会自行决定选取哪一个提交作为最优的共同祖先，并以此作为合并的基础；

![选区_660.png-17.5kB](http://static.zybuluo.com/huiyun/a6uiem6wpc48rmwow0co0zd9/%E9%80%89%E5%8C%BA_660.png)



### **4.3 分支合并冲突**

有时候合并操作不会如此顺利。如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。 

```sh
$ git merge iss53 
Auto-merging index.html
CONFLICT (add/add): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

于是我们编辑index.html

```
<<<<<<< HEAD
11111111
=======
aaaaa
>>>>>>> iss53
```

为了解决冲突，你必须选择使用由=======分割的两部分中的一个，或者你也可以自行合并这些内容。 例如，你可以通过把这段内容换成下面的样子来解决冲突：

```
11111111
```

这里我选择了其中一个分支的内容，在你解决了所有文件里的冲突之后，对每个文件使用` git add` 命令来将其标记为冲突已解决。 一旦暂存这些原本有冲突的文件，Git就会将它们标记为冲突已解决。

```sh
$ git add index.html 

$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:

        new file:   bug.txt

```

于是我们直接使用`git commit`来结束合并操作

```sh
$ git commit 
[master d22f1b4] Merge branch 'iss53'
```



### **4.4 分支管理**

现在已经创建、合并、删除了一些分支，让我们看看一些常用的分支管理工具。

`git branch`命令不只是可以创建与删除分支。如果不加任何参数运行它，会得到当前所有分支的一个列表：

```bash
$ git branch
  iss53
* master
```

如果需要查看每一个分支的最后一次提交，可以运行 `git branch -v` 命令：

```sh
$ git branch -v
  iss53  3be77de bug v2
* master d22f1b4 Merge branch 'iss53'
```

`--merged` 与 `--no-merged` 这两个有用的选项可以过滤这个列表中已经合并或尚未合并到当前分支的分支。 如果要查看哪些分支已经合并到当前分支，可以运行 `git branch --merged`：

```sh
$ git branch --merged
  iss53
* master
```

因为之前已经合并了 iss53 分支，所以现在看到它在列表中。 在这个列表中分支名字前没有 * 号的分支通常可以使用 `git branch -d` 删除掉；你已经将它们的工作整合到了另一个分支，所以并不会失去任何东西。

查看所有包含未合并工作的分支，可以运行 `git branch --no-merged`：

```sh
$ git branch --no-merged  
testing
```

这里显示了其他分支。 因为它包含了还未合并的工作，尝试使用 `git branch -d` 命令删除它时会失败：

```sh
$ git branch -d testing
error: The branch 'testing' is not fully merged.If you are sure you want to delete it, run 'git branch -D testing'.
```

如果真的想要删除分支并丢掉那些工作，如同帮助信息里所指出的，可以使用 -D 选项强制删除它。

我们还可以使用`git log --graph`查看提交历史

```java
why@huiyun:~/mygit$ git log --graph
*   commit d22f1b4a8988a49ffe78e9609e55de5f48bb1b7c (HEAD -> master)
|\  Merge: 4604644 3be77de
| | Author: wu <why@email.com>
| | Date:   Thu Jan 3 15:11:30 2019 +0800
| | 
| |     Merge branch 'iss53'
| | 
| * commit 3be77dece8005ae540c7a280be9e506e09de8352 (iss53)
| | Author: wu <why@email.com>
| | Date:   Thu Jan 3 15:00:51 2019 +0800
| | 
| |     bug v2
| | 
| * commit af6b1be12a1d89bead792d3498af30f22776145d
| | Author: wu <why@email.com>
| | Date:   Thu Jan 3 14:51:55 2019 +0800
| | 
| |     bug v1
| | 
* | commit 4604644db58151d8fbbac7138d063ceaa888f89e
|/  Author: wu <why@email.com>
|   Date:   Thu Jan 3 14:55:16 2019 +0800
|   
|       hotfix v1
| 
```



### **4.5 标签**

标签的作用给提交对象起一个简单的名称

Git 使用两种主要类型的标签：**轻量标签（lightweight）**与**附注标签（annotated）**。

创建一个轻量级标签

```
git tag v1.0.1
```

创建一个带有附注的标签

```
git tag -a v1.0.2 -m "release version"
```

-m 选项指定了一条将会存储在标签中的信息。 如果没有为附注标签指定一条信息，Git 会运行编辑器要求你输入信息。

默认标签是打在最新提交的commit上的。有时候，如果忘了打标签，比如，现在已经是周五了，但应该在周一打的标签没有打，怎么办？

我们可以找到找到历史提交的 commit id，然后打上就可以了：

```
$ git tag v0.1 d22f1b4 
```

> **说明**：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。 

**查看标签**

简单查看标签

```
$ git tag 
v1.0.1 
v1.0.2
```

过滤标签

```
$ git tag -l 'v1.0.*'
v1.0.1
v1.0.2
```

**删除标签**

```
git tag -d v1.0.1
```



### **4.6 变基**

在 Git 中整合来自不同分支的修改主要有两种方法：merge 以及 rebase

首先我们创建两个分支：

```shell
$ vim text1.txt           ##第一次提交
$ git add text1.txt 
$ git commit -m "C1"
[master (root-commit) b6755a0] C1
 1 file changed, 1 insertion(+)
 create mode 100644 text1.txt
 
$ vim text1.txt           ##第二次提交
$ git commit -am "C2"
[master 785413f] C2
 1 file changed, 1 insertion(+)

##创建orgin和mywork分支
$ git branch origin
$ git branch mywork
```

如下图所示：

![1546501776687](/home/why/.config/Typora/typora-user-images/1546501776687.png)



现在我们操作origin分支

```sh
$ git checkout origin

### 第一次提交
$ vim index.html
$ git add index.html 
$ git commit -m "C3"
[origin 5ca17aa] C3
 1 file changed, 1 insertion(+)
 create mode 100644 index.html

### 第二次提交
$ vim index.html 
$ git commit -am "C4"
[origin 3ca424a] C4
 1 file changed, 1 insertion(+)
$ ls
index.html  text1.txt
```

再操作mywork分支

```sh
$ git checkout mywork 
Switched to branch 'mywork'

### 第一次提交
$ vim file.txt
$ git add file.txt 
$ git commit -m "C5"
[mywork 6088845] C5
 1 file changed, 2 insertions(+)
 create mode 100644 file.txt
 
## 第二次提交
$ vim file.txt 
$ git commit -am "C6"
[mywork e881ada] C6
 1 file changed, 1 insertion(+), 1 deletion(-)
```

此时的分支图应该是这样的：

![1546502694962](/home/why/.config/Typora/typora-user-images/1546502694962.png)



之前介绍过，整合分支最容易的方法是 merge 命令。 它会把两个分支的最新快照（C4 和 C6）以及二者最近的共同祖先（C2）进行三方合并，合并的结果是生成一个新的快照（并提交）。

![1546502757412](/home/why/.config/Typora/typora-user-images/1546502757412.png)

在这里，其实是将origin分支合并到了mywork分支上。

其实，还有另一种做法，它可以提取在 C5 和 C6 中引入的补丁和修改（相对于共同祖先C2所做的修改），然后在 C4 的基础上应用一次。 在 Git 中，这种操作就叫做**变基**。 

你可以使用 `git rebase` 命令将提交到某一分支上的所有修改都移至另一分支上，就好像“重新播放”一样。

在上面这个例子中，先切换分支

```
$ git checkout mywork 
```

然后使用`git log`查看

```
$ git log
commit e881ada1bc790ce28f397bb138df7badff4b011b (HEAD -> mywork)
Author: why <why@email.com>
Date:   Thu Jan 3 15:54:51 2019 +0800

    C6

commit 608884576f8fa9e0b7bb464bf2ef6eebe8568b08
Author: why <why@email.com>
Date:   Thu Jan 3 15:54:39 2019 +0800

    C5

commit 785413f52e83dda7018cfdde1aa3d7287d3406d9 (master)
Author: why <why@email.com>
Date:   Thu Jan 3 15:40:31 2019 +0800

    C2

commit b6755a0ffc80f0242d9635e4cfde29b12b2a6c2c
Author: why <why@email.com>
Date:   Thu Jan 3 15:40:13 2019 +0800

    C1
```

然后执行变基操作

```
$ git rebase origin
```

再查看日志

```
$ git log
commit b141a8f84c4cfe6b9d552c32fd4cb436c64b3a29 (HEAD -> mywork)
Author: why <why@email.com>
Date:   Thu Jan 3 15:54:51 2019 +0800

    C6

commit e92aefc7bb72578954c64082bf7f3b955cf76ee8
Author: why <why@email.com>
Date:   Thu Jan 3 15:54:39 2019 +0800

    C5

commit 3ca424a50c0adce52fde1059be815ae4b6df8d86 (origin)
Author: why <why@email.com>
Date:   Thu Jan 3 15:51:08 2019 +0800

    C4

commit 5ca17aa846557eb542f7810afeb411d260176a84
Author: why <why@email.com>
Date:   Thu Jan 3 15:50:51 2019 +0800

    C3

commit 785413f52e83dda7018cfdde1aa3d7287d3406d9 (master)
Author: why <why@email.com>
Date:   Thu Jan 3 15:40:31 2019 +0800

    C2

commit b6755a0ffc80f0242d9635e4cfde29b12b2a6c2c
Author: why <why@email.com>
Date:   Thu Jan 3 15:40:13 2019 +0800

    C1
```

此时，可以看到C5和C6的对象已经变了，分支的情况变成这样：

![1546503621785](/home/why/.config/Typora/typora-user-images/1546503621785.png)



于是我们可以知道，执行完`git rebase`之后，原来的 C5 和 C6 快照就没用了，其实就变成了一条分支 ，于是此时可以将master(在C2上)和mywork分支合并了

```
$ git checkout master 
$ git merge mywork 
```

> **注意：** git rebase会修改提交历史，所以不要在共享的分支上使用它



### **4.7 保存现场**

有时，当你在项目的一部分上已经工作一段时间后，但是还未提交，此时你想切换到另一个分支上去做其它事情，那么就可以使用`git stash`命令来保存现场。

为了演示，进入项目并改动几个文件，然后可能暂存其中的一个改动。 如果运行 `git status`，可以看到有改动的状态：

```sh
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   file.txt
```

然后我们切换到origin分支

```sh
$ git checkout origin 
error: Your local changes to the following files would be overwritten by checkout:
        file.txt
Please commit your changes or stash them before you switch branches.
Aborting

```

可以看到报错了，也就是说，如果你此时想要切换分支，但是还不想提交之前的工作，那么就需要`git stash`命令来保存现场。

```sh
$ git stash 
Saved working directory and index state WIP on master: b141a8f C6

$ git checkout origin 
Switched to branch 'origin'
```

如果我们要查看所保存的对象，可以使用`git stash list`

```
$ git stash list
stash@{0}: WIP on master: b141a8f C6
```

当我们切回分支，想要恢复现场，那么可以使用

```sh
$ git stash apply stash@{0} 
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   file.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

可以看到，之前暂存的文件却没有重新暂存。如果想要那样的话，必须使用 --index 选项来运行`git stash apply`命令。

```sh
$ git stash apply --index
```

恢复之后，就可以删除栈中的内容了

```sh
$ git stash drop stash@{2} 
```

其实还有一个命令`git stash pop`，它就相当于执行了`git stash apply`和`git stash drop`



## **5.远程仓库**

### **5.1 GitHub的使用**

首先，我们使用一个邮箱去GitHub上注册一个帐号 

![图片.png-40.8kB](http://static.zybuluo.com/huiyun/98uxdbhv2dw0aa8nhsk5wloi/%E5%9B%BE%E7%89%87.png)

然后登陆GitHub，创建一个仓库

![图片.png-48.3kB](http://static.zybuluo.com/huiyun/ez8e0q266r5zz2m51qbrpejh/%E5%9B%BE%E7%89%87.png)



**将本地仓库推送到远程服务器上**

```sh
$ git remote add origin https://github.com/yungestudying/yunstudying.git
$ git push -u origin master
Username for 'https://github.com':  yungestudying      ##输入github帐号
Password for 'https://yungestudying@github.com':   	   ##输入github密码
```



**使用SSH连接GitHub**

1.首先我们需要在本地生成密钥文件

```sh
$ ssh-keygen -o -t rsa -b 4096 -C "email@example.com"
```

2.将SSH公钥复制到GitHub上去

![选区_674.png-66.9kB](http://static.zybuluo.com/huiyun/usfacuvmnyumv19ukuv79qxy/%E9%80%89%E5%8C%BA_674.png)

3.测试

```sh
$ ssh -T git@github.com
```

出现以下提示表示成功

```sh
Hi yungestudying! You've successfully authenticated, but GitHub does not provide shell access.
```

4.添加远程仓库

```
$ git remote add why git@github.com:yungestudying/yunstudying.git
```

5.推送远程代码

```
$ git push why master
```

**基于git的开发模型**

- develop分支：频繁变化的一个分支，开发人员经常push代码
- test分支：供测试使用的一个分支
- master：生产发布分支



### **5.2 远程操作**

**查看远程仓库**

比如，我想查看本地仓库与哪些远程仓库相关联
```sh
$ git remote 
origin    //这个origin是我们自定义的一个名称
```

如果想查看更详细点的信息，可以使用`git remote -v`
```sh
$ git remote -v
origin  https://github.com/yungestudying/yunstudying.git (fetch)
origin  https://github.com/yungestudying/yunstudying.git (push)
```

如何还想查看某个分支更详细的信息，可以使用
```sh
$ git remote show origin 
* 远程 origin
  获取地址：https://github.com/yungestudying/yunstudying.git
  推送地址：https://github.com/yungestudying/yunstudying.git
  HEAD 分支：master
  远程分支：
    master 已跟踪
  为 'git pull' 配置的本地分支：
    master 与远程 master 合并
  为 'git push' 配置的本地引用：
    master 推送至 master (最新)
```
这个命令列出了当你在特定的分支上执行 `git push` 会自动地推送到哪一个远程分支。 它也同样地列出了哪些远程分支不在你的本地，哪些远程分支已经从服务器上移除了，还有当你执行 `git pull` 时哪些分支会自动合并。

**添加远程仓库**

运行 `git remote add <shortname> <url> `添加一个新的远程 Git 仓库
```sh
git remote add pb https://github.com/paulboone/ticgit
```

现在你可以在命令行中使用字符串 pb 来代替整个 URL

**拉取和推送**

从远程仓库中获得数据，可以执行：
```sh
git fetch [remote-name]
```
这个命令会访问远程仓库，从中拉取所有你还没有的数据。执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。

>**说明：**必须注意 `git fetch` 命令会将数据拉取到你的本地仓库 - 它并不会自动合并或修改你当前的工作。 当准备好时你必须手动将其合并入你的工作。

那如果想把本地数据推送到远程仓库，可以执行：
```sh
git push origin master
```

>**说明：**只有当你有所克隆服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。 当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会毫无疑问地被拒绝。 你必须先将他们的工作拉取下来并将其合并进你的工作后才能推送。 





### **5.3 远程分支**

git的远程分支以 (remote)/(branch) 形式命名。我们可以使用如下命令查看：
```sh
$ git branch -av
* master                1f91083 v4
  remotes/origin/HEAD   -> origin/master
  remotes/origin/master 1f91083 v4
```

为了更好的理解远程分支，我们举个例子：

我们现在从 github 上克隆一个项目。
```sh
$ git clone git@github.com:yungestudying/yunstudying.git
```
Git 的 clone 命令会为你自动将其命名为 origin，拉取它的所有数据，创建一个指向它的 master 分支的指针，并且在本地将其命名为 origin/master。 Git 也会给你一个与 origin 的 master 分支在指向同一个地方的本地 master 分支，这样你就有工作的基础。

![图片.png-52.6kB](http://static.zybuluo.com/huiyun/y095fqoexhq0lt25ud5lyjc8/%E5%9B%BE%E7%89%87.png)


我们可以使用`git branch -av`进行查看
```sh
$ git branch -av
* master                1f91083 v4
  remotes/origin/HEAD   -> origin/master
  remotes/origin/master 1f91083 v4
```

如果你在本地的 master 分支做了一些工作，然而在同一时间，其他人推送提交到 git服务器上 并更新了它的 master 分支，那么你的提交历史将向不同的方向前进。 也许，只要你不与 origin 服务器连接，你的 origin/master 指针就不会移动。
![图片.png-49.5kB](http://static.zybuluo.com/huiyun/y1g1vmnf59norgojk9whxn0v/%E5%9B%BE%E7%89%87.png)

如果要同步你的工作，运行 `git fetch origin` 命令。 这个命令查找 “origin” 是哪一个服务器（在本例中，它是 `git@github.com:yungestudying/yunstudying.git`），从中抓取本地没有的数据，并且更新本地数据库，移动 origin/master 指针指向新的、更新后的位置。
![图片.png-62.3kB](http://static.zybuluo.com/huiyun/d9an0v05tyte329sfjz4r4el/%E5%9B%BE%E7%89%87.png)

>**说明：** `当 git fetch` 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。 然而，有一个命令叫作 `git pull` 在大多数情况下它的含义是一个 `git fetch` 紧接着一个 `git merge` 命令。 `git pull` 会查找当前分支所跟踪的服务器与分支，从服务器上抓取数据然后尝试合并入那个远程分支。

好的，现在我们就来演示一下，如果同时有两个用户推送数据，该怎么搞？

1.首先，我们创建两个用户，如果你事先使用`git config --global`,那么请先删除`.gitconfig`文件
```sh
$ mkdir mygit1
$ mkdir mygit2

======在mygit1上的操作
$ cd mygit1/
$ git init
$ git config --local user.name "Bob"
$ git config --local user.email "Bob@qq.com"
$ git config --list
$ git remote add origin git@github.com:yungestudying/yunstudying.git
$ git pull origin master

======在mygit2上的操作
$ cd mygit2/
$ git init 
$ git config --local user.name "Alice"
$ git config --local user.email "Alice@qq.com"
$ git config --list
$ git remote add origin git@github.com:yungestudying/yunstudying.git
$ git pull origin master
```

2.修改文件并提交
```sh
$ cat text.txt 
HelloWorld
Welcome to you
111111
222222


$ cat text.txt 
HelloWorld
Welcome to you
111111
333333
```

3.使用Alice用户提交代码
```sh
$ git push origin master
对象计数中: 3, 完成.
Delta compression using up to 4 threads.
压缩对象中: 100% (2/2), 完成.
写入对象中: 100% (3/3), 293 bytes | 293.00 KiB/s, 完成.
Total 3 (delta 0), reused 0 (delta 0)
To github.com:yungestudying/yunstudying.git
   1f91083..27b90f2  master -> master
```

4.再使用Bob用户提交代码
```sh
$ git push origin master
To github.com:yungestudying/yunstudying.git
 ! [rejected]        master -> master (fetch first)
error: 无法推送一些引用到 'git@github.com:yungestudying/yunstudying.git'
提示：更新被拒绝，因为远程仓库包含您本地尚不存在的提交。这通常是因为另外
提示：一个仓库已向该引用进行了推送。再次推送前，您可能需要先整合远程变更
提示：（如 'git pull ...'）。
提示：详见 'git push --help' 中的 'Note about fast-forwards' 小节。
```

可以看到，由于Alice用户先提交了，所以导致远程master版本与本地origin/master的分支不一致，于是我们首先应该使用`git pull`命令获取最新的数据
```sh
$ git pull 
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
展开对象中: 100% (3/3), 完成.
来自 github.com:yungestudying/yunstudying
   1f91083..27b90f2  master     -> origin/master
自动合并 text.txt
冲突（内容）：合并冲突于 text.txt
自动合并失败，修正冲突然后提交修正的结果。
```

可以看到，`git pull`命令会将远程master分支拉取下来，并自动合并。由于远程test.txt和本地test.txt的内容不一样，所以产生合并冲突问题。

于是我们可以编辑`vim test.txt`手动解决冲突
```sh
HelloWorld
Welcome to you
111111
<<<<<<< HEAD    //当前本地冲突文件的内容
222222
=======         //冲突文件符号
333333
>>>>>>> 27b90f23f7fdecea9814f083c0b26f0b7adc5ad0    //远程冲突文件内容
```

手动修改冲突后，再查看
```sh
$ git status
位于分支 master
您的分支和 'origin/master' 出现了偏离，
并且分别有 1 和 1 处不同的提交。
  （使用 "git pull" 来合并远程分支）

您有尚未合并的路径。
  （解决冲突并运行 "git commit"）
  （使用 "git merge --abort" 终止合并）

未合并的路径：
  （使用 "git add <文件>..." 标记解决方案）

        双方修改：   text.txt

修改尚未加入提交（使用 "git add" 和/或 "git commit -a"）
```

于是我们按照上面提示操作：
```sh
$ git add text.txt 
$ git commit 
$ git push
```





## **6.GitLab的使用**

### **6.1 GitLab的安装**

> **安装前说明**：使用GitLab需要至少4GB可用内存(RAM + Swap)! 由于操作系统和其他正在运行的应用也会使用内存, 所以安装GitLab前一定要注意当前服务器至少有4GB的可用内存. 少于4GB内存会导致在reconfigure的时候出现各种诡异的问题, 而且在使用过程中也经常会出现5xx错误.



**1.下载GitLab仓库**

```
curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```



**2.安装依赖环境**

```sh
yum install curl openssh-server openssh-clients cronie policycoreutils-python git -y
```



**3.安装GitLab**

```
yum install gitlab-ce -y
```



**4.修改配置文件**：`/etc/gitlab/gitlab.rb`

```sh
## 本机地址或域名
external_url 'http://127.0.0.1'  

## 更改时区
gitlab_rails['time_zone'] = 'Asia/Shanghai'

## 添加到GitLab信任代理
gitlab_rails['trusted_proxies'] = ['127.0.0.1']

## Git仓库目录
git_data_dirs({
	"default" => {
		"path" => "/mnt/nfs-01/git-data"
	}
})

## smtp相关配置,如果不配置则使用系统自带的Postfix
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
gitlab_rails['smtp_port'] = 25
gitlab_rails['smtp_authentication'] = "plain"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_user_name'] = "xx@oo.com"
gitlab_rails['smtp_password'] = "****"
gitlab_rails['smtp_domain'] = "smtp.qq.com"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['gitlab_email_from'] = 'xx@oo.com'
gitlab_rails['gitlab_email_reply_to'] = 'xx@oo.com'

### 设置GitLab监听的端口
gitlab_workhorse['enable'] = true
gitlab_workhorse['listen_network'] = "tcp"
gitlab_workhorse['listen_addr'] = "127.0.0.1:30008"

## 修改unicorn端口，默认是8080,可能会占用tomcat，修改为其它端口
unicorn['listen'] = 'localhost'
unicorn['port'] = 30009

## 不使用GitLab自带的Nginx
nginx['enable'] = false

## 修改prometheus端口，默认为9090,可能被占用，修改为其他端口
prometheus['enable'] = true
prometheus['listen_address'] = 'localhost:30010'

## 限制unicorn启动的进程数
unicorn['worker_processes'] = 1
unicorn['worker_timeout'] = 60
unicorn['worker_memory_limit_min'] = "200 * 1 << 20"
unicorn['worker_memory_limit_max'] = "400 * 1 << 20"

```



**5.启动服务**

```sh
$ gitlab-ctl reconfigure
```



**6.Nginx的配置**

```
server {
    listen 80;
    server_name _;
    client_max_body_size 2G;

    location / {
        proxy_pass http://127.0.0.1:30008;
    }
}
```

**7.访问网站，提示修改密码**

![1546830313446](/home/why/.config/Typora/typora-user-images/1546830313446.png)

如果忘记密码，还可以这样修改：

```shell
$ gitlab-rails console production

irb(main):001:0> user = User.where(id: 1).first
irb(main):002:0> user.password=12345678
irb(main):003:0> user.password_confirmation=12345678
irb(main):004:0> user.save!
irb(main):005:0> quit
```



### **6.2 禁止用户注册**

一般来说，公司内部的GitLab是禁止其它人注册的，而是由管理员给成员分配帐号。

![1546831302160](/home/why/.config/Typora/typora-user-images/1546831302160.png)



### **6.3 创建用户和组**

![1546831705928](/home/why/.config/Typora/typora-user-images/1546831705928.png)



### **6.4 使用GitLab**

GitLab的地址：http://git.autoflysoft.com



**1.安装客户端**

如果你的电脑是Linux或Mac OS,则使用它们自己的终端

如果你的电脑是Windows，则安装Git Bash

GitBash下载地址:[点击下载][1]
![image.png-87.6kB](http://static.zybuluo.com/huiyun/wr0eb8gc4ej3i2pomziy80ku/image.png)



下面是安装git bash的操作

![image.png-31.3kB](http://static.zybuluo.com/huiyun/8eswnamskd5060mww68i7xe5/image.png)



![image.png-25.7kB](http://static.zybuluo.com/huiyun/p85n4foz4wpvq651quka7jhc/image.png)



![image.png-32.8kB](http://static.zybuluo.com/huiyun/h5u91pbpj4dtj4fud6zyipiv/image.png)



![image.png-35.6kB](http://static.zybuluo.com/huiyun/zzoszv425qh9objqpsil5h5b/image.png)

后面的操作都点击下一步即可

**2.生成新的密钥对**

```sh
ssh-keygen -o -t rsa -b 4096 -C "email@example.com"

说明：email@example.com可以填你的邮箱
```
例如：
![image.png-27.6kB](http://static.zybuluo.com/huiyun/vqtglaohtfzibvzzvdwbmg74/image.png)

>**说明：**提示输入密码可以不用理，敲Enter键即可

**3.将SSH密钥添加到GitLab帐户**

```sh
cat ~/.ssh/id_rsa.pub | clip
```
>**说明**：这个操作相当于复制id_rsa.pub里的内容

然后登陆你的gitlab账户，将id_rsa.pub的内容粘贴到下图所示处
![image.png-14.9kB](http://static.zybuluo.com/huiyun/2ss2k9c6uw98vydop8ijgf0t/image.png)
![image.png-280.7kB](http://static.zybuluo.com/huiyun/sgo9tcyvyl758pe9j7t48sz4/image.png)

**4.测试所有设置是否正确**

```sh
ssh -T -p 90 git@git.autoflysoft.com
```

如果返回以下内容，则说明成功
```
Welcome to GitLab, @why!
```

**5.配置config**
为了能够push代码至gitlab，需要编辑~/.ssh/config
```sh
vim ~/.ssh/config

编辑内容如下：
host git.autoflysoft.com
port 90
```

编辑完成后，先敲【Esc】键，然后`:wq`退出编辑

此时就可以使用git上传代码至gitlab了

**6.进入项目目录**

```sh
git init
git add .
git commit -m "你的注释"
git remote add origin 项目地址
git push -u origin maste
```

