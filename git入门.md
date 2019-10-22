---
title: git入门
date: 2018-10-04 21:31:34
tags:
---

### 1. 首先创建一个目录,进入该目录，运行`git init`命令,生成.git 目录
![git init](/img/git-init.jpg "git init")

### 2. 然后创建新的文件，运行`git status -sb`,查看工作区的文件状态，git会忽略空目录，当目录中有文件时，则表示有新内容要提交。运行`git add .`命令，将当前目录修改的文件提交到暂存区，再运行`git status -sb`命令，发现修改的文件都已提交到暂存区
![git add](/img/git-add.jpg "git add")

### 3. 最后运行`git commit -m "remarks"`将暂存区内容提交到本地仓库
![git commit](/img/git-commit.jpg "git commit")
