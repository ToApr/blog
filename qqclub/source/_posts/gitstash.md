---
title: git stash 隐藏和恢复工作区
date: 2019-11-21 10:33:46
tags:  git
categories: 技术
---

# git stash 

保存当前工作进度，会把暂存区和工作区的改动保存起来。执行完这个命令后，在运行git status命令，就会发现当前是一个干净的工作区，没有任何改动。使用git stash save 'message...'可以添加一些注释

# git stash list 

查看隐藏的进度的列表。意味着git stash 可以执行多次。

# git stash pop [–index] [stash_id]

- git stash pop 恢复最新的隐藏区到工作区
- git stash pop stash@{0}  恢复指定的隐藏区到工作区

# git stash clear

删除所有的隐藏区

参考链接：
https://blog.csdn.net/daguanjia11/article/details/73810577