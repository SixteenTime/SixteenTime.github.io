---
title: Git相关知识点
date: 2021-12-21 11:07:51
tags: [学习,前端面试，Git]
categories:
- 计算机基础复习
---

------

*本文来源于https://github.com/CyC2018/CS-Notes，是在阅读该资源时的笔记整理，但有大部分的内容和原文一致。并且因为本次复习是争对前端面试的准备，所以也会选择性复习，对于深刻的，计算类知识点只会粗略扫过*。

------

1.集中式与分布式

Git属于分布式版本控制系统，分布式的好处是每个成员的电脑上都有一份完整代码，而集中式的只有中心服务器才有。并且集中式版本控制系统一旦中心服务器出问题所有成员都无法使用，还必须依赖连网使用，分布式则不存在这些问题。分布式版本控制建立新分支、合并分支操作迅速，集中式新建一个分支相当于复制一份完整代码，操作较慢。

2.中心服务器

Git的中心服务器是GitHub。中心服务器用来交换成员的修改。

3.工作流

新建一个目录并且Git初始化后，该目录就成为一个工作仓库。该工作区有一个.git文件夹，它是Git的版本库。Git 的版本库有一个称为 Stage 的暂存区以及最后的 History 版本库，History 存储所有分支信息，使用一个 HEAD 指针指向当前分支。

具体工作流如下图所示：

![](/images/react/2021122111.png)

执行命令git add files 将仓库内的文件存入Stage缓存区，执行git commit命令将这些存在Stage的文件数据存入History区，此时Stage也被清空。执行命令git reset --files使用当前分支上的修改覆盖暂存区，用来撤销最后一次 git add files，执行命令git checkout --file 使用暂存区的修改覆盖工作目录，用来撤销本地修改。执行git push origin [branch]:[branch]将本地分支数据上传到远程中央仓库分支中。执行git pull从远程仓库拉回数据到本地仓库。

4.分支实现和冲突

HEAD指针总是指向当前分支指针，在新建分支时，新建的分支指针会指向当前节点，并且HEAD指针指向新分支指针。每次提交只有当前分支指针会改变。

如果两个分支都进行了修改，当它们合并时会产生冲突，必须删除冲突才能进行合并。

5.SSH传输设置

Git 仓库和 Github 中心仓库之间的传输是通过 SSH 加密。

本地设置SSH Key可以通过命令`ssh-keygen -t rsa -C "youremail@example.com"`来实现。其中出现在.ssh目录的id_rsa.pub里的数据就是SSH的公钥。

6.储存

在一个分支操作后，如果还没有将修改提交到分支上，此时进行切换分支，那么另一个分支上也会有修改内容，这是所有分支公用一个工作区的缘故。可以使用命令git stash将当前分支的修改存储起来，此时当前工作区的所有修改都会被存到栈中，也就是说当前工作区是干净的，没有任何未提交的修改。此时就可以安全的切换到其它分支上了。

7.Git常用命令

git init 设置本地目录为新的Git仓库

git clone [url] 克隆一个代码仓库

git add [file1] [file2] 增加file1、file2到暂存区

git add . 将当前目录下全部文件存入暂存区。

git rm [file1] [file2] 删除指定文件，并在暂存区中也删除

git commit -m [message] 提交暂存区到仓库区（History），并备注消息

git commit -a 直接从暂存区提交进仓库区

git branch 列出所有本地分支

git branch -r 列出所有远程分支

git branch -a 列出所有分支，包括本地分支和远程分支。

git branch [branch] 新建分支

git checkout [branch-name] 切换到指定的分支进行工作

git merge [branch] 合并指定分支到当前分支