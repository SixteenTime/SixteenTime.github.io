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

## 1.集中式与分布式

Git属于<u>分布式版本控制系统</u>，分布式的好处是每个成员的电脑上都有一份完整代码，而<u>集中式</u>的只有中心服务器才有。并且集中式版本控制系统一旦中心服务器出问题所有成员都无法使用，还必须依赖连网使用，分布式则不存在这些问题。分布式版本控制建立新分支、合并分支操作迅速，集中式新建一个分支相当于复制一份完整代码，操作较慢。

## 2.中心服务器

Git的中心服务器是<u>GitHub</u>。中心服务器用来交换成员的修改。

## 3.工作流

新建一个目录并且Git初始化后，该目录就成为一个工作仓库。该工作区有一个`.git`文件夹，它是Git的版本库。Git 的版本库有一个称为 `Stage` 的暂存区以及最后的 `History` 版本库，`History` 存储所有分支信息，使用一个 `HEAD` 指针指向当前分支。

具体工作流如下图所示：

![](/images/react/2021122111.png)

执行命令`git add files` 将仓库内的文件存入`Stage`缓存区，执行`git commit`命令将这些存在`Stage`的文件数据存入`History`区，此时`Stage`也被清空。执行命令`git reset --files`使用当前分支上的修改覆盖暂存区，用来撤销最后一次 `git add files`，执行命令`git checkout --file` 使用暂存区的修改覆盖工作目录，用来撤销本地修改。

## 4.分支实现和冲突

`HEAD`指针总是指向当前分支指针，在新建分支时，新建的分支指针会指向当前节点，并且`HEAD`指针指向新分支指针。每次提交只有当前分支指针会改变。

如果两个分支都进行了修改，当它们合并时会产生冲突，必须删除冲突才能进行合并。

## 5.SSH传输设置

Git 仓库和 Github 中心仓库之间的传输是通过 SSH 加密。

本地设置SSH Key可以通过命令`ssh-keygen -t rsa -C "youremail@example.com"`来实现。其中出现在`.ssh`目录的`id_rsa.pub`里的数据就是SSH的公钥。

## 6.储存

在一个分支操作后，如果还没有将修改提交到分支上，此时进行切换分支，那么另一个分支上也会有修改内容，这是所有分支公用一个工作区的缘故。可以使用命令`git stash`将当前分支的修改存储起来，此时当前工作区的所有修改都会被存到栈中，也就是说当前工作区是干净的，没有任何未提交的修改。此时就可以安全的切换到其它分支上了。

## 7.Git常用命令

**初始化操作**

`git init` 设置本地目录为新的Git仓库

`git clone [url]` 克隆一个代码仓库

**增加文件操作**

`git add [file1] [file2]` 增加file1、file2到暂存区

`git add .` 将当前目录下全部文件存入暂存区。

**删除文件操作**

`git rm [file1] [file2]` 删除指定文件，并在暂存区中也删除

**代码提交操作**

`git commit -m [message]` 提交暂存区到仓库区（History），并备注消息

`git commit -a` 直接从暂存区提交进仓库区

**分支相关操作**

`git branch` 列出所有本地分支

`git branch -r` 列出所有远程分支

`git branch -a` 列出所有分支，包括本地分支和远程分支。

`git branch [branch]` 新建分支

`git checkout [branch-name]` 切换到指定的分支进行工作

`git merge [branch]` 合并指定分支到当前分支

`git branch -d [branch-name]` 删除指定的本地分支

`git push origin --delete [branch-name]` 删除远程分支

`git branch -dr [remote/branch]` 删除远程分支

**查看信息操作**

`git status` 显示有变更文件

`git log` 显示当前分支的版本历史

**远程同步操作**

`git remote -v` 显示所有的远程仓库

`git remote show [remote]` 显示某个远程仓库信息

`git remote add [remote]` 新增某个远程仓库

`git pull [remote] [branch]` 从远程仓库的某一分支拉取到本地

`git push [remote] [branch]` 上传本地指定分支到远程仓库

`git push -f [remote] [branch]` 强制推送当前分支到远程仓库，即使有冲突

`git push [remote] --all` 推送所有分支到远程仓库

**撤销操作**

`git checkout [commit] [file]` 恢复某个commit的指定文件到暂存区和工作区

`git reset --hard` 重置暂存区与工作区，与上一次commit保持一致

`git reset [commit]` 重置当前分支指针为指定commit，同时重置暂存区，但工作区不变

`git reset --hard [commit]` 重置当前HEAD为指定commit，同时重置暂存区与工作区。