---
layout: post
title: 在终端中使用Git
description: GitHub作为一个成熟的开源平台，成为了大量程序员活跃的地方，Git命令的使用也应该是一项必备技能，想要优雅的使用Git，这些命令就能满足。
category: blog
---
<h2>本地操作</h2>
* `ssh-keygen -t rsa -C "email@xxx.com"`
* `git init` 把这个目录变成Git可以管理的仓库
* `git diff test.txt` 查看文件具体修改的内容
* `git add test.txt` 把文件添加到仓库（实际是把文件修改添加到暂存区）
* `git status` 查看仓库当前的状态
* `git commit -m "info"` 把文件提交到仓库（把暂存区的所有内容提交到当前分支）
* `git log` 显示最近到最远的提交日志
* `git log --pretty=oneline` 只显示**commit id**（版本号）和“info”说明信息
* `git reset --hard HEAD^` 回到上一个版本
> HEAD表示当前版本，上一个版本是HEAD^，上上一个版本是HEAD^^，当前网上100个版本可以写成HEAD~100。
`git reset --hard 232333`（回到某一个版本直接跟着commit id的前几位）
`git diff HEAD -- test.txt`（查看工作区和版本库里面最新版本的区别）

* `git reflog` 记录着每一次的命令，（可查到对应每一个**commit**的**commmit id**）
* `git checkout -- test.txt` 撤销文件在工作区的修改
* `git checkout .` 撤销文件在工作区的所有修改

    > 两种情况
一种是test.txt自修改后还没被放到暂存区，现在，撤销修改就是回到和版本库一模一样的状态
一种是test.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加带暂存区后的状态
总之，就是让这个文件回到最近一次**git commit** 或**git add**时的状态
`git checkout -- test.txt` 命令中的--很重要，没有--就变成了切换到另一个分支的命令

* `git reset  HEAD file` 把暂存区的修改撤销掉，从新放回工作区（如果想改变已经**git add**到暂存区的文件用这个命令，）
* `git rm` 删除一个文件
    > 如果test.txt文件在文件管理器中被删除了，
但版本库中没有被删掉，这时可以`git rm test.txt`把文件从版本库中删掉，然后commit

* `git checkout -- test.txt` 找回在文件管理器中删除的文件，前提是文件已提交到git仓库
* `git checkout -b dev` 创建dev分支，然后切换到dev分支（相当于**git branch dev** 然后**git checkout dev**）
* `git branch` 查看当前分支
* `git branch -d dev` 删除dev分支
* `git merge dev` 把dev分支的工作成果合并到当前分支上
* `git stash` 把当前工作现场储藏起来， 等以后回复现场后继续工作。
* `git stash list 显示储藏的工作现场列表
* `git stash apply` 恢复工作现场，stash不删除
* `git stash drop` 删除stash
* `git stash pop` 恢复并删除stash
* `git tag <name>` 打标签
* `git tag` 查看所有标签
* `git tag <name> <4546546(commit id)>` 在对应的提交上打标签
* `git show <tagname>` 查看标签信息
* `git tag -a <tagname> -m "info"` 创建有说明的标签
* `git tag -d <tagname>` 删除标签

<h2>远程操作</h2>
* `git clone <版本库的网址>` 从远程主机克隆一个版本库（克隆到当前目录，主机名默认为origin）
* `git clone <版本库的网址> <本地目录名>` 从远程主机克隆一个版本库（克隆到指定目录）
* ` git remote` 列出所有远程主机
* `git remote -v` 查看远程主机的详细信息
* `git remote add <主机名> <网址>` 在本地的代码仓库下运行，添加远程主机，与之关联（主机名通常设为origin）
* `git remote rm <主机名>` 删除远程主机
* `git remote rename <原主机名> <新主机名>` 给远程主机改名
* `git push -u origin master` 把本地库的所有内容推送到远程库上（-u 指定一个默认主机，第一次指定后，以后可以不用-u）
* `git push origin <分支名>` 把该分支上的本地提交推送到远程库，如果远程仓库没有此分支，自动创建。
* `git push -f` 强推把本地的内容推送到远程仓库
* `git fetch` 将远程分支信息获取到本地
* `git checkout -b dev origin/dev` 创建远程origin的dev分支到本地dev
* `git branch -a` 查看远程分支
* `git branch --set-upstream dev origin/dev` 设置本地dev与远程origin/dev的链接
* `git pull` 把最新的提交从与本地对应的远程分支上的内容抓下来
* `git push origin <tagname>` 推送某个标签到远程
* `git push origin --tags` 推送全部尚未推送到远程的标签
* `git tag -d <tagname>` 为了删除远程的标签，现在本地删除
* `git push origin :refs/tags/<tagname>` 为了删除远程的标签，再在远程删除

这其中的部分命令是根据[廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)再加上我在平常使用中积累而成，基本上能解决日常使用所需。

