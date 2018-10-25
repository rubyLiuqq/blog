---
title: git 命令
date: 2018-09-06 10:30:23
tags: [git]
categories: 编码规范
toc: true
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%281%29.jpeg
---

## 删除分支
【git 删除本地分支】：`git branch -D br`
【git 删除远程分支】：`git push origin :br`  (origin 后面有空格)
【恢复删除掉的分支】：`git branch 分支名 hash_val`
    其中 hash_val 为散列值，如果不知，可利用 reflog。
    reflog命令：显示整个本地仓储的commit，包括所有branch的commit，甚至包括已经撤销的commit。
    eg: git branch 分支名 HEAD@{4}

【将远程的某个分支，拉取到本地】： 
    方法一：
      `git checkout -b 本地分支名x  origin/远程分支名x`
    方法二：
      `git fetch origin 远程分支名x:本地分支名x`

## 回退回滚取消提交返回上一版本
【没有push】：`git reset [ --soft || --hard || --mixed ] commitId`
eg：git reset --hard 139dcfaa558e3276b30b6b2e5cbbb9c00bbdca96  (ID)

--soft	保留源码（work），回退到 commit 信息到某个版本，不涉及index的回退
        如果还需要提交，直接commit
        **commit 回退，index和work不会**

--hard	源码也会回退到某个版本，commit和index 都回退到某个版本。
        commit、index和work都会改变
        **push之后千万不要使用，因为线上代码没变，包括commit和index；**
        **但把本地代码修改完提交时会发生权限冲突  加 force**
        **把修改推到远程： git push -f -u origin master**

--mixed	会保留源码，只会将 git commit 和index 回退
        commit和index会回退，work保留

【已经push】`git revert commintId`
git revert 反转提交（工作树必须干净  三个区域的状态、git status干净）用一个新提交来消除一个历史提交所做的任何修改。
revert 本地代码回退，再git push 更新线上代码

## 子模块