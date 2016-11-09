---
title: 我的Git常用命令整理
date: 2015-10-11 15:52:30
tags: [git]
---

Git版本控制器（Github）在我的实际开发过程中起到很重要的作用，它使开发过程更有效率。通过Git可以记录项目中代码的每一次变化，并且可以“穿越时空”回到过去的任何状态。我在开发中主要使用Git的命令行提交每次的变化和克隆线上项目，自我感觉Git命令非常重要,所以今天总结一下我平常使用的一些Git命令。

* 从现有仓库克隆项目到你的工作空间
```
$ git clone [url]
```
* 要确定哪些文件当前处于什么状态：
```
$ git status
```
* 跟踪新文件，要跟踪 README 文件，运行：
```
$ git add README
```
* 跟踪所有的文件：
```
$ git add --all
```
* 提交更新,git add 命令过后就要运行提交命令：
```
$ git commit -m "此处添加本次提交的信息"
```
* 把修改的文件先提交到stage,然后再从stage提交到branch：
```
$ git commit -a 
```
* 将git add 和 git commit 命令合并，但此命令不适用于新建的文件（没有被add的文件）：
```
$ git commit -am "此处添加本次提交的信息"
```
* 查看和对比修改的内容：
```
$ git diff <filename>
```
* 查看提交历史：
```
$ git log
```
* 创建名为branchName的分支：
```
$ git brach branchName 
```
* 放弃工作区和index的改动,HEAD指针仍然指向当前的commit：
```
$ git reset --hard HEAD 
```
回退到上一个版本(等价于 git reset --hard HEAD~1) 
```
$ git reset --hard HEAD^ 
```
* 回退到指定版本号状态
```
$ git reset --hard 20038
```
* 合并指定分支到当前分支（默认使用fast forward方式）：
```
$ git merge <branch>
```
* 删除分支：
```
$ git branch -d <branch>
```
* 从远程获取最新版本到本地仓库，并自动merge到本地分支：
```
$ git pull origin master
```
* 丢弃你所有的本地改动与提交，可以到服务器上获取最新的版本并将你本地主分支指向到它：
```
$ git fetch origin
```
		
以上是就是Git的常用命令，可以完成大部分版本控制任务。我接触Git不算太长时间，对Git的命令和流程还没有完全理解，只停留在会用的状态，做此总结算是一个巩固，希望以后能够更深刻的理解Git这个神器。
>我的GitHub账号[https://github.com/w3cay](https://github.com/w3cay)